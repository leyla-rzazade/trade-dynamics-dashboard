# Fraud Detection Model

A machine learning project for detecting fraudulent financial transactions. The project covers the full pipeline: data preprocessing, comparison of several tree-based models (XGBoost, LightGBM, CatBoost, Random Forest), hyperparameter optimization with Optuna, feature selection via feature importance/SHAP analysis, and finally deploying the best model on a new (unseen) dataset.

## Table of Contents

- [About the Project](#about-the-project)
- [Tech Stack](#tech-stack)
- [Project Structure / Workflow](#project-structure--workflow)
- [Installation](#installation)
- [Usage](#usage)
- [Dataset](#dataset)
- [Results](#results)
- [Notes](#notes)

## About the Project

This notebook demonstrates an end-to-end process for building a fraud detection model on a transaction-level dataset:

1. Data cleaning and feature engineering
2. Training and comparing multiple models with default parameters
3. Hyperparameter optimization using Optuna
4. Comparing default vs. optimized models based on Gini scores
5. Selecting the most meaningful features via feature importance and SHAP analysis
6. Training the final model on the selected features
7. Applying the final model to a new dataset to compute fraud probabilities

## Tech Stack

- Python 3.11
- pandas, numpy
- matplotlib, seaborn
- scikit-learn (train_test_split, LabelEncoder, RandomForestClassifier, metrics)
- xgboost
- lightgbm
- catboost
- optuna (hyperparameter optimization)
- shap (model explainability)

## Project Structure / Workflow

### 1. Pre-Processing
Steps performed on the raw dataset (`fraud_data.xlsx`):
- Drop identifier/non-predictive columns: `branch`, `nameOrig`, `nameDest`
- Convert `Date of transaction` to `datetime` type using `pd.to_datetime(..., dayfirst=True)`
- Derive three new date-based features from it:
  - `transaction_year`
  - `transaction_month`
  - `transaction_day`
- Drop the original `Date of transaction` column after extracting the features above
- Check the dataset for missing values (`data.isnull().sum()`)
- Create two parallel encoded copies of the dataset for different modeling needs:
  - `new_data` — all categorical (object-type) columns encoded with `LabelEncoder` (used for XGBoost, LightGBM, default CatBoost)
  - `data_rf` — a separate label-encoded copy used specifically for the Random Forest model
- Keep the **original (unencoded)** categorical columns for CatBoost's native categorical handling (`inputs_cat`), and fill missing values in `type`, `Acct type`, and `Time of day` with the placeholder `"Missing Value"`
- Split the data into train/test sets (`test_size=0.3`, `random_state=42`), separately for:
  - `X_train / X_test` — label-encoded features (for XGBoost, LightGBM, default CatBoost)
  - `X_train_rf / X_test_rf` — label-encoded features for Random Forest
  - `X_train_cat / X_test_cat` — raw categorical features for CatBoost with native categorical support

### 2. Modeling
Five models are trained and evaluated with **default parameters** as a baseline:

| Model | Input data used | Notes |
|---|---|---|
| **XGBoost** (`XGBClassifier`) | `X_train` / `X_test` (label-encoded) | Baseline gradient boosting model |
| **LightGBM** (`LGBMClassifier`) | `X_train` / `X_test` (label-encoded) | Baseline gradient boosting model |
| **CatBoost** (`CatBoostClassifier`) | `X_train` / `X_test` (label-encoded) | Trained like a standard boosting model, without native categorical handling |
| **CatBoost (Custom)** (`CatBoostClassifier` with `cat_features=['type','Acct type','Time of day']`) | `X_train_cat` / `X_test_cat` (raw categorical) | Uses CatBoost's built-in categorical feature handling instead of label encoding |
| **Random Forest** (`RandomForestClassifier`) | `X_train_rf` / `X_test_rf` (label-encoded) | Bagging-based ensemble baseline |

All five models are trained and scored through a shared `train_and_evaluate_model` function, which for each model computes:
- Predicted probabilities and **Gini coefficient** (`2 * ROC-AUC − 1`) on both train and test sets
- `classification_report` (precision, recall, F1-score per class)
- `confusion_matrix`

Results of all five default models are collected into a single comparison table (`gini_df`), sorted by Test Gini.

### 3. Hyperparameter Optimization with Optuna
Each model is then re-tuned with **Optuna** (Bayesian/TPE-based hyperparameter search), optimizing ROC-AUC via `cross_val_score`:

- **Random Forest** — tunes `n_estimators`, `max_depth`, `min_samples_split`, `min_samples_leaf`, `max_features`, `bootstrap`
- **LightGBM** — tunes `n_estimators`, `learning_rate`, `max_depth`, `num_leaves`
- **XGBoost** — tunes `n_estimators`, `learning_rate`, `max_depth`, `subsample`, `colsample_bytree`, `gamma`
- **CatBoost** — tunes `iterations`, `learning_rate`, `depth`, `l2_leaf_reg`, `loss_function`

For each model, an Optuna `study` searches for the parameter combination that maximizes cross-validated ROC-AUC; the best parameters are then used to refit the final version of that model (`best_rf_model`, `best_lgb_model`, `best_xgb_model`, `best_cb_model`), which is re-evaluated with `train_and_evaluate_model` on the held-out test set.

### 4. Comparing All Results
- The default-parameter results (`gini_df_sorted`) and the Optuna-optimized results (`gini_df_sorted_optuna`) are concatenated into one table (`final_review`)
- A `Gini_gap` column is computed as `abs(Train Gini − Test Gini)` to flag overfitting
- The table is sorted first by **Test Gini (descending)**, then by **Gini_gap (ascending)**, so the top row represents the model with the best and most stable (least overfit) performance
- Based on this comparison, **XGBoost (Optuna-optimized)** is selected as the best-performing model to carry forward

### 5. Feature Importance and SHAP Analysis
- Feature importances from the best model (optimized XGBoost) are extracted, normalized, and visualized as a horizontal bar chart
- Features are filtered to keep only those with **importance greater than 1%**, producing a shortlist of the most predictive variables
- The selected features identified in this step are:
  - `unusuallogin`
  - `oldbalanceOrg`
  - `amount`
  - `newbalanceOrig`
  - `transaction_day`
  - `type`
  - `Acct type`
  - `Time of day`
- `shap.TreeExplainer` is applied to the optimized XGBoost model to compute SHAP values on the training set
- A SHAP summary plot is generated for the selected features to visualize the direction and magnitude of each feature's impact on the fraud prediction

### 6. Building the Final Model
- The dataset is reduced to only the important features identified above (`fin_input` / `fin_output`)
- A new train/test split is performed on this reduced feature set (`X_train_fin`, `X_test_fin`, `y_train_fin`, `y_test_fin`)
- XGBoost is **re-tuned with Optuna** on this smaller feature set (same search space: `n_estimators`, `learning_rate`, `max_depth`, `subsample`, `colsample_bytree`, `gamma`), producing `best_xgb_model_fin`
- The final model is evaluated with `train_and_evaluate_model` and via an **ROC curve**, reporting both ROC-AUC and Gini on the test set
- Acceptance criterion: if the final Gini score exceeds **40%**, the model qualifies to move into production

### 7. Deployment
- A new, unseen dataset (`fraud_deploy_data.xlsx`) is loaded and passed through the **same preprocessing pipeline** as the training data:
  - Drop `branch`, `nameOrig`, `nameDest`
  - Convert `Date of transaction` to `datetime` and derive `transaction_year`, `transaction_month`, `transaction_day`
  - Drop the original date column
- The dataset is reduced to only the final model's input features: `unusuallogin`, `type`, `oldbalanceOrg`, `transaction_day`, `amount`, `newbalanceOrig`
- The categorical `type` column is encoded using the `LabelEncoder` fitted during training
- The final optimized XGBoost model (`best_xgb_model_fin`) predicts a **`probability of fraud`** for every transaction in the deployment set
- Transactions with a `probability of fraud` **above 10%** can be filtered out and flagged for manual review or further action

## Installation

```bash
git clone <repo-url>
cd <repo-folder>
pip install -r requirements.txt
```

Example `requirements.txt` content:

```
pandas
numpy
matplotlib
seaborn
scikit-learn
xgboost
lightgbm
catboost
optuna
shap
openpyxl
```

## Usage

1. Place the `fraud_data.xlsx` file in the project folder (or the path referenced in the notebook).
2. Open the notebook (`Fraud_model_task.ipynb`) in Jupyter/JupyterLab.
3. Run the cells in order:
   - Update the file paths (`pd.read_excel(...)`) to match your local file location.
   - Run the model training and optimization steps (Optuna steps may take time depending on data size).
4. In the deployment stage, use `fraud_deploy_data.xlsx` to generate fraud probabilities for new transactions.

> **Note:** The file paths in the notebook (`C:\Users\TOSHIBA\Downloads\...`) are specific to a local machine. Update these paths before running the notebook in your own environment.

## Dataset

The project uses two datasets:
- `fraud_data.xlsx` — historical transaction data used for training and testing the model
- `fraud_deploy_data.xlsx` — new transaction data on which the trained model is applied

Expected columns include: `type`, `amount`, `oldbalanceOrg`, `newbalanceOrig`, `unusuallogin`, `Acct type`, `Time of day`, `Date of transaction`, `isFraud` (target variable).

## Results

- **10 models in total** are compared across the pipeline: 5 baseline models with default parameters + 5 Optuna-optimized models (Random Forest, LightGBM, XGBoost, CatBoost, and CatBoost with native categorical features).
- Models are ranked using the **Gini score** (`2 * ROC-AUC − 1`) on the test set, with the Train-Test Gini gap used as a secondary check against overfitting.
- **XGBoost (Optuna-optimized)** emerges as the best-performing and most stable model.
- The feature set is then reduced from the full set of encoded columns down to **8 key features** (`unusuallogin`, `oldbalanceOrg`, `amount`, `newbalanceOrig`, `transaction_day`, `type`, `Acct type`, `Time of day`) based on feature importance (>1%) and confirmed with SHAP analysis.
- XGBoost is retrained and re-optimized on this reduced feature set, then evaluated with an ROC curve and final Gini score.
- The final model is designed to move to production if its Gini score exceeds a **40% threshold**.
- In deployment, the final model scores new transactions with a `probability of fraud`, and transactions above a **10% probability threshold** are flagged for review.

## Notes

- Some cells in the code contain instructional comments (in Azerbaijani), indicating that the project was prepared as part of a task/assignment.
- During deployment, `LabelEncoder` is applied again with `fit_transform`; for production consistency, it is recommended to use `transform` (not `fit`) with the encoder that was fitted during training.

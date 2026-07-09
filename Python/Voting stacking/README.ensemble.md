# Ensemble Models: Voting & Stacking (Telecom Churn)

A project for predicting customer churn, building several machine learning models, tuning them with Optuna, performing feature selection, and combining the best models using **Voting (Hard/Soft)** and **Stacking** ensemble methods.

## 📋 About the project

The goal of this notebook is to train several classification models of different nature (linear, distance-based, tree-based, boosting) separately, evaluate each one's performance using the Gini coefficient, identify the most predictive features, and finally combine the best-performing models with **ensemble** techniques to achieve a more stable and stronger result.

## 🗂 Data

- Input file: `telecom_churn.xlsx`
- Target variable: `Churn` (converted from `Yes`/`No` to `1`/`0`)
- The `customerID` column is dropped before analysis, as it carries no predictive value.

## 🔧 Model-specific preprocessing

Each algorithm gets its own tailored data preparation:

| Model | Missing values | Additional steps |
|---|---|---|
| **Logistic Regression** | Filled with mean (`tenure`, `TotalCharges`) / mode (others) | WoE (Weight of Evidence) encoding, correlation with target (10% threshold), removal of intercorrelated variables (70% threshold) |
| **KNN** | Filled with mean/mode | Outlier treatment (IQR), correlation analysis (10% threshold), VIF, Label Encoding, StandardScaler |
| **Random Forest** | Filled with mean/mode | Label Encoding |
| **XGBoost** | — | Label Encoding |
| **LightGBM** | — | Label Encoding |
| **CatBoost** | — | Label Encoding |
| **CatBoost (categorical)** | Categorical columns filled with `"Missing Value"` | Raw categorical variables (`cat_features`) passed directly to the model |

## 🤖 Trained models

- Logistic Regression
- K-Nearest Neighbors (KNN)
- Random Forest
- XGBoost
- LightGBM
- CatBoost (default)
- CatBoost (custom, with `cat_features`)

For each model, **ROC-AUC** and the derived **Gini** (`2*AUC - 1`) are calculated for both train and test sets, along with a `classification_report` and `confusion_matrix`.

## 🎯 Hyperparameter optimization (Optuna)

**Optuna** is used to tune hyperparameters for KNN, Random Forest, XGBoost, LightGBM, and CatBoost, based on `cross_val_score` (ROC-AUC). The optimized models are compared against the baseline models and ranked by the **Train/Test Gini gap**.

## 🔍 Univariate feature selection (Logistic Regression)

Each WoE-encoded variable is tested individually as a single-feature Logistic Regression model. Variables with **Test Gini > 0.10** and a **Train/Test Gini gap ≤ 0.05** are kept, and the Logistic Regression model is retrained using only this reduced, stable set of features.

## 🧩 Ensemble methods

### 1. Voting Classifier — Hard
Final predictions are decided by majority vote among the class predictions of the base models (`LR`, `RF`, `XGBoost`, `CatBoost`, `LightGBM`).

### 2. Voting Classifier — Soft
The predicted **probabilities** of the base models are averaged to produce the final decision.

### 3. Stacking Classifier
Predictions from the base models (`RF`, `XGBoost`, `CatBoost`, `LightGBM`) are combined by a meta-model (Logistic Regression), using `passthrough=True` and `stack_method='predict_proba'`, with 5-fold cross-validation.

## 📊 Evaluation metric

The main comparison metric across all models is the **Gini coefficient**:

```
Gini = 2 * ROC_AUC - 1
```

Results are consolidated in the `final_review_sorted` table, ranked by Train/Test Gini and the gap between them (an indicator of overfitting).

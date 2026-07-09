# Ensemble Models: Voting & Stacking

A project for predicting loan default risk, building several machine learning models, tuning them with Optuna, and combining them using **Voting (Hard/Soft)** and **Stacking** ensemble methods.

## 📋 About the project

The goal of this notebook is to train several classification models of different nature (linear, distance-based, tree-based, boosting) separately, evaluate each one's performance using the Gini coefficient, and finally combine them with **ensemble** techniques to achieve a more stable and stronger result.

## 🗂 Data

- Input file: `data_main.xlsx`
- Target variable: `target` (binary — default/non-default)
- Columns considered redundant or highly correlated (`Contracts`, `90_dpd_crd_max`, `app_phone_amount_06_count`) are dropped before analysis.

## 🔧 Model-specific preprocessing

Each algorithm gets its own tailored data preparation:

| Model | Missing values | Additional steps |
|---|---|---|
| **Logistic Regression** | Filled with mean/mode | WoE (Weight of Evidence) encoding, correlation with target, removal of highly intercorrelated variables |
| **KNN** | Filled with mean/mode | Outlier treatment (IQR), correlation analysis, VIF, Label Encoding, StandardScaler |
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

## 🧩 Ensemble methods

### 1. Voting Classifier — Hard
Final predictions are decided by majority vote among the class predictions of the base models (`LR`, `RF`, `XGBoost`, `CatBoost`, `LightGBM`).

### 2. Voting Classifier — Soft
The predicted **probabilities** of the base models are averaged to produce the final decision (e.g., `(0.1+0.6+0.3)/3` vs `(0.9+0.4+0.7)/3`).

### 3. Stacking Classifier
Predictions from the base models (`RF`, `XGBoost`, `CatBoost`, `LightGBM`) are combined by a meta-model (Logistic Regression), using `passthrough=True` and `stack_method='predict_proba'`, with 5-fold cross-validation.

## 📊 Evaluation metric

The main comparison metric across all models is the **Gini coefficient**:

```
Gini = 2 * ROC_AUC - 1
```

Results are consolidated in the `final_review_sorted` table, ranked by Train/Test Gini and the gap between them (an indicator of overfitting).

## 📁 Structure

```
.
├── Ensemblemodels__Voting_and_Stacking____6_.ipynb
├── README.md
└── data_main.xlsx   (to be added in your own environment)
```

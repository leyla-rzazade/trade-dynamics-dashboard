# Credit Scoring — Probability of Default (PD) Model

## Project Overview
This project builds a **Probability of Default (PD)** model using **Logistic Regression** within a credit risk / credit scoring framework. The target is a binary variable derived from a customer's credit score category, and the model estimates the probability that a customer will default. The pipeline follows a classic credit scoring approach: preprocessing → Weight of Evidence (WoE) transformation → correlation-based feature selection → logistic regression → Gini/AUC evaluation → deployment on new data.

## Dataset
- **Training file:** `credit_score.csv`
- **Deployment file (WoE-ready):** `prod_data_with_woe.xlsx`
- **Deployment file (raw values, homework):** `test_data_real_values.xlsx`
- **Target variable:** `score` — derived from `CreditScore`:
  - `CreditScore == 'Poor'` → `1` (default)
  - otherwise (`Good` / `Standard`) → `0` (non-default)
- **Features:** Numeric (Age, AnnualIncome, MonthlyInhandSalary, NumBankAccounts, NumCreditCard, InterestRate, NumofLoan, Delayfromduedate, NumofDelayedPayment, ChangedCreditLimit, NumCreditInquiries, OutstandingDebt, CreditUtilizationRatio, TotalEMIpermonth, Amountinvestedmonthly, MonthlyBalance) and categorical (Month, Occupation, PaymentofMinAmount, PaymentBehaviour)

> Note: file paths in the notebook are hardcoded to a local machine (`C:\Users\...\Downloads\...`). Update to relative paths before running elsewhere.

## Project Workflow

1. **Pre-Processing** — dropped irrelevant identifier columns, built the binary target from `CreditScore`, and handled missing values (mean for numeric, mode for categorical)
2. **Weight of Evidence (WoE) Transformation** — numeric variables binned into quartile-based buckets, categorical variables used directly; WoE computed for every feature and merged back as `*_woe` columns
3. **Distribution Check** — Kolmogorov–Smirnov test on numeric columns
4. **Correlation-Based Feature Selection** — kept WoE features with meaningful correlation to the target (Spearman), and checked for redundant highly-correlated feature pairs
5. **Modeling** — 70/30 train/test split, Logistic Regression trained on the selected WoE features
6. **Evaluation** — Gini coefficient, Precision, Recall, Confusion Matrix, and ROC curve computed for train and test sets
7. **Univariate Analysis** — each WoE feature tested individually to measure its standalone predictive power (Gini)
8. **Refined Model** — a second Logistic Regression trained on only the strongest variables from the univariate analysis
9. **Deployment (WoE-ready data)** — scored a set of customers whose features were already in WoE form
10. **Deployment (raw-value data, homework)** — applied the training-derived WoE mapping to new raw customer data, then scored it with the model

## Model
- **Algorithm:** Logistic Regression
- **Library:** Scikit-learn

## Model Evaluation Metrics
- Gini Coefficient (`2*AUC - 1`)
- Precision
- Recall
- Confusion Matrix
- ROC Curve / AUC

## Technologies Used
- Python
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Scikit-learn
- SciPy (Kolmogorov–Smirnov test)

## How to Run
```bash
pip install -r requirements.txt
jupyter notebook Logistic_regression_praktiki_tapshiriq.ipynb
```
Update the hardcoded file paths in the notebook to point to your local copies of `credit_score.csv`, `prod_data_with_woe.xlsx`, and `test_data_real_values.xlsx` before running.

## Skills Demonstrated
- Credit Scoring / PD Modeling
- Weight of Evidence (WoE) Transformation
- Correlation-Based Feature Selection
- Logistic Regression
- Gini / AUC / ROC Evaluation
- Univariate Predictive Power Analysis
- Model Deployment on WoE and Raw-Value Data

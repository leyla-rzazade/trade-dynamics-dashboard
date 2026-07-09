# LGD Prediction — Credit Risk Modeling

## Project Overview
This project focuses on predicting **Loss Given Default (LGD)** within a credit risk modeling framework. The objective is to estimate the expected loss percentage when a borrower defaults. The model was developed using a structured regression approach, including data preprocessing, feature engineering, statistical validation, and a deployment step for scoring new data.

## Dataset
- **Training file:** `lgd_data.xlsx`
- **Deployment/scoring file:** `lgd_data_deploy.xlsx`
- **Target variable:** `LGD%`
- **Features:** A mix of numeric variables (Exposure Amount, Loan Term, Income, Debt to Income Ratio, Employment History, Previous Defaults, Credit Score) and categorical variables (Home Ownership, Loan Purpose, Loan Type, Region)

> Note: file paths in the notebook are currently hardcoded to a local machine (`C:\Users\...\Downloads\...`). Update these paths to relative paths (e.g. `data/lgd_data.xlsx`) before running on another machine.

## Project Workflow
1. **Data loading & inspection** — `describe()`, missing value check (`isnull().sum()`)
2. **Outlier treatment** — IQR-based capping (winsorization) of numeric columns, visualized with boxplots
3. **Feature engineering** — derived features:
   - `Income_mean_by_Region` (mean income grouped by region)
   - `Previous_Defaults_mean_by_Credit_Score` (mean previous defaults grouped by credit score)
4. **Normality check** — Kolmogorov–Smirnov test on numeric columns
5. **Exploratory Data Analysis (EDA)** — Spearman correlation analysis
   - Correlation of each feature with the target (`target_correlation`)
   - Inter-feature correlation to flag redundant variables (`intercorrelation`)
6. **Multicollinearity check** — Variance Inflation Factor (VIF)
7. **Column selection** — reducing the dataframe to the relevant feature set
8. **Encoding** — one-hot encoding of categorical variables (`pd.get_dummies`)
9. **Scaling** — feature standardization with `StandardScaler`
10. **Train/Test split** — 80/20 split (`random_state=42`)
11. **Model training** — Linear Regression (full feature set)
12. **Single-variable comparison** — R² per individual feature to assess standalone predictive power
13. **Model refinement** — retraining on a reduced, selected feature subset (Exposure Amount, Income, Debt to Income Ratio, Loan Term, Previous Defaults, Employment History)
14. **Model evaluation** — metrics on train and test sets
15. **Deployment** — loading new/unseen data, applying the same feature engineering, capping, and scaling pipeline, and generating `predicted_lgd` scores

## Model
- **Algorithm:** Linear Regression
- **Library:** Scikit-learn

## Model Evaluation Metrics
- R² Score (and Adjusted R²)
- Mean Absolute Error (MAE)
- Root Mean Squared Error (RMSE)

Metrics are computed separately for the full-feature model and the reduced-feature model, on both train and test sets.

## Technologies Used
- Python
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Scikit-learn
- SciPy (Kolmogorov–Smirnov test)
- Statsmodels (VIF)

## How to Run
```bash
pip install -r requirements.txt
jupyter notebook LGD_prediction.ipynb
```
Update the hardcoded file paths in the notebook to point to your local copies of `lgd_data.xlsx` and `lgd_data_deploy.xlsx` before running.

## Skills Demonstrated
- Credit Risk Analytics
- Feature Engineering
- Regression Modeling
- Multicollinearity Handling (VIF)
- Outlier Treatment
- Model Evaluation
- Model Deployment / Scoring Pipeline
- Data Visualization

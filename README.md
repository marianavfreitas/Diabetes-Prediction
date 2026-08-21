# Diabetes Predictive Model

Machine learning pipeline predicting diabetes onset from diagnostic measurements, with an interactive Power BI dashboard for exploring model performance, feature drivers, and individual patient risk.

## Overview

This project builds and compares five classification models to predict diabetes onset using the Pima Indians Diabetes Dataset. Beyond model training, the project includes SHAP-based interpretability analysis and an interactive Power BI dashboard that lets users explore model performance, the strongest predictive features, and risk profiles for individual patients by adjusting age, BMI, and glucose ranges.

## Dataset

- **Source:** Pima Indians Diabetes Dataset
- **Size:** 768 patients, 8 diagnostic features, 1 binary target (`outcome`)
- **Data quality note:** Several features (glucose, blood pressure, skin thickness, insulin, BMI) contain biologically impossible zero values, which were treated as missing data rather than true zeros.

## Methodology

1. **Data Cleaning** — Zeros converted to `NaN` for biologically implausible values.
2. **Imputation** — Median imputation (glucose, blood pressure, BMI), KNN imputation (skin thickness), missing-value flag + median fill (insulin).
3. **Outlier Treatment** — IQR-based capping (pregnancies, age, and diabetes pedigree function excluded, as their spread is clinically meaningful).
4. **Feature Selection** — Triangulated across RFE, Lasso, Random Forest importances, and SHAP values.
5. **Modeling** — Logistic Regression, Random Forest, XGBoost, LightGBM, and SVM, each tuned via grid search.
6. **Evaluation** — Accuracy, Precision, Recall, F1, and ROC-AUC, with particular attention to **Recall**: in a medical screening context, a missed diabetic patient (false negative) is costlier than a false alarm.

## Results (Validation Set)

| Model | Accuracy | Precision | Recall | F1 Score | ROC-AUC |
|---|---|---|---|---|---|
| **Random Forest** | 0.762 | 0.651 | **0.691** | **0.671** | 0.849 |
| LightGBM | 0.775 | 0.710 | 0.605 | 0.653 | 0.839 |
| XGBoost | 0.771 | 0.700 | 0.605 | 0.649 | 0.841 |
| Logistic Regression | 0.766 | 0.721 | 0.543 | 0.620 | **0.867** |
| SVM | 0.766 | 0.721 | 0.543 | 0.620 | 0.868 |

**Random Forest** was selected as the primary model, offering the best Recall and F1 Score on the validation set — the most important trade-off for a screening use case where missing a diabetic patient is the costlier error. Logistic Regression and SVM achieve the highest ROC-AUC, but at a meaningfully lower Recall.

## Key Findings

- **Glucose, age, and BMI** are the strongest predictors of diabetes onset, confirmed independently by both Random Forest feature importance and SHAP values — a reassuring consistency check between two different interpretability methods.
- An engineered **"insulin missing" flag** (marking patients with missing insulin readings) carries more predictive signal than the raw insulin value itself, suggesting the *pattern* of missingness is informative, not just noise to be imputed away.
- Model performance clusters closely together (Recall 0.54–0.69), suggesting the ceiling on this dataset may be close to being reached with the available features — further gains would likely require additional clinical variables rather than more modeling effort.

## Dashboard

An interactive Power BI dashboard supports exploration of:
- Model performance comparison across all 5 models
- Top predictive features
- Distributions of key variables (glucose, BMI, age) split by outcome
- A patient-level risk explorer with adjustable age/BMI/glucose filters

[View the full dashboard (PDF)](dashboard/diabetes_dashboard.pdf)

## Repository Structure

```
├── Diabetes_Predictive_Model.ipynb   # Full analysis: cleaning, modeling, SHAP, exports
├── diabetes.csv                       # Raw dataset
├── dashboard/
│   ├── diabetesBI.pbix               # Power BI dashboard file
│   └── dashboard_overview.png        # Dashboard screenshot
└── report/
    └── report.pdf                    # Academic write-up (LaTeX)
```

## Tools Used

Python (pandas, scikit-learn, XGBoost, LightGBM, SHAP) · Power BI · LaTeX (Overleaf)

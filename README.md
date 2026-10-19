# Customer Churn Analysis and Prediction

A reproducible Python project exploring customer churn in a telecommunications dataset. The project combines exploratory data analysis, feature engineering, and supervised classification to examine which customer characteristics are associated with churn and how well several baseline models identify customers who leave.

> **Project status:** Portfolio-ready first version, consolidated from exploratory notebooks. Results below are generated from the included Telco dataset and the cleaned notebook—not copied from the original notebook outputs.

## Research question

Can customer account, service, contract, and billing information help identify customers who are likely to churn? Which features appear useful, and what trade-offs do different classification models make when churn is the positive class?

## Dataset and source notes

The analysis uses [`data/telco_customer_churn.csv`](data/telco_customer_churn.csv), the supplied Telco Customer Churn CSV. It contains **7,043 customer records and 21 columns**, including tenure, subscribed services, contract type, payment method, monthly/total charges, and the `Churn` outcome. The customer ID is an identifier, not a predictive feature.

Three data files were attached during project review. They are **not interchangeable**:

- **Telco Customer Churn** (`WA_Fn-UseC_-Telco-Customer-Churn.csv`): selected for the predictive modeling notebook; target is `Churn`.
- **Online retail customer churn** (`online_retail_customer_churn.csv`): 1,000 rows with a different schema and `Target_Churn`; the original retail notebook expected a different file named `retail_data.csv`, so its code cannot run against this attached file unchanged.
- **E-commerce user churn** (`ecom-user-churn-data.csv`): 49,358 rows with behavioral/session features and `target_class`; it is a separate dataset and is not used in the current model comparison.

The Telco file matches the commonly distributed **IBM Sample Data Set / Kaggle Telco Customer Churn** schema. A commonly cited dataset page is [Kaggle: Telco Customer Churn](https://www.kaggle.com/datasets/blastchar/telco-customer-churn). Because the supplied CSV did not contain provenance metadata, confirm that this is the exact upstream source and review its current license/redistribution terms before committing the raw file publicly. Do not imply that the retail or e-commerce datasets were analyzed in the reported model results.

## Workflow

1. **Load and inspect:** review dataset dimensions, columns, missing values, target balance, and data types.
2. **Clean and prepare:** normalize column names, coerce `TotalCharges` to numeric, remove rows where it cannot be parsed, and separate the target from the predictors.
3. **Feature engineering:** create interpretable indicators such as active-service count, average historical charge per month of tenure, protection-service coverage, low technology-service use, and new/long-tenure customer flags.
4. **Exploratory analysis:** inspect churn rates and distributions by contract, tenure, payment method, and service use; review numeric correlations as descriptive evidence only.
5. **Modeling:** compare Logistic Regression, Decision Tree, and Random Forest using a stratified train/test split and a preprocessing pipeline. Categorical encoding and numeric imputation are fitted on training data through scikit-learn pipelines.
6. **Evaluation:** report precision, recall, F1, ROC-AUC, and a confusion matrix. F1 is included because missing likely churners and generating false alarms have different business costs.
7. **Interpretation and limitations:** discuss patterns as associations, not causes, and identify validation steps before any business use.

## Results from the reproducible baseline

The supplied file has **7,043 rows**; 11 rows have blank `TotalCharges` values that become missing during numeric conversion, leaving **7,032 rows** for analysis. The observed churn rate after cleaning is about **26.6%**.

Descriptive patterns in this sample:

- Churn was **42.7%** among month-to-month customers, compared with **11.3%** on one-year contracts and **2.8%** on two-year contracts.
- Churn was **45.3%** among customers using electronic checks; it ranged from **15.3% to 16.7%** for the two automatic-payment categories.
- Median tenure was **10 months** for churners and **38 months** for non-churners.
- Median monthly charges were **79.65** for churners and **64.45** for non-churners.

On a stratified 80/20 split (random seed 42), the baseline model comparison produced:

| Model | Accuracy | Precision | Recall | F1 | ROC-AUC |
|---|---:|---:|---:|---:|---:|
| Random Forest | 0.756 | 0.529 | 0.765 | 0.625 | 0.834 |
| Decision Tree | 0.744 | 0.512 | 0.775 | 0.617 | 0.820 |
| Logistic Regression | 0.729 | 0.494 | 0.807 | 0.613 | 0.838 |

Random Forest had the highest F1 in this baseline comparison, while Logistic Regression had the highest recall and ROC-AUC. This is a trade-off, not a universal winner: model choice depends on whether the business values finding more churners or limiting false alarms. Performance is not production-ready evidence; this is one held-out split on one dataset, not external or temporal validation.

Run the notebook to refresh figures and metrics after any code or data changes.

## Quick start

Python 3.10+ is recommended.

```bash
git clone <your-repository-url>
cd customer-churn-analysis
python -m venv .venv
source .venv/bin/activate       # macOS/Linux
# .venv\Scripts\activate    # Windows
python -m pip install -r requirements.txt
jupyter notebook notebooks/customer_churn_analysis.ipynb
```

Run notebook cells from top to bottom. The notebook reads the included CSV using a path relative to the repository, so it does not depend on Google Drive or a local absolute path.

## Repository structure

```text
customer-churn-analysis/
├── data/
│   ├── telco_customer_churn.csv
│   └── README.md
├── docs/
│   └── original_code_review.md
├── notebooks/
│   └── customer_churn_analysis.ipynb
├── .gitignore
├── README.md
└── requirements.txt
```

## Key limitations

- Churn is imbalanced, so accuracy alone can hide poor detection of churners.
- Correlation does not establish causation and correlated features can make coefficients or feature importance unstable.
- Feature definitions such as “new customer” (<3 months) and “loyal customer” (≥24 months) are analyst-defined thresholds, not validated business rules.
- `TotalCharges / tenure` approximates average historical monthly charges; it is not a forward-looking lifetime value estimate.
- The original notebook tuned some models against the test set in loops. The cleaned notebook avoids choosing hyperparameters on the test set; further tuning should use cross-validation on training data only.
- Model explanations and retention recommendations need domain review, fairness checks, and prospective validation before operational use.

## Next improvements

- Verify and cite the exact dataset source/license.
- Add cross-validated hyperparameter tuning with a held-out final test set.
- Calibrate predicted probabilities and choose a threshold using explicit retention campaign costs.
- Add temporal or external validation, and compare results against a simple baseline.
- If estimating customer lifetime value, define the value target and methodology separately; this notebook predicts churn, not CLV.

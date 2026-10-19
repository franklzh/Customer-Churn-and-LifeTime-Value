# Review of the original notebooks

## What the original work explored

The supplied notebooks contained two separate strands:

1. **Retail/customer-behavior exploration** (`Research.ipynb`): descriptive statistics, days since last purchase, categorical and numeric distributions, a correlation heatmap, one-hot encoding, and an additional Telco heatmap. It expects a file called `retail_data.csv` with columns such as `customer_id`, `transaction_date`, `income_bracket`, and `total_sales`.
2. **Telco churn modeling** (`Reseach_2.ipynb`): cleaning the 7,043-row Telco dataset, binary encoding and one-hot encoding, derived features, distribution plots, logistic regression, decision trees, random forests, SHAP-based importance, ROC/precision-recall plots, and a KMeans clustering pipeline.

These are different datasets and should not be described as one continuous analysis. The attached `online_retail_customer_churn.csv` has 1,000 rows and 15 different columns; it is not the `retail_data.csv` expected by the original retail notebook. The attached `ecom-user-churn-data.csv` is another distinct dataset with `target_class`. The published notebook therefore focuses on the Telco data, where the source code and target are aligned.

## Important code issues corrected or documented

- **Hard-coded paths:** original notebook paths pointed to Google Drive or `/WA_Fn-UseC_-Telco-Customer-Churn.csv`; the new notebook uses a repository-relative path.
- **Repeated preprocessing:** the original code reloads and transforms the data in multiple cells, making the final state depend on execution order. The new notebook has one clear preprocessing workflow.
- **Potentially stale outputs:** original model outputs include small test-set metrics and inconsistent results across sections. They should not be reported as validated headline results without a clean, top-to-bottom rerun. The new notebook regenerates metrics from the included 7,043-row file.
- **Test-set tuning:** original loops repeatedly choose the model based on test-set F1, which leaks test-set information into model selection. The new baseline comparison does not optimize on the test set.
- **Preprocessing leakage risk:** the original code standardizes some variables before splitting, and manually bins variables before dropping interval columns. The new notebook places preprocessing inside scikit-learn pipelines, so imputers, encoders, and scalers are fit on training data only.
- **Encoding ambiguity:** replacing `No internet service` and `No phone service` with 0 makes the columns usable, but it conflates “service unavailable/not applicable” with “service explicitly declined.” The notebook documents this simplifying assumption.
- **Feature engineering caveats:** `avgmonthlyspend = TotalCharges / tenure` is a historical average, not lifetime value. `autosubscription` must be derived from the original payment method, not inferred from `idxmax()` over a one-hot subset because the omitted reference category can be misclassified.
- **KMeans result not interpreted:** the original code fits three clusters but does not evaluate cluster quality or explain the resulting segments. It is omitted from the primary workflow until a meaningful segmentation analysis is added.
- **SHAP implementation sensitivity:** SHAP output shapes vary by estimator/version, so feature-importance code should be validated against the installed SHAP version before it is relied on. The initial public version keeps the core model comparison dependency-light.
- **Claims about lifetime value:** the resume describes “Customer Churn and Lifetime Value Model,” but the supplied code predicts churn and engineers spend proxies; it does not define or validate a lifetime-value target. Either add a separate CLV analysis or describe this public repository as a churn prediction project.

## How to read each notebook section

1. **Question and data:** defines churn as the binary target and identifies the Telco file as the only dataset used for the reported models.
2. **Data quality:** checks shape, data types, missing values, and churn prevalence. The original CSV has 7,043 rows; `TotalCharges` contains blank strings that need numeric coercion and become missing values.
3. **Feature engineering:** builds service counts and interpretable customer indicators. These are exploratory definitions rather than established churn drivers.
4. **Exploratory analysis:** compares observed churn rates across groups. A difference in rates is descriptive association, not proof that a contract or service causes retention.
5. **Preprocessing:** separates numerical and categorical features and performs encoding/imputation inside a pipeline.
6. **Model comparison:** compares Logistic Regression (linear baseline), Decision Tree (nonlinear rules), and Random Forest (ensemble of trees).
7. **Evaluation:** precision asks how many flagged customers actually churned; recall asks how many churners were found; F1 balances precision and recall; ROC-AUC summarizes ranking ability across thresholds.
8. **Conclusion:** selects no “best production model” from a single split. Business threshold selection requires campaign capacity and costs for false positives/false negatives.

## Publication checklist

- [ ] Confirm source URL and dataset license before committing data publicly.
- [ ] Run notebook from a fresh kernel in order and inspect every output.
- [ ] Ensure plots have titles, axis labels, and readable labels.
- [ ] Add final findings only after reviewing actual generated plots and metrics.
- [ ] Add a project date, author details if desired, and a code license once the data license is settled.
- [ ] Keep personal resume/contact information out of this repository unless intentionally desired.

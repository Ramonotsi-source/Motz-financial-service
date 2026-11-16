# Model Creation and Implementation Record

## 1. Data source and scope

The application reads `data/train.csv`, the user-supplied Kaggle loan-prediction training file. It contains 614 labelled applications and 13 columns. `Loan_ID` is an identifier, not a predictor. `Loan_Status` is the target: `Y` means historically approved and `N` means historically rejected. No lecturer-provided out-of-sample data is used.

## 2. Data checks and cleaning

At load time, the Prolog source validates the expected CSV header, normalizes categorical values to lowercase, and converts blank cells to the explicit atom `missing`. Numeric fields are parsed as numbers where present. This is an explicit missing-value strategy; it is **not** median/mode imputation. It preserves the fact that a value was missing without pretending an unknown value is known. The dashboard and `outputs/dataset_audit.json` report actual missing counts.

The CSV has 149 missing cells: Gender 13, Married 3, Dependents 15, Self_Employed 32, LoanAmount 22, Loan_Amount_Term 14, Credit_History 50. There are no exact duplicate rows and no repeated `Loan_ID` values in the supplied file.

### Outlier analysis

The raw data show long right tails in income fields: ApplicantIncome ranges from 150 to 81,000 and CoapplicantIncome ranges from 0 to 41,667. LoanAmount ranges from 9 to 700. These extremes warrant investigation. The current Prolog baseline does not delete or winsorize records; it converts numeric inputs into broad, fixed bins, reducing sensitivity to exact extreme magnitudes. This is a modelling choice, not proof that the values are errors. A future refinement should compare quantile-based binning or winsorization using training-fold statistics only.

## 3. Feature representation

The classifier uses 11 predictors: Gender, Married, Dependents, Education, Self_Employed, ApplicantIncome, CoapplicantIncome, LoanAmount, Loan_Amount_Term, Credit_History, and Property_Area. `Loan_ID` is excluded. The target is not used as an input feature.

The current fixed numeric bins are:

- Applicant income: `<2500`, `2500–5999`, `6000–11999`, `12000+`
- Co-applicant income: `0`, `<2000`, `2000–4999`, `5000+`
- Loan amount: `<100`, `100–199`, `200–349`, `350+`
- Loan term: `<180`, `180–360`, `>360` months

Missing numeric values map to the `missing` bin. Categorical values use their normalized category or `missing`. The same transformations are used for the model's training records, holdout records, and interactive predictions.

## 4. Algorithm

The classifier is a categorical Naive Bayes baseline implemented in SWI-Prolog. For each class, it combines a smoothed prior probability with smoothed conditional feature probabilities. Scores are accumulated in log space to avoid numerical underflow. The class with the larger score is returned.

The model uses Laplace-style additive smoothing. Its core assumption is conditional independence of features given the class. In loan applications, income, loan amount, employment, and other factors may be dependent, so this assumption is a limitation. The returned score is relative and is not a calibrated approval probability.

## 5. Training and internal evaluation

The program uses a deterministic ordered 80/20 holdout: records whose one-based row index is divisible by five form the test set; the remaining records form the training set. The model is trained only on the training partition, and each held-out row is classified without its label being used as a feature. The evaluation routine counts TP, TN, FP and FN from those predictions.

This split is reproducible but is not randomized or stratified. Since the source file has an order, its results may differ from results produced by a shuffled, stratified split or cross-validation. For a stronger final study, compare stratified cross-validation and tune bin thresholds within training folds only. Do not use the lecturer's external test dataset for model selection.

The server calculates the following measures at startup. Record the values returned by `/api/performance` during a real SWI-Prolog run; do not manually fill in guessed values:

- Accuracy
- Precision (approved is the positive class)
- Recall / sensitivity
- Specificity
- F1 score
- Confusion matrix

## 6. Reproducibility and verification

1. Install SWI-Prolog.
2. From the project root, run `swipl -q -s main.pl`.
3. Open `http://localhost:8080`.
4. Confirm the dashboard loads and compare its dataset count with 614.
5. Submit a sample applicant in the form and confirm a prediction response.
6. Open the model-performance section and record the returned values and confusion matrix.
7. Check `/api/data_quality` and compare its missing-value counts with the CSV audit.
8. Capture screenshots only after successful live execution.

The build environment used to prepare this package did not have SWI-Prolog installed, so a live runtime test could not be completed here. The project should not be described as fully runtime-verified until the above checks pass on the target machine.

## 7. Ethical and operational limits

This prototype predicts patterns in a small public benchmark. It does not constitute financial advice or a credit decision. A real lending system would require representative data, calibrated risk estimates, robust validation, explainability, privacy and security controls, bias/fairness analysis, human oversight, and legal/compliance review. Historical outcomes may encode structural bias. Missing credit history must not be treated as negative credit history.

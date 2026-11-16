# Report guide — follow the lecturer's marking requirements

Use Arial Narrow, 12 pt, IEEE numbered citations. The brief contains two different length instructions (at least 11 pages excluding references, and 8–10 pages excluding references). Confirm the expected length with the lecturer; the safer interpretation is to satisfy the stricter minimum of 11 pages if accepted.

Suggested structure:
1. Abstract — one paragraph.
2. Introduction — problem statement, importance, objectives, scope (about one page).
3. Data gathering and cleaning — Kaggle source, schema, data types, missing-value counts, duplicate checks, outlier analysis and descriptive statistics.
4. Preprocessing — explain categorical handling, numerical bins, target encoding, feature selection and any changes. Clearly disclose that current code treats missing values as a category; do not claim imputation.
5. Model determination — explain Naive Bayes, its conditional-independence assumption, why it is a suitable baseline, and its limitations.
6. Model training — dataset split, training procedure and leakage safeguards.
7. Model tuning — document each actual experiment, settings, measured metrics and reason for retaining/changing a setting. Do not invent experiments.
8. Dashboard — screenshots of the running application and explanation of each page/visualisation.
9. Evaluation — confusion matrix, accuracy, precision, recall, specificity and F1 from the live Prolog output.
10. Conclusion — one paragraph.
11. References — at least 21 IEEE-style references to dataset documentation, Prolog, ML and evaluation sources.

The assignment requires at least four distinct figures in the presentation. The dashboard currently supplies three charts; add a fourth data-backed figure (for example, income distribution or missing-value counts) only after calculating it from the actual CSV. Keep the lecturer's separately supplied out-of-sample dataset out of training and tuning until presentation day.

Group responsibilities: add the real names and agreed responsibilities of all three members; do not invent them.

# CHANGELOG

All significant improvements from the original notebook to this production version.

---

## [2.0.0] — Refactored Production Version

### 🔴 Critical Fixes (would have caused wrong conclusions)

- **[DATA LEAKAGE — SCALING]** Original: `X_test` was standardised using its own mean/std. Fixed: `StandardScaler` is now inside a `Pipeline`; it is `fit` only on `X_train` and `transform`-only on `X_test`. This eliminates test-set leakage and produces valid holdout metrics.

- **[DATA LEAKAGE — IMPUTATION]** Original: Medians for WBC/RBC were computed on the full dataset before the train/test split. Fixed: `SimpleImputer` is inside the Pipeline, fitted on training data only.

- **[STATISTICAL INTERPRETATION]** Original: "Fail to reject H₀" was described as confirming the drug is safe. Fixed: Correct interpretation added — failing to reject H₀ does not confirm H₀ is true. Absence of evidence ≠ evidence of absence.

- **[TARGET LEAKAGE]** Original: `num_effects` (count of adverse effects) could have been included as a model feature. Fixed: Explicitly excluded from features as it is derived from the target variable.

- **[ML MODEL vs. BUSINESS QUESTION]** Original: The model excluded `trx` (the primary variable of interest in an RCT) without justification. Fixed: All relevant features including `trx` are included; exclusions are explicitly justified.

---

### 🟠 Major Improvements

- **[MULTIPLE COMPARISONS]** Added Benjamini-Hochberg FDR correction across all 5 hypothesis tests. Original ran 5 tests at α=0.05 with family-wise error rate ≈23% — no correction applied.

- **[EFFECT SIZES]** Added Cohen's d (t-tests), rank-biserial r (Mann-Whitney U), and Cramér's V (Chi-Square) to every test. p-values alone are insufficient — effect size determines clinical meaningfulness.

- **[NORMALITY CHECKING]** Added Shapiro-Wilk tests and Q-Q plots before selecting parametric vs. non-parametric tests. Original applied t-tests without checking the normality assumption.

- **[NON-PARAMETRIC FALLBACK]** Added Mann-Whitney U test as automatic fallback when normality is violated.

- **[CLASS IMBALANCE]** Added class distribution check and `class_weight='balanced'` to logistic regression. Original never checked whether the target was imbalanced.

- **[CROSS-VALIDATION]** Added stratified 5-fold cross-validation. Original used a single train/test split which gives unreliable performance estimates.

- **[PIPELINE]** Replaced manual preprocessing with `sklearn.Pipeline` + `ColumnTransformer`. This is the industry standard and the only correct way to prevent leakage across CV folds.

- **[DUMMY VARIABLE TRAP]** Added `drop='first'` to `OneHotEncoder` for categorical features. Original encoded sex without dropping one column, introducing perfect collinearity.

- **[MISSINGNESS STRATEGY]** Imputation is now performed with a function (`impute_by_group_median`) that uses vectorized `groupby.transform` instead of 4 copy-pasted `.loc` assignments.

- **[IMPUTATION JUSTIFICATION]** Little's MCAR test result now drives imputation strategy selection with explicit explanation of MCAR/MAR/MNAR definitions.

---

### 🟡 Code Quality Improvements

- Consolidated all imports into a single cell at the top of the notebook
- Added `RANDOM_STATE`, `ALPHA`, `TEST_SIZE`, `N_CV_FOLDS` configuration constants — no magic numbers
- Set `np.random.seed(RANDOM_STATE)` globally for reproducibility
- Replaced `== True` boolean comparisons with direct boolean indexing
- Replaced 4 copy-paste imputation lines with a single reusable function
- Fixed spelling errors in comments ("Impelement" → "Implement")
- Added `LabelEncoder` removal — target encoding now uses a simple `.map()` dict
- Removed intermediate `drug_safety_copy` dataframe — missingness flags computed inline

---

### 🟢 EDA Enhancements

- Added target class distribution bar chart with percentages
- Added `num_effects` distribution plot
- Added treatment group vs. adverse effects stacked proportion chart
- Added age distribution histograms overlaid by adverse effect status
- Added blood count distribution histograms by adverse effect status
- Added weekly adverse effect rate line chart (critical for safety monitoring)
- Added correlation heatmap for numerical features
- Added outlier box plots for all numerical features with IQR counts

---

### 📊 Visualisation Improvements

- All figures have: professional title, axis labels, legend, consistent colour palette
- All figures saved to disk (`fig1_*.png` … `fig10_*.png`) for reproducibility
- Every visualisation is followed by a markdown cell explaining the observation and its implications

---

### 📝 Documentation Improvements

- Added formal Business Understanding and Problem Statement section
- Added Dataset Description table
- Hypotheses stated formally in Markdown before each test
- Every major preprocessing decision justified with a Teaching Note
- Added Limitations section
- Added Next Steps section with specific actionable recommendations
- Business answer synthesised in plain language closing the loop on the original question

---

### 🏗️ Structural Improvements

- Reorganised into 13 numbered sections following the professional DS workflow
- Added `register_test()` helper to collect all test results for joint correction
- Added `cohens_d()`, `rank_biserial_r()`, `cramers_v()` reusable functions
- Model evaluation now includes: Classification Report, Confusion Matrix, ROC Curve, Feature Importance chart

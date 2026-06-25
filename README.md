# Drug Safety Analysis: Adverse Effect Detection in a Randomized Controlled Trial

![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![License](https://img.shields.io/badge/License-MIT-green)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

---

## Project Overview

This project presents an independent statistical analysis of a pharmaceutical randomized controlled trial (RCT) dataset. The analysis was conducted on behalf of a drug safety non-profit organization to evaluate whether a new drug is associated with adverse reactions at a statistically and clinically meaningful rate.

The notebook follows a full, reproducible data science workflow: from exploratory analysis through formal hypothesis testing to a random forest predictive model — all with correct statistical methodology, no data leakage, and production-grade code organization.

---

## Business Problem

GlobalXYZ completed an RCT and shared the dataset with our organization. Our mandate:

> **Are adverse reactions associated with the drug treatment at a statistically and clinically significant level, controlling for demographic and physiological covariates?**

---

## Dataset Description

Source: [Hbiostat](https://hbiostat.org/data/) — Vanderbilt University Department of Biostatistics

| Column | Type | Description |
|---|---|---|
| `sex` | Categorical | Participant biological sex (`male` / `female`) |
| `age` | Continuous | Age in years |
| `week` | Ordinal | Trial week |
| `trx` | Categorical | Treatment group: `Drug` or `Placebo` |
| `wbc` | Continuous | White blood cell count (×10³/μL) |
| `rbc` | Continuous | Red blood cell count (×10⁶/μL) |
| `adverse_effects` | Binary | ≥1 adverse effect occurred (`Yes` / `No`) |
| `num_effects` | Count | Number of adverse effects experienced |

Drug-to-placebo ratio: **2:1**

---

## Project Workflow

```
Data Loading
    ↓
Data Quality Assessment
  ├── Duplicate detection
  ├── Missing value analysis (Little's MCAR test)
  └── Group-conditional imputation
    ↓
Exploratory Data Analysis
  ├── Target distribution & class balance
  ├── Treatment group vs. adverse effects
  ├── Demographic analysis (age, sex)
  ├── Blood count distributions
  ├── Adverse effect rate over trial weeks
  └── Correlation matrix
    ↓
Outlier Analysis
    ↓
Statistical Testing (with multiple comparisons correction)
  ├── Normality checks (Shapiro-Wilk + Q-Q plots)
  ├── Continuous: Welch's t-test or Mann-Whitney U
  ├── Statistical significant VS. Practical significant
  ├── Categorical: Chi-Square (+ Cramér's V)
  └── Benjamini-Hochberg FDR correction
    ↓
Machine Learning
  ├── Pipeline (ColumnTransformer + StandardScaler + OneHotEncoder)
  ├── Stratified train/test split
  ├── Stratified k-fold cross-validation
  ├── Random Forest (class_weight='balanced')
  ├── Confusion matrix + ROC curve
  └── Feature importance
    ↓
Conclusions & Business Recommendations
```

---

## Key Findings

- The Chi-Square test did **not** find statistically significant evidence that drug vs. placebo assignment is associated with adverse effect occurrence (after BH correction).
- **Important caveat:** Failure to reject H₀ does not confirm safety. It reflects the statistical power of this specific dataset.
- No statistically significant association found between sex and adverse effects.
- The Random Forest baseline provides a multivariate view; AUC-ROC reflects the discriminative difficulty given the feature set.

---

## Statistical Methods (Parametric VS. Non-parametric)

| Variable Pair | Test | Effect Size Metric |
|---|---|---|
| Age vs. AE | Welch's t-test or Mann-Whitney U | Cohen's d / Rank-biserial r |
| WBC vs. AE | Welch's t-test or Mann-Whitney U | Cohen's d / Rank-biserial r |
| RBC vs. AE | Welch's t-test or Mann-Whitney U | Cohen's d / Rank-biserial r |
| Sex vs. AE | Chi-Square | Cramér's V |
| TRX vs. AE | Chi-Square | Cramér's V |

Multiple comparisons correction: **Benjamini-Hochberg FDR** (α = 0.05)

---

## Technologies Used

| Package | Purpose |
|---|---|
| `pandas`, `numpy` | Data manipulation |
| `matplotlib`, `seaborn` | Visualisation |
| `scipy`, `pingouin` | Statistical testing |
| `statsmodels` | Multiple comparisons correction |
| `scikit-learn` | ML pipeline, modelling, evaluation |
| `pyampute` | Little's MCAR test |

---

## How to Run

```bash
# 1. Clone the repository
git clone https://github.com/Omar-Mohamed-Eng/Drug-Safety-project-Hypothesis-testing-brainstorm.git
cd Drug-Safety-project-Hypothesis-testing-brainstorm

# 2. Create virtual environment
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Launch Jupyter
jupyter notebook notebook.ipynb
```

Ensure `drug_safety.csv` is placed in the project root directory.

---

## Folder Structure

```
drug-safety-analysis/
├── notebook.ipynb          # Main analysis notebook
├── drug_safety.csv         # Dataset
├── requirements.txt        # Python dependencies
├── README.md               # This file
├── CHANGELOG.md            # Version history and improvements
├── .gitignore              # Git ignore rules
└── figures/                # Exported visualisations (auto-generated)
    ├── fig1_target_distribution.png
    ├── fig2_treatment_adverse.png
    ├── fig3_demographics.png
    ├── fig4_blood_counts.png
    ├── fig5_weekly_trend.png
    ├── fig6_correlation.png
    ├── fig7_outliers.png
    ├── fig8_normality.png
    ├── fig9_model_evaluation.png
    └── fig10_feature_importance.png
```

---

## Future Improvements

1. **Multiple imputation (MICE)** for more rigorous handling of MAR data
2. **Survival analysis (Cox model)** treating adverse effect onset as time-to-event
3. **Power analysis** to determine minimum sample size for conclusive findings
4. **Subgroup analysis** by individual adverse effect type
5. **Model calibration** using Platt scaling for reliable probability estimates
6. **SHAP values** for individual-level explainability
7. **Longitudinal mixed-effects models** exploiting the repeated-measures structure

---

## License

MIT License — see `LICENSE` for details.

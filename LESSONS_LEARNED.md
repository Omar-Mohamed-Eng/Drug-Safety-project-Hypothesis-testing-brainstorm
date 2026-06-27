# Lessons Learned

Key Data Science, Statistics, and Machine Learning concepts demonstrated in this project — written as study notes for junior practitioners.

---

## 🔬 Statistics

### 1. Failing to reject H₀ ≠ Accepting H₀
The most common misinterpretation in hypothesis testing. If p > α, you have insufficient evidence to conclude the alternative is true — you do **not** have evidence the null is true. In drug safety, this means you cannot claim a drug is "safe" from a non-significant test result alone. Statistical power, sample size, and effect size all determine whether a non-significant result is meaningful.

### 2. Effect Size Is Not Optional
A p-value answers "is there an effect?" not "how large is it?" With large N, even a 0.2-year difference in age becomes statistically significant at p < 0.001. Effect sizes (Cohen's d, Cramér's V, rank-biserial r) tell you whether the effect is practically meaningful. Always report both.

### 3. Multiple Comparisons Inflation
Running k tests at α = 0.05 gives a family-wise error rate of 1 − (1−α)^k. At k=5, this is ~23%. Always correct using Bonferroni (strict, for FWER) or Benjamini-Hochberg (less conservative, controls FDR). In practice, BH is preferred for exploratory analysis.

### 4. Check Assumptions Before Choosing a Test
- t-test requires approximate normality (or large N via CLT)
- Chi-Square requires expected frequencies ≥ 5 in all cells
- ANOVA requires normality + homogeneity of variances
Violating assumptions doesn't always invalidate results, but you should know when to fall back to a non-parametric alternative (Mann-Whitney U, Fisher's Exact, Kruskal-Wallis).

### 5. Missing Data Mechanisms
- **MCAR**: Missingness independent of data → simple imputation OK
- **MAR**: Missingness depends on *observed* data → conditional imputation, or MICE
- **MNAR**: Missingness depends on the *missing value itself* → most dangerous, requires specialised models
Always test the mechanism (Little's test, missingness pattern analysis) before imputing.

---

## 🤖 Machine Learning

### 6. Data Leakage Is the #1 Silent Killer
Leakage inflates test performance without you realising it. Two most common sources:
- **Scaling leakage**: Computing scaler statistics on the full dataset (or test set) before splitting
- **Imputation leakage**: Computing imputation statistics (mean, median) on the full dataset
The solution is always: **Split first. Fit all transformers on train only. Transform test using train statistics.**

### 7. Pipelines Are Not Optional
`sklearn.Pipeline` + `ColumnTransformer` is the only pattern that:
- Prevents leakage across CV folds
- Makes preprocessing reproducible for new data
- Keeps code clean and auditable
Treat any manual preprocessing chain (fit scaler → transform → fit model) as a code smell in production.

### 8. A Single Train/Test Split Is Unreliable
One split gives one noisy estimate of generalisation performance. The train/test split you happened to get might be unusually easy or hard. Stratified k-fold cross-validation averages over k estimates and gives you a mean ± std — the std tells you how stable the model is.

### 9. Class Imbalance Makes Accuracy Misleading
If 85% of participants have "No adverse effect," a model predicting "No" every time achieves 85% accuracy while being completely useless. Always check class distribution and use:
- `class_weight='balanced'` or `SMOTE` for imbalanced training
- F1, AUC-ROC, precision/recall as evaluation metrics
- Confusion matrix to understand error types

### 10. The Dummy Variable Trap
One-hot encoding k categories produces k binary columns that sum to 1 — perfectly collinear. This inflates variance in linear models. Always use `drop='first'` (or `drop='if_binary'`) in `OneHotEncoder`.

### 11. Why Random Forest?
The hierarchical structure of tree-based models such as Decision Trees, Random Forests and Gradient Boosted Trees allows them to handle imbalanced datasets better than non-tree-based models.

---

## 📊 EDA

### 11. EDA Should Tell a Story
EDA is not "run `.describe()` and some plots." It should progressively answer the business question:
1. What does the target look like? (class balance)
2. How does it vary by the key predictor? (treatment group)
3. What confounders exist? (demographics)
4. What temporal patterns exist? (week trend)

Every plot should be followed by: What do I observe? Why does it matter? What will I do next?

### 12. Always Visualise Your Target Variable First
Before modelling, know your target distribution. An 80/20 imbalance completely changes your evaluation strategy.

---

## 🏥 Clinical Context Reminders

### 13. Don't Remove Clinical Outliers Automatically
Extreme WBC or RBC values may represent genuine pathological readings. Unlike price or sensor data, clinical outliers often contain the signal you're looking for. Always consult domain knowledge before removing.

### 14. RCT Design ≠ Automatic Causal Inference
Even in an RCT, the statistical analysis doesn't automatically imply causality. Proper causal claims require: randomisation verification, ITT analysis, sensitivity analysis, and more. Statistical significance is not sufficient.

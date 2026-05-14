# EDA Findings — IBM HR Analytics

**Date:** 2026-05-14
**Stage:** Phase 1, Week 1 — Exploratory Data Analysis
**Dataset:** IBM HR Analytics Employee Attrition (Kaggle), 1,470 employees, 35 variables
**Author:** Abhishek Sharma

---

## 1. Executive summary

IBM HR Analytics is a clean, well-structured dataset suitable as a starter for attrition modelling. It has a binary attrition target (16.1% positive class, class-imbalanced), no missing values, and no duplicates. Linear correlations with attrition are uniformly weak (maximum absolute correlation = 0.17), suggesting non-linear models (tree-based ensembles) are likely to substantially outperform logistic regression. Strong patterns emerge from categorical variables (OverTime, BusinessTravel, MaritalStatus) and from a tightly correlated seniority cluster (Age, JobLevel, MonthlyIncome, TotalWorkingYears).

Key limitations: cross-sectional snapshot only (no time dimension), partially synthetic, four "wellbeing" variables fail to inter-correlate (challenging the assumption that they measure a single construct), and `PerformanceRating` is unusable as a productivity outcome. These limitations directly motivate the Phase 1 synthetic data layer.

---

## 2. Dataset profile

### Shape and integrity
- **Rows:** 1,470 employees
- **Columns:** 35
- **Missing values:** 0
- **Duplicate rows:** 0
- **Data types:** 26 numeric (int64), 9 categorical (object)

### Target variable: Attrition
- **Yes (left):** 237 (16.12%)
- **No (stayed):** 1,233 (83.88%)
- **Class imbalance:** ~1:5 — material for modelling. Accuracy will be misleading; ROC-AUC, PR-AUC, F1 preferred.

### Columns to drop
- **Constants (no variance):** `EmployeeCount` (always 1), `Over18` (always Y), `StandardHours` (always 80).
- **Identifier:** `EmployeeNumber` (1,470 unique values).
- **Unusable target:** `PerformanceRating` (only values 3 and 4 in the data: 1,244 / 226). Confirms IBM cannot support productivity modelling.

---

## 3. Categorical driver findings

Four categorical variables show meaningful variation in attrition rate against the overall 16.1% baseline:

### OverTime — dominant predictor
- Yes: **30.53%** attrition
- No: **10.44%** attrition
- **Gap: 2.9x** — by far the strongest single signal in the dataset.
- Flagged for robustness check: model must be re-trained without OverTime to assess whether other features carry meaningful weight when this variable is removed.

### BusinessTravel
- Travel_Frequently: ~25%
- Travel_Rarely: ~15%
- Non-Travel: ~8%
- Clean monotonic relationship — more travel, more attrition.

### MaritalStatus
- Single: ~25%
- Married: ~12%
- Divorced: ~10%
- Single employees are roughly twice as likely to leave as married ones.

### Department
- Sales: 20.6%
- Human Resources: 19.0%
- Research & Development: 13.8%

### Coherent narrative
The pattern across these four variables is internally consistent: **higher work intensity and weaker life-stage anchoring correlate with higher attrition.** Sales (high-pressure), Single (less rooted), OverTime (high-demand), Travel_Frequently (high-disruption) all align. This is draft material for the commercial story: workplace wellbeing interventions plausibly target the modifiable side of this — work demands.

---

## 4. Numeric driver findings

Six numeric variables inspected via box-plot and mean comparison. All move in the same direction:

| Variable | Stayer mean | Leaver mean | Direction |
|---|---|---|---|
| Age | 37.6 | 33.6 | Younger leave |
| MonthlyIncome | 6,832.7 | 4,787.1 | Lower-paid leave |
| YearsAtCompany | 7.4 | 5.1 | Shorter-tenure leave |
| YearsInCurrentRole | 4.5 | 2.9 | Less role time leave |
| TotalWorkingYears | 11.9 | 8.2 | Less experienced leave |
| DistanceFromHome | 8.9 | 10.6 | Longer commute leave |

### IBM leaver profile (unconditional)
> Younger (~33), less experienced (~8 years total work), lower-paid (~$4,800/month), shorter tenure (~5 years at company, ~3 years in role), with a slightly longer commute (~11 vs 9 miles).

### Three caveats worth noting
1. **These are unconditional differences.** Age, TotalWorkingYears, MonthlyIncome, and JobLevel are highly inter-correlated (see §6). Some of these effects will shrink or vanish once others are controlled for. Multivariate analysis is the proper test.

2. **MonthlyIncome's $2,000 gap is the largest absolute story but the most confounded.** Younger employees earn less, less experienced employees earn less, lower-level employees earn less. The model must untangle whether income matters *independently* of these. The likely truth: pay matters *relative to role and experience*, not in absolute terms.

3. **DistanceFromHome is genuinely surprising.** Most HR models find commute distance a weak predictor. A 2-mile gap is small but consistent in direction. Worth including as a candidate wellbeing input in the synthetic data design — plausibly mediates through energy, work-life balance, and time-with-family.

---

## 5. Wellbeing variable analysis

### Candidates
The four IBM variables conventionally treated as wellbeing proxies:
- JobSatisfaction (1–4 scale, mean 2.73)
- EnvironmentSatisfaction (1–4 scale, mean 2.72)
- RelationshipSatisfaction (1–4 scale, mean 2.71)
- WorkLifeBalance (1–4 scale, mean 2.76)

### Critical finding: they don't inter-correlate
Pairwise correlations between the four range from **-0.02 to +0.03** — effectively zero. In psychometric terms, these variables do not load on a common factor in the IBM dataset.

### Implications
- **Cronbach's alpha would be ≈ 0.** A composite wellbeing index by averaging or summing these four would not be a psychometrically valid scale.
- **Likely cause:** Artefact of IBM's synthetic data generation, which appears to have drawn these variables independently. Real-world satisfaction measures typically inter-correlate at 0.3–0.5 because they share an underlying "general affective evaluation of work."
- **Decision for IBM modelling:** Keep the four variables as *separate features*. Do not build a naïve composite.
- **Decision for synthetic data design:** Generate wellbeing as a *latent* variable that causes observed satisfaction indicators with realistic factor loadings (target inter-correlation ~0.4). This is one of the principal methodological motivations for the synthetic layer: IBM cannot demonstrate the wellbeing construct properly, so synthetic data must.

### Weak signal in individual wellbeing variables vs attrition
- JobSatisfaction: r = -0.103
- EnvironmentSatisfaction: r = -0.103
- WorkLifeBalance: r = -0.064
- RelationshipSatisfaction: r = -0.046

The two strongest are JobSatisfaction and EnvironmentSatisfaction, but neither exceeds |0.11|. The wellbeing-as-driver story is *visible but weak* in IBM. This is also expected if wellbeing is operating through mediators (OverTime, salary, life-stage) rather than directly.

---

## 6. Correlation findings (numeric variables)

### Top linear predictors of attrition (sorted by |r|)

| Variable | r with Attrition |
|---|---|
| TotalWorkingYears | -0.171 |
| JobLevel | -0.169 |
| YearsInCurrentRole | -0.161 |
| MonthlyIncome | -0.160 |
| Age | -0.159 |
| YearsWithCurrManager | -0.156 |
| StockOptionLevel | -0.137 |
| YearsAtCompany | -0.134 |
| JobInvolvement | -0.130 |
| JobSatisfaction | -0.103 |
| EnvironmentSatisfaction | -0.103 |
| DistanceFromHome | +0.078 |
| WorkLifeBalance | -0.064 |

**All correlations are weak.** The strongest is -0.17, well below any threshold for "strong" linear association. This is the headline finding: linear correlation is *not the right tool* for this data. Tree-based models will likely substantially outperform logistic regression because the signal is in non-linear combinations and interactions.

### Three collinearity clusters

**1. Seniority cluster** — variables effectively measuring the same underlying "experienced and established" construct:
- TotalWorkingYears ↔ JobLevel: 0.78
- TotalWorkingYears ↔ MonthlyIncome: 0.77
- TotalWorkingYears ↔ Age: 0.68
- JobLevel ↔ MonthlyIncome: **0.95** (effectively duplicate)
- YearsAtCompany ↔ TotalWorkingYears: 0.63

**2. Tenure cluster** — overlapping but distinct measures of "rootedness in the current role":
- YearsAtCompany ↔ YearsWithCurrManager: 0.77
- YearsAtCompany ↔ YearsInCurrentRole: 0.76
- YearsInCurrentRole ↔ YearsWithCurrManager: 0.71
- YearsAtCompany ↔ YearsSinceLastPromotion: 0.62
- YearsInCurrentRole ↔ YearsSinceLastPromotion: 0.55

**3. Performance pair** — likely mechanical relationship:
- PerformanceRating ↔ PercentSalaryHike: 0.77
- PerformanceRating already dropped; PercentSalaryHike acts as its proxy.

### Implications for modelling
- **Tree-based models (RF, XGBoost, LightGBM, GBM):** Collinearity is not a problem. Keep all variables.
- **Logistic regression:** MonthlyIncome and JobLevel at r=0.95 will produce unstable coefficients. Options: drop one, or build a single compensation index. Document the choice.
- **Engineered features worth trying:**
 - `career_stagnation = YearsSinceLastPromotion / max(YearsAtCompany, 1)` — captures "how stuck" an employee is.
 - `salary_relative_to_role = MonthlyIncome / median_income_at_JobLevel` — captures under-payment for the role, which may be a stronger wellbeing signal than absolute pay.

---

## 7. Cross-cutting findings and project-wide implications

### Findings that shape Phase 1 modelling
- Tree-based ensembles expected to outperform linear models. Plan to compare logistic regression, random forest, XGBoost, LightGBM, and sklearn Gradient Boosting.
- Class imbalance (16%) requires careful metric choice: ROC-AUC, PR-AUC, F1. Class weights or SMOTE for handling.
- OverTime is a likely dominant single predictor — must do a robustness check (model with vs without).
- Feature engineering should produce two derived signals: career stagnation and relative compensation.

### Findings that shape Phase 1 synthetic data design
- **Real wellbeing must inter-correlate.** Generate wellbeing as a latent variable causing observed satisfaction indicators with realistic loadings (factor loadings ~0.5–0.7, producing observed inter-correlations of ~0.3–0.5).
- **Include a commute_friction variable** as a wellbeing input (operationalised as commute minutes per day, not distance).
- **Include salary_relative_to_role**, not just absolute salary. Real attrition responds to relative pay.
- **Generate a longitudinal structure** that IBM lacks — monthly snapshots for 24 months — to enable Phase 3 causal analysis.

### Findings that shape commercial narrative
- The attrition driver story is coherent and HR-recognisable: work-intensity (OverTime, Travel) and life-stage anchoring (Single, younger, less experienced) drive risk.
- This positions Wellmatch as addressing the modifiable side: work-demands and work-related stress.
- The wellbeing-categories-to-bottom-line link Lavinia flagged (priority #5) is the natural headline output, since the data already shows wellbeing variables exist as candidates but their construct validity is poor on IBM — which is exactly the gap a wellbeing platform fills.

### Limitations of IBM (essential for dissertation)
- **Cross-sectional snapshot.** No time dimension, so no longitudinal modelling or genuine causal inference possible on IBM alone.
- **Partially synthetic.** Wellbeing variables are uncorrelated in ways real survey data is not. PerformanceRating compressed to 2 values.
- **Class imbalance.** 16% positive class. Some statistical noise.
- **Cleanliness.** No missing values is unrealistic for real HR systems. Phase 2 will need imputation strategies not exercised here.
- **Small.** 1,470 rows is small for sophisticated ML. Limits the complexity of models that can be reliably trained.

These limitations are not failures; they are findings. They define exactly what the synthetic data layer must contribute and what the dissertation must claim.

---

## 8. Next steps

### Day 2 (Week 1, Tuesday)
- Feature engineering: encoding decisions, scaling, derived variables.
- Train/test split (stratified 80/20).
- First model: regularised logistic regression baseline.

### Day 3–4 (Wednesday–Thursday)
- Train and tune the remaining four models: Random Forest, XGBoost, LightGBM, sklearn Gradient Boosting.
- Comparison table on ROC-AUC, PR-AUC, F1, precision, recall.

### Day 5 (Friday)
- SHAP analysis for the best model — global summary plot, dependence plots for top features.
- Insight memo draft (4–6 pages).
- Start synthetic data design document.

### Week 2 onwards
- Synthetic data generator construction.
- Multi-region calibration using ONS, BLS, EWCS distributions.

---

## 9. Files produced today

| File | Purpose |
|---|---|
| `notebooks/01_ibm_data_load_and_explore.ipynb` | Working notebook with all EDA cells |
| `outputs/figures/attrition_by_category.png` | 2x2 grid: Department, OverTime, BusinessTravel, MaritalStatus |
| `outputs/figures/numeric_by_attrition.png` | 2x3 grid: Age, Income, Tenure, Distance |
| `outputs/figures/correlation_heatmap.png` | Full correlation matrix |
| `docs/methodology_decisions.md` | Formal decisions log |
| `docs/eda_findings_notes.md` | This document |

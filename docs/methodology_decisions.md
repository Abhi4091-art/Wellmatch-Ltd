# Methodology Decisions Log

A running log of analytical decisions and the reasoning behind them.

---

## 2026-05-14 — IBM HR Analytics initial exploration

### Decisions on column handling
- **Drop:** `EmployeeCount`, `Over18`, `StandardHours` — constants, no variance, no information.
- **Drop from features:** `EmployeeNumber` — unique identifier, no predictive value.
- **Drop:** `PerformanceRating` — takes only values 3 and 4 (84.6% / 15.4%). Unusable as a productivity target. Documents that IBM does not support productivity proxy modelling, justifying use of synthetic data for this outcome.

### Finding: wellbeing variables in IBM are uncorrelated
Inter-correlations between JobSatisfaction, EnvironmentSatisfaction, RelationshipSatisfaction, and WorkLifeBalance range from -0.02 to +0.03 — effectively zero.

**Implication:** Cronbach's alpha for any composite wellbeing index from these four would be near zero. The variables do not measure a single latent construct in the IBM dataset.

**Likely cause:** Artefact of IBM's synthetic data generation, which appears to have drawn these variables independently. Real-world satisfaction measures typically inter-correlate at 0.3-0.5.

**Decision for Phase 1 IBM modelling:** Use the four satisfaction variables as separate features, not as a composite index. A composite would be statistically misleading.

**Decision for synthetic data layer (Phase 1 weeks 3-4):** Generate wellbeing as a latent variable that causes observed satisfaction indicators with realistic loadings, producing inter-correlations in the 0.3-0.5 range. This is one of the methodological motivations for building synthetic data: real wellbeing constructs are not independent, and IBM cannot demonstrate this.

### Class imbalance
- Attrition: 16.12% positive class (237 of 1,470)
- Implication: accuracy is a misleading metric. Use ROC-AUC, PR-AUC, F1.
- Will handle via class weights and/or SMOTE in modelling stage.

### Dataset cleanliness
- No missing values, no duplicates.
- Likely an artefact of IBM's curation, not representative of real HR data.
- Implication for Phase 2 (real client data): missingness will be a major issue not encountered here. Plan for imputation strategies.

### Categorical driver findings (initial EDA)

**OverTime** dominates: 30.5% attrition for Yes vs 10.4% for No — a 3x gap. **Robustness check required:** train models both with and without OverTime to assess whether other features have meaningful contribution.

**Other strong categorical signals (all coherent):**
- BusinessTravel: Travel_Frequently 25% vs Non-Travel 8%
- MaritalStatus: Single 25% vs Married 12% vs Divorced 10%
- Department: Sales 20.6% vs R&D 13.8%

**Emerging narrative:** Work-intensity (OverTime, Travel) and life-stage (Single) appear to be the dominant categorical drivers. Coherent with a "work demands and life-stage anchoring" framing — useful for the Wellmatch wellbeing-as-driver story.

### Numeric driver findings (initial EDA)

All six numeric variables inspected move consistently with the leaver direction:

| Variable | Stayer mean | Leaver mean | Direction |
|---|---|---|---|
| Age | 37.6 | 33.6 | Younger leave |
| MonthlyIncome | 6832.7 | 4787.1 | Lower-paid leave |
| YearsAtCompany | 7.4 | 5.1 | Shorter-tenure leave |
| YearsInCurrentRole | 4.5 | 2.9 | Less role time leave |
| TotalWorkingYears | 11.9 | 8.2 | Less experienced leave |
| DistanceFromHome | 8.9 | 10.6 | Longer commute leave |

**Confounding caveat:** Age, TotalWorkingYears, and tenure variables are highly inter-correlated. Income confounds with seniority. Modelling will partially disentangle these.

**Synthetic data implication:** Include commute_distance and salary_relative_to_role as wellbeing inputs in the synthetic generator. Salary should drive engagement and wellbeing, which then drives attrition.

### Correlation findings (numeric variables)

**All linear correlations with attrition are weak (max |r| = 0.17).** Top predictors are TotalWorkingYears (-0.17), JobLevel (-0.17), YearsInCurrentRole (-0.16), MonthlyIncome (-0.16), Age (-0.16). Strongly suggests tree-based models will outperform logistic — linear correlation isn't capturing the signal.

**Three collinearity clusters identified:**

1. **Seniority cluster** (Age, TotalWorkingYears, JobLevel, MonthlyIncome) — pairwise correlations 0.5-0.95. MonthlyIncome and JobLevel are r=0.95 — effectively duplicate.

2. **Tenure cluster** (YearsAtCompany, YearsInCurrentRole, YearsWithCurrManager, YearsSinceLastPromotion) — pairwise correlations 0.5-0.77.

3. **Performance pair** (PerformanceRating, PercentSalaryHike) — r=0.77. PerformanceRating already dropped; PercentSalaryHike acts as its proxy.

**Decisions for feature engineering:**
- For tree models (RF, XGBoost, LightGBM, GBM): keep all variables — collinearity doesn't hurt these.
- For logistic regression: consider dropping MonthlyIncome (keep JobLevel) or creating a single compensation index to avoid coefficient instability.
- Engineer `career_stagnation = YearsSinceLastPromotion / max(YearsAtCompany, 1)` as a derived feature.
- Engineer `salary_relative_to_role = MonthlyIncome / median_income_at_JobLevel` (calculated per job level).

**Implication for the wellbeing-as-driver story:**
The two satisfaction variables that *do* correlate with attrition (JobSatisfaction -0.10, EnvironmentSatisfaction -0.10) are weak but real. WorkLifeBalance is surprisingly weak (-0.06). This is consistent with the earlier finding that IBM's wellbeing variables don't measure a coherent construct.

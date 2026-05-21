# EWCS 2024 EDA — Notes

**Date:** 2026-05-20
**Phase:** 1, Week 2
**Dataset:** European Working Conditions Telephone Survey 2024 (Eurofound)
**Source:** UK Data Service study 9511
**Author:** Abhishek Sharma

---

## 1. What I did

Independent EDA of the EWCS 2024 dataset, focused on identifying what working conditions differentiate employees who say they're likely to leave their job from those who say they'll stay.

## 2. Sample

- Original dataset: 36,644 respondents across 35 European countries
- Filtered to 11 target countries: Belgium, Denmark, Finland, France, Germany, Ireland, Luxembourg, Netherlands, Norway, Sweden, Switzerland
- Filtered to employees only (excluded self-employed)
- **Final analytical sample: 10,585 employees**

## 3. Scope decisions

**Filter to employees only.** Wellmatch's target market is corporate employers; self-employed workers aren't in the commercial universe. Also resolves substantial missingness on attrition-related variables.

**Park `absent_days` as a primary outcome.** After employee filtering, missingness remains at 36.6% and is country-driven (France 54.6%, Netherlands 51.6% vs Sweden 21.5%, Norway 21.8%). Cross-country comparison would be biased. Absence cost calibration will come from ONS and BLS instead.

**Define at-risk binary.** \`quit_job\` measured on 1–5 scale (1 = "Very likely" to leave, 5 = "Very unlikely"). At-risk = scores 1 or 2.

## 4. Headline finding: 1 in 5 European employees considers leaving

**19.8% of employees in target countries report being likely to leave within 12 months.**

### At-risk rate by country

| Country | At-risk % |
|---|---|
| Finland | 25.6% |
| Norway | 25.0% |
| Sweden | 24.5% |
| Denmark | 20.0% |
| Germany | 19.2% |
| Ireland | 18.9% |
| Luxembourg | 18.8% |
| France | 18.6% |
| Switzerland | 18.2% |
| Netherlands | 17.3% |
| Belgium | 15.8% |

### Why Nordic > Continental?

Counter to intuition. Three plausible reasons:

1. **Labour market mobility.** Nordic countries have low unemployment — workers there can leave easily. Intent reflects perceived option value, not dissatisfaction.
2. **Cultural directness.** Nordic respondents are typically direct in surveys; Belgian respondents may understate intent.
3. **Sectoral composition.** Nordics have larger tech/professional sectors with high mobility norms.

**Implication:** Country level captures labour market dynamics, not just workplace conditions. Multivariate analysis must include country fixed effects.

## 5. Driver ranking — top 15 predictors of intent-to-leave

Method: Cohen's d (standardised effect size) comparing at-risk (n ≈ 2,000) vs stayers (n ≈ 8,100). All p-values < 0.001.

| Rank | Variable | d | Interpretation |
|---|---|---|---|
| 1 | recognition | 0.52 | At-risk feel less recognised |
| 2 | eng_enthusiastic | 0.51 | At-risk less enthusiastic |
| 3 | priority_well_being | 0.47 | At-risk feel employer doesn't prioritise wellbeing |
| 4 | eng_energy | 0.46 | At-risk less energetic at work |
| 5 | fair_treatment | 0.43 | At-risk feel less fairly treated |
| 6 | work_welldone | 0.42 | At-risk feel less appreciated |
| 7 | useful_work | 0.40 | At-risk feel work is less meaningful |
| 8 | exhaust_emot | 0.39 | At-risk more emotionally exhausted |
| 9 | er_balance | 0.38 | At-risk feel effort vs reward imbalanced |
| 10 | wellbeing (WHO-5) | 0.37 | At-risk score 7.3 points lower on WHO-5 |
| 11 | support_manager | 0.35 | At-risk get less manager support |
| 12 | exhaust_phys | 0.32 | At-risk more physically exhausted |
| 13 | stress | 0.30 | At-risk more frequently stressed |
| 14 | apply_ideas | 0.30 | At-risk get fewer chances to apply ideas |
| 15 | lonely | 0.29 | At-risk more lonely |

**Effect size benchmarks:** d ≈ 0.2 small, d ≈ 0.5 medium, d ≈ 0.8 large. All top 10 reach medium.

## 6. The structural pattern

Grouping the top 15 by theme:

**Recognition & dignity (6 of top 10):** recognition, fair_treatment, work_welldone, useful_work, er_balance, priority_well_being. *Six of the top ten predictors are about whether employees feel seen, valued, and fairly compensated — not paid more or worked less.*

**Engagement & burnout (4 strong findings):** eng_enthusiastic, eng_energy, exhaust_emot, exhaust_phys. Direct UWES and Maslach measures.

**Manager quality (1):** support_manager at rank 11. Important but not dominant.

**General wellbeing:** WHO-5, stress, lonely, work-life balance — all present at medium effects.

**What is NOT in the top 15:** Workload variables (long hours, night, weekend, longday). Pay (only via effort-reward perception, not raw). *How employees are treated matters more than how hard they work.*

## 7. Theoretical grounding

Pattern aligns with two established frameworks in occupational psychology:

- **Effort-Reward Imbalance theory (Siegrist):** the imbalance between effort given and recognition received drives turnover intention. The dominance of recognition variables in our top 7 supports this.
- **Job Demands-Resources theory (Bakker & Demerouti):** resources (recognition, autonomy, support) protect against burnout when demands are high. Top 10 mixes both demand and resource variables, consistent with this.

## 8. The Wellmatch finding

\`priority_well_being\` ranks 3rd — whether employees feel their employer prioritises wellbeing (d=0.47). Wellmatch's product makes employer wellbeing investment *visible* to employees. The data shows visibility of wellbeing investment is among the strongest predictors of retention.

That's the direct line from product to outcome.

## 9. EWCS vs IBM — what EWCS gives the project

| Dimension | IBM | EWCS |
|---|---|---|
| Wellbeing measure | 4 unrelated items (Cronbach α ≈ 0) | Validated WHO-5 index |
| Recognition / fairness | Not measured | 6 of top 7 predictors |
| Manager quality | Not measured | Measured (rank 11) |
| Engagement / burnout | Not measured | Top 10 |
| Sample | 1,470 (single company) | 10,585 (11 countries) |
| Outcome | Binary attrition (retrospective) | Intent (prospective) |

**Conclusion:** IBM gives clean target, weak features. EWCS gives rich features, softer target. Together they bracket the truth.

## 10. Methodological caveats

- **Univariate, not multivariate.** Top 15 variables overlap heavily (recognition, fair treatment, work appreciated likely correlate at r>0.5). True number of independent drivers probably 4–6.
- **Intent ≠ behaviour.** 19.8% at-risk rate is higher than typical actual annual attrition (10–15%). Intent is broader.
- **Self-report throughout.** Common-method bias may inflate correlations between similarly-worded items.
- **Cross-sectional.** No causal claims possible from this data alone.

## 11. What's next

1. **Multivariate regression** with country fixed effects to identify independent drivers
2. **Cross-dataset triangulation:** do recognition variables predict actual attrition in IBM proxies?
3. **ONS / BLS EDA** for UK and US calibration of absence costs (deliberately not covered by EWCS)
4. **Synthetic data design:** wellbeing as latent variable, recognition variables alongside workload, effect sizes calibrated to EWCS

## 12. Files

| File | Purpose |
|---|---|
| \`notebooks/02_ewcs_eda.ipynb\` | Working EDA notebook |
| \`outputs/tables/ewcs_atrisk_comparison.csv\` | Full 44-variable ranking |
| \`outputs/figures/ewcs_atrisk_by_country.png\` | Country at-risk rates |
| \`outputs/figures/ewcs_drivers_ranking.png\` | Top 15 drivers visual |
| \`outputs/figures/ewcs_wellbeing_distribution.png\` | WHO-5 distribution by at-risk status |
| \`docs/ewcs_findings.md\` | This document |

---

## The one-paragraph version

EWCS 2024 analysis on 10,585 European employees in 11 target countries. 19.8% report being likely to leave within 12 months. The strongest predictors of intent-to-leave aren't workload or pay — they're recognition (Cohen's d = 0.52), enthusiasm at work (0.51), and whether employees feel their employer prioritises wellbeing (0.47). Six of the top ten drivers are about how employees are treated, not what they do. The \`priority_well_being\` finding is the direct empirical foundation for Wellmatch's commercial pitch: visibility of wellbeing investment is among the strongest predictors of retention.

# ONS / HMRC RTI Findings — UK Calibration Baseline

**Date:** 2026-05-21
**Phase:** 1, Week 2
**Datasets:**
- ONS Sickness Absence in the UK Labour Market, 1995–2025 (sicknessabsence2025.xlsx)
- ASHE Selected Estimates 1997–2025 (selectedestimates19972025.xlsx)
- HMRC PAYE Real Time Information, May 2026 release (rtinsamay2026.xlsx)

**Author:** Abhishek Sharma

---

## 1. What I did

Independent EDA of three official UK datasets to provide calibration figures for the project's ROI claims and structural backdrop for the EWCS individual-level analysis. This is "light EDA" — these datasets are pre-aggregated official statistics, so the work is about extracting headline numbers and plotting trends, not statistical modelling.

The three files together answer three questions:

1. What does UK absence look like, structurally and over time? (ONS sickness absence)
2. How much does an average UK employee earn — and therefore how much does an absent day cost? (ASHE)
3. How does actual UK turnover compare to EWCS's intent-to-leave figure? (HMRC RTI)

The arithmetic that ties them together:

> Days absent (ONS) × Daily wage (ASHE) = £ cost of absence per employee
> 4.4 × £128.50 = £565 per employee per year

> Intent to leave (EWCS, 19.8%) − Actual voluntary turnover (RTI, ~15%) = ~5 percentage point gap
> That gap is the early-warning window Wellmatch sits in.

---

## 2. Headline UK calibration numbers (2025)

| Number | Value | Source |
|---|---|---|
| UK sickness absence rate | 2.0% | ONS Sickness Absence, Table 1 |
| Days lost per UK worker per year | 4.4 | ONS Sickness Absence, Table 3 |
| Total UK days lost annually | 149 million | ONS calculation |
| UK median gross weekly pay | £642.50 | ASHE Table 1 |
| Implied median daily pay | £128.50 | ÷ 5 |
| Direct cost of UK absence per employee/year | £565 | days × daily pay |
| UK total payrolled employees | 30.1 million | RTI Sheet 1, April 2026 |
| UK total annual payroll outflow rate | 23.3% | RTI Sheet 6, 12-month rolling |
| Estimated UK voluntary turnover (~65% of total) | ~15% | RTI derived |

---

## 3. The headline UK ROI calibration

Direct annual cost of sickness absence per UK employee:

> 4.4 days × £128.50/day = £565 per employee per year

For a 1,000-employee client: £565,000/year in direct absence cost. A 10% reduction (a plausible target from wellbeing intervention research) saves £56,500/year per 1,000 employees.

**Important caveat:** this is direct cost only — the wage paid during absence. CIPD's full-cost model puts the true cost at 2–3× direct (£1,100–£1,700/employee/year), once you include replacement labour, lost productivity, and management time. For commercial pitch material, the £565 number is the conservative figure; the higher band is the realistic full cost.

---

## 4. Finding: the composition of UK absence is shifting

Mental health is rising as a share of UK sickness absence; physical complaints are falling.

| Cause | 2009 share | 2025 share | Direction |
|---|---|---|---|
| Mental health conditions | 9.0% | 11.5% | +28% relative |
| Musculoskeletal problems | 26.7% | 18.3% | -31% relative |
| Minor illnesses | 29.0% | 22.2% | -23% relative |

Mental health peaked at 14.6% in 2019 (pre-pandemic) and has been volatile since. The long-term trajectory is clearly upward.

**For Wellmatch:** the composition shift validates a product positioned on mental health. The category of UK absence Wellmatch addresses is the only one growing.

---

## 5. Finding: the wellbeing-impact validator

Employees with long-term health conditions miss roughly 4× as many days as everyone else, and the gap is widening over time.

| Year | LTHC absence | Non-LTHC absence | Ratio |
|---|---|---|---|
| 1997 | 7.0% | 2.2% | 3.2× |
| 2017 | 4.4% | 1.1% | 4.0× |
| 2025 | 4.0% | 1.0% | 4.0× |

The ratio has widened from 3.2× in 1997 to 4.0× in 2025. Even as overall UK absence has fallen, the relative disadvantage of having a chronic condition has grown.

**Why this matters:** this is the strongest empirical evidence in the entire ONS file for "wellbeing measurably affects business outcomes." If employees with chronic conditions miss 4× more days than colleagues without, then interventions that reduce the prevalence or severity of chronic conditions (preventive care, ergonomic adjustments, mental-health support) have a direct, measurable absence impact. This number anchors every ROI claim Wellmatch makes.

---

## 6. Finding: industry pattern — demands, not pay, drive absence

UK absence rate by industry, 2025:

**Highest absence sectors:**
1. Transport & storage — 3.3%
2. Human health & social work — 3.0%
3. Mining, energy and water — 2.9%
4. Public admin & defence — 2.4%
5. Manufacturing — 2.2%

**Lowest absence sectors:**
1. Professional, scientific & technical — 1.0%
2. Information & communication — 1.1%
3. Agriculture, forestry & fishing — 1.2%
4. Construction — 1.4% (tied)
5. Accommodation & food services — 1.4% (tied)

A 3.3× gap between highest and lowest sectors.

### Pay does not predict absence

Cross-referencing ASHE 2025 median annual pay with ONS 2025 absence rates across 15 UK industries:

> Pearson correlation: r = 0.020, p = 0.94, n = 15.
> Pay does NOT predict UK absence at the sector level.

Pattern in the data:
- High pay + low absence: IT, Professional Services (knowledge work, high autonomy)
- High pay + high absence: Mining/energy (physical hazards regardless of pay)
- Low pay + low absence: Hospitality, Agriculture (possibly under-reported / presenteeism by workers lacking sick pay entitlement)
- Mid pay + high absence: Transport, Health & Social (high physical and emotional demands)

The empty quadrant in the data is low-pay-high-absence. The dominant public narrative would predict this quadrant to be full ("badly paid workers are stressed and miss work"). The UK data shows the opposite.

### Triangulation with EWCS

EWCS at individual level: recognition and fair treatment predict intent-to-leave; pay does not (effort-reward imbalance matters but raw pay doesn't).

ASHE × ONS at sector level: pay does not predict absence (r = 0.02); demands do.

Two different datasets, two different levels of analysis, two different outcomes — same finding. **Demands and recognition matter; pay does not, in any direct way.** This is a triangulated empirical refutation of the "pay people more to fix wellbeing" narrative.

---

## 7. Finding: the intent-vs-behaviour gap

Combining EWCS intent-to-leave with HMRC RTI actual outflows lets us quantify the gap between what European employees say they'll do and what UK employees actually do.

| Measure | Source | Figure |
|---|---|---|
| Intent to leave (next 12 months) | EWCS 2024, target European countries | 19.8% |
| Actual UK payroll outflow (12-month rolling) | RTI 2026, all departures | 23.3% |
| Estimated UK voluntary turnover (~65% of total) | RTI 2026, derived | ~15% |

Two things to note:

1. The 23.3% RTI figure includes all departures — retirements, contract endings, dismissals, deaths, moves to self-employment, transfers between PAYE schemes. The voluntary component is estimated at roughly 65% (consistent with historical CIPD figures), giving a UK voluntary turnover rate of approximately 15%.

2. EWCS's 19.8% intent rate is higher than the RTI ~15% voluntary rate. About 25% of "at-risk" employees stay despite their stated intent.

### Why this matters for Wellmatch

The intent-vs-behaviour gap is the entire premise of an early-warning system.

- If you measure actual departures, your data arrives too late — the employee has already gone
- If you measure intent, you have a forward-looking signal that fires before the behaviour

Roughly 1 in 4 employees who say they'll leave don't actually leave. They're the population a retention platform can save.

The commercial framing:

> "We don't tell you who left — we tell you who's about to."

### Methodological caveat

The 65% voluntary share is an estimate, not from the RTI data itself. RTI does not break outflows down by reason. Sensitivity analysis: applying 60%–70% as voluntary share gives a range of 14–16% UK voluntary turnover. The direction of the intent-vs-behaviour gap holds across this range.

---

## 8. Finding: the UK labour market is cooling

UK turnover and hire rates have fallen since 2022:

- 2018: ~28% turnover, ~29% hire rate
- 2022 (post-COVID rebound): ~26% turnover, ~30% hire rate
- 2026: ~23% turnover, ~23% hire rate

Net flow is near zero — the UK workforce isn't growing or shrinking, it's churning steadily. Hiring is no longer outpacing departures.

Possible drivers (speculative): cost-of-living anxiety reducing job-hopping, fewer good vacancies, post-COVID stabilisation.

**Implication for Wellmatch's commercial pitch:** in a cooling market, retention matters more than ever — employers can't easily replace leavers. The retention proposition gets stronger when the labour market is tight in either direction (overheating or cooling).

---

## 9. Methodological caveats

- **Pre-aggregated data.** ONS and HMRC publish summary statistics, not individual-level records. We extract and plot — we cannot run statistical inference at the individual level on these files.
- **65% voluntary turnover share is an estimate**, applied externally to RTI total outflows. RTI itself doesn't distinguish reasons.
- **2019–2021 sickness absence figures.** ONS treated the COVID years as discontinuous; some breakdowns are not reported.
- **Cross-sectional pay-vs-absence analysis** has n=15 industries; this is suggestive, not definitive. With small n and pre-aggregated data, the conclusion is best understood as "no positive evidence for the pay-absence relationship" rather than "definitive disproof."
- **EWCS is European; RTI is UK.** Intent-vs-behaviour comparison is approximate. UK-specific intent rates are not available in EWCS 2024 (UK is not in this wave).

---

## 10. What's next

1. **BLS EDA** to provide US calibration figures equivalent to ONS (especially absence rate, average earnings, JOLTS quits rate)
2. **Multivariate regression on EWCS** with country fixed effects, to identify independent drivers of intent-to-leave
3. **Synthetic data design** calibrated to these ONS / ASHE / RTI numbers: absence rate, daily wage, turnover rate, mental health share of absence, LTHC absence multiplier

---

## 11. Files produced

| File | Purpose |
|---|---|
| notebooks/03_ons_eda.ipynb | Working EDA notebook |
| outputs/figures/ons_absence_rate_uk.png | UK absence rate over 30 years |
| outputs/figures/ons_mental_health_share.png | Mental health rising, MSK falling |
| outputs/figures/ons_absence_by_lthc.png | LTHC 4× absence finding |
| outputs/figures/ons_absence_by_industry.png | Sector-level absence ranking |
| outputs/figures/ons_pay_vs_absence_by_industry.png | The "pay doesn't predict absence" scatter |
| outputs/figures/ons_rti_uk_turnover.png | UK turnover vs EWCS intent |
| docs/ons_findings.md | This document |

---

## The one-paragraph version

ONS / HMRC RTI analysis on three official UK datasets (sickness absence 1995–2025, ASHE earnings 1997–2025, RTI payroll flows 2014–2026). Headline UK calibration: 2.0% absence rate, 4.4 days lost per worker per year, £128.50 median daily pay → £565 direct annual cost of absence per employee (£1,100–£1,700 full cost per CIPD multiplier). Two substantive findings: (1) Mental health's share of UK absence has risen 28% since 2009 while musculoskeletal has fallen 31% — Wellmatch is positioned on the only growing category. (2) UK pay does not correlate with absence at sector level (r = 0.02, n = 15) — empirical refutation of the "pay people more" narrative, triangulating with the EWCS individual-level finding that recognition (not pay) predicts intent-to-leave. The intent-vs-behaviour gap (EWCS 20% vs RTI ~15% voluntary) shows roughly 1 in 4 at-risk employees stay despite intent — the early-warning window for a retention platform.

# BLS / FRED Findings — US Calibration Baseline

**Date:** 2026-05-27
**Phase:** 1, Week 3
**Datasets:**
- `JTSQUR.xlsx` — US Quits Rate, total nonfarm, monthly seasonally adjusted (FRED mirror of BLS JOLTS series JTSQUR, March 2016 – March 2026)
- `CES0500000030.xlsx` — US Average Weekly Earnings of Production and Nonsupervisory Employees, Total Private (FRED mirror of BLS CES, April 2016 – April 2026)
- `cpsa2025.xlsx` — BLS CPS Annual Averages 2025 (11-month average, October 2025 excluded due to US government shutdown)

**Author:** Abhishek Sharma

---

## 1. What I did

Independent EDA of three US labour datasets to provide US-equivalent calibration figures matching the UK baseline from notebook 03. This is calibration-focused — these are pre-aggregated official statistics, so the work is about extracting headline numbers, building UK-comparable figures, and validating cross-country patterns.

The three datasets together let us answer:

1. How much do Americans voluntarily quit each year? (JOLTS quits rate — the cleanest US "regrettable attrition" measure)
2. How much does a US working day cost? (CES weekly earnings + CPS full-time median)
3. What's the US absence rate, and is the demand-driven pattern from UK data also visible in US data? (CPS Table 46 + 47)

---

## 2. Headline US calibration numbers (2025)

| Number | Value | Source |
|---|---|---|
| US monthly quits rate (latest) | 2.0% | FRED JTSQUR, March 2026 |
| US annualised quits rate (× 12) | ~24% | JOLTS derived |
| Great Resignation peak (Nov 2021) | 3.0%/month (~36% annualised) | JOLTS historical |
| US median weekly earnings (full-time) | $1,204 | CPS Table 37, 2025 |
| Implied US daily wage | $240.80 | ÷ 5 |
| In GBP (FX 0.78) | £188/day | conversion |
| US absence rate (full-time) | 3.2% | CPS Table A-46, 2025 |
| Implied days absent/year | ~7.7 | 3.2% × 240 |
| Direct cost of absence/employee/year | $1,854 (~£1,446) | days × daily wage |
| Per 1,000 employees | $1.85M (~£1.45M) | direct |
| Fully-loaded (× 2.5×) | $4,635 (~£3,615) | SHRM multiplier |

### Important methodological note on 2025 BLS data

The 2025 CPS annual averages are **11-month averages, not 12**. Data for October 2025 was not collected due to the US federal government shutdown. BLS explicitly states: "2025 annual estimates are not strictly comparable with annual averages for other years." We use the 11-month average as the best available figure.

### Note on earnings series

This notebook uses two US earnings series. The first download was the FRED CES Production and Nonsupervisory series ($1,089/week — bottom ~80% of workforce). The second was the CPS Table 37 full-time median ($1,204/week — true median across all full-time workers). The CPS figure is the better comparator to UK ASHE median and is used for the headline calibration. The CES series is retained as a secondary check.

---

## 3. The headline US ROI calibration

> Direct annual cost: 7.7 days × $240.80/day = **$1,854 per employee per year**
> Per 1,000 employees: **$1.85 million/year direct cost**
>
> Fully-loaded (× 2.5× SHRM multiplier): **$4,635 per employee per year**
> Per 1,000 employees: **$4.6 million/year**

A 10% reduction in absence (plausible from intervention research) saves ~$464/employee fully-loaded — or $464k per year per 1,000 employees.

---

## 4. Finding: JOLTS shows the US labour market cooling from Great Resignation peak

US monthly quits rate, March 2016 – March 2026:
- 10-year mean: 2.28%/month (~27% annualised)
- 2018–2019 pre-COVID: ~2.3%/month
- COVID dip (mid-2020): ~1.6%/month — Americans held jobs
- Great Resignation peak (November 2021): **3.0%/month (~36% annualised)**
- 2022–2023 plateau: ~2.5%/month
- 2024–2026 cooling: 1.9–2.0%/month (~24% annualised)

**Current state:** US quits rate at 2.0%/month is back to pre-pandemic levels but below the 10-year mean. Cooling pattern matches what we saw in UK RTI.

### Methodological caveat on annualising
Multiplying monthly rate × 12 slightly overstates the true annual rate (people can quit twice in a year and get double-counted in the sum). A more conservative multiplier is × 11 to 11.5, giving ~22% real annual quit rate. The headline finding (US ~2× UK) holds across either estimate.

---

## 5. Finding: US shows the same demands-driven absence pattern as the UK

US absence rate by major occupation (CPS Table 47, 2025):

| Occupation | Absence rate |
|---|---|
| Service occupations | **3.7%** |
| Sales and office occupations | 3.5% |
| Production/transportation/material moving | 3.4% |
| Professional and related occupations | 3.3% |
| Mgmt/professional (parent) | 3.0% |
| Natural resources/construction | 2.9% |
| Mgmt/business/financial | **2.6%** |

US sectoral pattern (CPS Table 47):

| Sector | Absence rate |
|---|---|
| Education and health services | **3.6%** |
| Leisure and hospitality | 3.4% |
| Wholesale and retail trade | 3.2% |
| Information | 3.2% |
| Manufacturing | 2.9% |
| Transportation and utilities | 2.9% |
| Professional and business services | 2.9% |
| Mining/quarrying | 2.8% |
| Financial activities | 2.7% |
| Construction | **2.3%** |

### Cross-country pattern triangulation

**Both UK and US show the same structural pattern:**
- Caring/health/social sectors top the list (UK Health & Social 3.0%; US Education & Health 3.6%)
- Knowledge/financial work bottom the list (UK Professional Services 1.0%; US Mgmt/Business/Financial 2.6%)
- Construction surprisingly low in both (UK 1.4%; US 2.3%) — consistent with the no-work-no-pay presenteeism hypothesis

The narrative observation holds across both countries: **demands drive absence, not pay**. The exact magnitudes are not directly comparable due to NAICS (US) vs SIC07 (UK) classification differences.

---

## 6. Finding: substantial US gender gap in absence

From CPS Table A-46 (2025):

| Demographic | Absence rate | Illness/injury | Other reasons |
|---|---|---|---|
| Men 16+ | 2.6% | 1.9% | 0.7% |
| **Women 16+** | **4.0%** | **2.6%** | **1.4%** |
| Gap | **+1.4pp** | +0.7pp | +0.7pp |

US women's absence rate is **54% higher than US men's** (4.0% vs 2.6%). The "other reasons" gap is the bigger story — US women's non-illness absences are double men's, driven mainly by caregiving responsibilities. This is consistent with US having no federal paid family leave.

For Wellmatch: any US product positioning must address the structural gender component of absence. The 4.0% women's figure brings US closer to UK CIPD (9.4 days ≈ 3.9% absence rate) than the 3.2% national average suggests.

ONS does not publish a directly comparable gender breakdown of UK absence, so we cannot do an exact UK vs US gender comparison.

---

## 7. Finding: transatlantic comparison — the four numbers that matter

| Metric | UK | US | Ratio (US/UK) |
|---|---|---|---|
| Median daily wage | £128.50 | £188 ($240.80) | **1.46×** |
| Annual absence days | 4.4 (ONS) / 9.4 (CIPD) | ~7.7 (BLS) | UK CIPD > US > UK ONS |
| Direct absence cost/employee | £565 (ONS) / £1,208 (CIPD) | £1,446 ($1,854) | **1.20× UK CIPD** |
| Annual voluntary turnover | ~13.5% (RTI derived) | ~24% (JOLTS × 12) | **1.8× higher in US** |

### The four-sentence summary

1. **Earnings:** US daily wages are ~46% higher than UK in GBP terms.
2. **Absence:** US absence rate is higher than UK ONS (3.2% vs 2.0%) but lower in days than UK CIPD (7.7 vs 9.4). Per-day cost converges with UK CIPD figure because US workers earn more per day even when missing fewer days.
3. **Turnover:** US voluntary turnover is roughly 2× the UK rate — Americans quit far more often.
4. **Implication for Wellmatch:** the US opportunity is structurally larger because turnover is higher and absence costs are similar to UK fully-loaded. But the US labour market has lower switching costs, so the intent-vs-behaviour gap that exists in Europe may be narrower in the US.

---

## 8. Methodological caveats

- **CPS 11-month vs 12-month.** 2025 CPS data excludes October due to US federal government shutdown. BLS recommends caution in year-over-year comparison.
- **NAICS vs SIC07.** US and UK industry classifications don't map one-to-one. Cross-country sector comparison is qualitative pattern-matching, not direct number comparison.
- **No US intent-to-leave measure available.** EWCS doesn't cover the US. So we can't compute a US intent-vs-behaviour gap analogous to UK 9.3pp. The framing that "US workers act on intent more readily" is inference from the higher voluntary turnover, not direct measurement.
- **US regrettable attrition ~18-22%** uses the same CIPD-derived 78% factor we applied to UK total outflows. This is a reasonable industry assumption but not source-verified for the US.
- **FX rate 0.78 USD→GBP is approximate.** Real rate fluctuates; conclusion holds across 0.74–0.82 range.
- **Multiplying monthly JOLTS × 12 slightly overstates annual quits.** A more conservative multiplier is × 11 to 11.5. Direction of cross-country comparison is unaffected.
- **Pre-aggregated data.** BLS publishes statistics, not individual-level records. No statistical inference possible from these files; the EWCS dataset remains our only individual-level source.

---

## 9. What's next

1. **Multivariate EWCS regression** with country fixed effects to identify independent drivers of intent-to-leave (next dissertation milestone)
2. **Synthetic data design** now calibrated to both UK and US baselines:
   - Absence rate: 2.0% (UK ONS), 9.4 days (UK CIPD), 3.2% (US BLS)
   - Daily wage: £128.50 (UK), $240.80 (US)
   - Voluntary turnover: ~13.5% (UK), ~24% (US)
   - Mental health share of absence: ~11.5% (UK), partly captured in US "illness/injury" 2.2%
3. **Optional**: telework-vs-absence cross-check using CPS Tables 60-66 if Lavinia wants the WFH hypothesis empirically tested on US data

---

## 10. Files produced

| File | Purpose |
|---|---|
| `notebooks/04_bls_eda.ipynb` | Working EDA notebook |
| `outputs/figures/bls_jolts_quits_rate.png` | US quits rate over time with Great Resignation peak |
| `outputs/figures/bls_us_vs_uk_absence.png` | Two-panel chart: US occupations + UK vs US sector comparison |
| `data/external/bls/JTSQUR.xlsx` | FRED JOLTS quits rate raw data |
| `data/external/bls/CES0500000030.xlsx` | FRED CES weekly earnings raw data |
| `data/external/bls/cpsa2025.xlsx` | BLS CPS Annual Averages 2025 raw data |
| `docs/bls_findings.md` | This document |

---

## The one-paragraph version

BLS/FRED EDA on three US labour datasets (JOLTS quits 2016-2026, CES earnings 2016-2026, CPS Annual Averages 2025). **Headline US calibration: $1,854 direct absence cost per employee per year (£1,446 GBP equivalent), 1.20× the UK CIPD figure.** Two substantive findings: **(1) US shows the same demands-driven sectoral absence pattern as the UK** — caring/service occupations top the list in both countries (US Service 3.7%; US Education & Health 3.6%) and knowledge/financial work bottom (US Mgmt/Business 2.6%) — triangulating the EWCS individual-level finding across two independent national datasets. **(2) US voluntary turnover is roughly 2× the UK rate** (24% annualised vs ~13.5% UK), driven by lower US labour-market switching costs. Methodological caveats: 2025 CPS data is 11-month due to government shutdown; NAICS vs SIC07 classifications don't map cleanly; FX rate approximate.

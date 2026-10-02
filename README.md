# NHANES III ECG Left-Ventricular-Hypertrophy Criteria and Mortality — Public Analytic Datasets

## Overview

This repository provides the de-identified analytic datasets underlying the manuscript:

> Qi Z, Wen Y. *Discordance among electrocardiographic left ventricular hypertrophy criteria, conduction abnormalities, and long-term mortality: a survey-weighted analysis of NHANES III (1988–1994).* International Journal of Cardiology, 2026.

**What the study examines.** Two families of ECG criteria for left ventricular hypertrophy (LVH) are compared and cross-classified:

- a **voltage/code criterion** — Minnesota Code probable-LVH (G5; codes 5.1–5.2);
- a **mass criterion** — the ECG-estimated left-ventricular-mass flag (LVM3, the Novacode algorithm), plus the continuous ECG-estimated LV mass index (LVMI).

The two criteria classify different people as hypertrophic, and the disagreement is systematic and unidirectional. The repository also contains the conduction variables used to examine **why** the disagreement arises and whether it carries independent prognostic weight: Minnesota 7-series conduction codes (MC7) and automated QRS duration.

## Data source

- **NHANES III (1988–1994)** Electrocardiogram Data File, Examination File, Laboratory File, and Adult Questionnaire File — U.S. CDC / NCHS public-use files.
- **Mortality linkage**: NCHS 2019 public-use Linked Mortality File (deaths ascertained through December 31, 2019; maximum follow-up 31.1 years).
- **Linkage key**: `SEQN`, the NHANES unique participant sequence number (one-to-one across files).
- **Ethics**: NHANES data are publicly released, de-identified, and exempt from individual informed consent. Analysis of public-use data was exempt from institutional review board review.

## Repository contents

| File | Description |
|---|---|
| `nhanes3_analysis_d1.csv` | Main analytic dataset (8,557 rows × 191 columns; one row per participant). Contains every derived variable used in the manuscript. |
| `DataSupplement_Figure1_KM_raw.csv` | Raw data for the Figure 1 Kaplan–Meier survival curves (per-participant follow-up time, event indicator, censoring status, combined classification group C1–C6). |
| `DataSupplement_Figure2_HR_raw.csv` | Hazard ratios with 95% confidence intervals by QRS-duration group (Figure 2). |
| `DataSupplement_FigureS1_prevalence_raw.csv` | Counts and crude proportions of conduction markers and wide QRS by LVH-criteria agreement group (Figure S1). |

## Study cohort and coding of the four agreement groups

Of 8,561 participants in the ECG file, 4 were ineligible for mortality linkage; the remaining **8,557** constitute the analysis cohort (missing ECG codes treated as negative, consistent with the handling described in the manuscript).

`D1G` codes the cross-classification of the voltage and mass axes:

| `D1G` | Definition | n |
|---|---|---|
| `both_neg` | Voltage-negative and mass-negative (reference) | 7,488 |
| `lvm3_only` | Mass-positive / voltage-negative (the discordant group central to the study) | 898 |
| `g5_only` | Voltage-positive / mass-negative | 66 |
| `both_pos` | Positive by both criteria | 105 |

Conduction status is encoded in `ECPMC7` (Minnesota 7-series ventricular conduction code) and `ECPQRS` (automated QRS duration, ms). A **conduction marker** is defined as any Minnesota 7-series code 7.1–7.6 or a QRS duration ≥120 ms. Recordings not encodable for MC7 (code 8, "blank but applicable" in the source file) are treated as MC7-negative, and QRS values coded 888 are not classified as wide-QRS; participants with an unencodable MC7 code but a genuine QRS duration ≥120 ms (n = 8) remain classified as wide-QRS.

## Data dictionary (key variables in `nhanes3_analysis_d1.csv`)

| Variable | Description |
|---|---|
| `SEQN` | NHANES unique participant sequence number (linkage key) |
| `HSSEX` | Sex (1 = male, 2 = female) |
| `DMARACER` | Race / ethnicity (NHANES recode) |
| `HSAGEIR` | Age in years at interview |
| `SDPPSU`, `SDPSTRA`, `WTPFEX` | Survey design variables: primary sampling unit, stratification stratum, final examination weight |
| `ECPLVM` | ECG-estimated left ventricular mass (Novacode) |
| `ECPLVMI` | ECG-estimated LV mass index (Novacode ECG-LVMI) |
| `ECPLVM3` | Binary ECG LV mass estimate flag (LVM3), used as the mass criterion |
| `ECPMC2`, `ECPMC3`, `ECPMC6` | Minnesota-code ECG-LVH / voltage components (see the NHANES III ECG codebook) |
| `ECPMC7` | Minnesota Code 7-series ventricular conduction code (0 = none; 1–6 = codes 7.1–7.6; 8 = blank but applicable / unencodable) |
| `ECPQRS` | QRS duration in milliseconds (888 = unmeasurable) |
| `BMPBMI` | Body mass index (kg/m²) |
| `BMPWAIST` | Waist circumference (cm) |
| `SBP`, `DBP` | Systolic / diastolic blood pressure (mmHg) |
| `D1G` | Four-level agreement group (see table above) |
| `TIME` | Follow-up time in months (0 for participants who died on the examination day) |
| `EVENT` | Death indicator (1 = died) |
| `MORTSTAT` | Mortality status |
| `UCOD_LEADING` | Leading underlying cause-of-death code (NCHS) |

Full variable definitions follow the NHANES III ECG and examination codebooks (CDC / NCHS).

## Reproducibility notes

- Estimates in the manuscript come from **survey-weighted Cox proportional hazards models** with the examination sample weight (`WTPFEX`), baseline hazards stratified by the 49 sampling strata (`SDPSTRA`), and standard errors clustered on primary sampling units (`SDPPSU`) with a finite-stratum correction.
- The primary endpoint is all-cause mortality; secondary endpoints are heart-disease death (underlying cause code 1) and stroke death (code 5). Cause-specific models censor deaths from other causes at their death time. The public-use linked file is perturbed by NCHS, and 5 decedents lacked a coded underlying cause.
- Confidence intervals in the manuscript are computed from the design-based robust variance–covariance matrix. A delete-one-PSU jackknife (98 replicates) was used as an independent variance check.
- The data release is perturbed by NCHS and is **not** suitable for attempting to re-identify individual participants.

## How to cite

Qi Z, Wen Y. Discordance among electrocardiographic left ventricular hypertrophy criteria, conduction abnormalities, and long-term mortality: a survey-weighted analysis of NHANES III (1988–1994). International Journal of Cardiology, 2026. Analytic datasets: https://github.com/wenyuxiangnihao/nhanes3-lvh-mortality-data

## License

Released under the MIT License — see the `LICENSE` file. The underlying NHANES III data are distributed by the U.S. CDC / NCHS; this repository redistributes only derived analytic variables prepared by the authors.

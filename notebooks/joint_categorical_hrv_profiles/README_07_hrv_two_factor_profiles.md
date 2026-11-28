# Stage 07  Two-factor HRV profiling and conditional associations

**Project:** HRV characterization in older adults from southern Chile  
**Status:** Implemented, executed on the accepted dataset, and methodologically reviewed  
**Review date:** 2026-11-28  
**Stage:** 07  
**Notebooks:** `07_01_joint_categorical_hrv_profiles.ipynb`, `07_02_targeted_two_factor_hrv_comparisons.ipynb`, `07_03_conditional_continuous_profiles_and_synthesis.ipynb`

## 1. Purpose and analytical scope

Stage 07 extends the completed unifactorial analyses of Stage 06 by describing HRV in **joint profiles defined by two observed factors** and examining **within-stratum associations**. Its purpose is to characterize heterogeneity in the available cohort while preserving the original observations, categorical definitions, numerical variables, and outcome measurements.

The stage is deliberately divided into three complementary analyses:

1. **07.01 — Exhaustive descriptive atlas:** characterize all ten possible pairs of the five pre-existing categorical factors, including empty and poorly represented intersections.
2. **07.02 — Targeted two-factor comparisons:** estimate selected conditional rank-distribution differences and effect sizes for six scientifically motivated pairs of categorical factors.
3. **07.03 — Continuous conditional profiles and synthesis:** estimate Spearman associations within pre-existing strata, describe differences between correlations for binary strata, and integrate Stage 07 estimands without recomputing upstream analyses.

These are **cross-sectional, observational, exploratory analyses**. They are not adjusted regression analyses, longitudinal aging analyses, formal factorial interaction tests, or causal analyses. Stage 07 does not aim to generate a statistically significant result for every proposed comparison.

## 2. Accepted analytical data

**Input:** `outputs/dataset_final.csv`  
**Observations in the completed execution:** 402  
**Source data:** unchanged from the accepted preprocessing and inspection workflow (Stages 01–05).

### Outcomes

| Role | Source variables | Reported labels |
| --- | --- | --- |
| Primary HRV | `t2m_pre_sdnn`, `t2m_pre_rmssd`, `t2m_pre_hf`, `t2m_pre_lf`, `t2m_pre_vlf` | SDNN, RMSSD, HF, LF, VLF |
| Complementary cardiac measures | `t2m_pre_mean_rr`, `t2m_pre_mean_hr` | Mean RR, Mean HR |

### Factors

| Kind | Source variables | Original categories or use |
| --- | --- | --- |
| Categorical | `sex_category` | Female, Male |
| Categorical | `age_group` | 60–69, 70–79, 80+ |
| Categorical | `bmi_category` | Underweight, Normal, Overweight, Obesity |
| Categorical | `sbp_category` | <140 mmHg, ≥140 mmHg |
| Categorical | `dbp_category` | <90 mmHg, ≥90 mmHg |
| Continuous | `age`, `weight_kg`, `height_cm`, `imc`, `bp_systolic`, `bp_diastolic`, `bp_pam`, `bp_pp` | Age, weight, height, BMI, SBP, DBP, mean arterial pressure, pulse pressure |

All categorical thresholds and BMI definitions are inherited from preprocessing. They are **not recomputed or modified** in this stage. No additional exclusions, imputations, recoding, category merging, or outlier trimming are performed. The complete dataset is retained in memory; each stratum-specific estimate naturally uses only observations belonging to that stratum.

## 3. Notebook 07.01  Joint categorical HRV profiles

### Design

The notebook enumerates the ten distinct pairs of five categorical factors:

| Pair | Possible cells | Occupied cells | Empty cells | Minimum occupied n |
| --- | ---: | ---: | ---: | ---: |
| Age group × Sex | 6 | 6 | 0 | 6 |
| Age group × BMI category | 12 | 12 | 0 | 1 |
| Sex × BMI category | 8 | 8 | 0 | 2 |
| SBP category × DBP category | 4 | 4 | 0 | 15 |
| BMI category × SBP category | 8 | 8 | 0 | 3 |
| BMI category × DBP category | 8 | 7 | 1 | 9 |
| Age group × SBP category | 6 | 6 | 0 | 7 |
| Age group × DBP category | 6 | 5 | 1 | 17 |
| Sex × SBP category | 4 | 4 | 0 | 31 |
| Sex × DBP category | 4 | 4 | 0 | 18 |
| **Total** | **66** | **64** | **2** | — |

The two empty cells are **Underweight × DBP ≥90 mmHg** and **Age 80+ × DBP ≥90 mmHg**. Their descriptive rows are retained with `n=0` and undefined statistics. Absence of observations is not evidence that these biological profiles cannot occur.

For each cell × outcome, the notebook calculates observed `n`, mean, sample standard deviation (`ddof=1`), minimum, P05, P25, median/P50, P75, P95, maximum, and interquartile range. Sample SD is undefined when `n<2`. Small occupied cells remain visible; `n<5` and `n<10` flags are **descriptive warnings, not exclusion or universal inferential thresholds**.

**Actual coverage:** 462 planned cell × outcome rows, comprising 448 occupied profiles and 14 empty profiles. There are no significance tests or confidence intervals in 07.01.

**Figures:** 41 multipanel images (one support overview, ten cell-count maps, ten median atlases, and twenty distribution sheets).

**Principal tables:**

- `categorical_pairwise_cell_counts.csv`
- `categorical_pairwise_support_summary.csv`
- `cohort_hrv_reference.csv`
- `joint_hrv_descriptive_profiles.csv`
- `figure_manifest.csv`
- `run_information.csv`

## 4. Notebook 07.02  Targeted two-factor HRV comparisons

### Prespecified contrasts

Six pair families are analyzed, with **28 planned categorical contrasts** across the original sex, age, SBP, and DBP groupings:

| Comparison family | Planned contrasts |
| --- | ---: |
| Sex × Age group | 5 |
| Sex × SBP category | 4 |
| Sex × DBP category | 4 |
| SBP × DBP categories | 5 |
| Age group × SBP category | 5 |
| Age group × DBP category | 5 |
| **Total** | **28** |

The full comparison–reference direction, scientific question, source cells, group sizes, and estimation status are preserved in `planned_two_factor_contrasts.csv`. A positive signed effect always refers to the **comparison group relative to the reference group**. For example, sex contrasts use Female minus Male and pressure contrasts generally use high minus low within their named strata. The joint SBP/DBP diagonal contrast changes both pressure categories and is explicitly labeled as such; it must not be interpreted as isolating either factor.

### Estimands and uncertainty

For each contrast and cardiac outcome, the notebook reports:

- Two-sided asymptotic Mann–Whitney U for rank-distribution comparisons.
- Cliff's delta, defined as `P(X_comparison > X_reference) − P(X_comparison < X_reference)`, with ties contributing zero.
- Difference of observed medians, `median(comparison) − median(reference)`, in original outcome units.
- Within-group stratified percentile bootstrap 95% confidence intervals for Cliff's delta and median difference, using **1,500 remamples** and random seed `20261128`.
- Explicit group sizes, descriptive profiles, estimation status, and numeric raw and adjusted p-values where defined.

Mann–Whitney tests rank distributions; **their p-values are not p-values for a difference of medians** without additional assumptions. Similarly, a significant difference in one stratum and a nonsignificant difference in another does **not** establish interaction or effect modification.

The Age 80+ × DBP ≥90 group has no observations. Its planned categorical contrast therefore remains explicitly **non-estimable** and receives no U, delta, median difference, confidence interval, p-value, or q-value. Occupied but small groups, including the 80+ sex comparison (22 versus 6) and the 80+ SBP comparison (7 versus 21), remain included and are flagged for interpretive caution.

**Actual coverage:** 27 estimable of 28 planned contrasts; 189 estimable and 7 undefined contrast × outcome rows out of 196 planned rows.

**Figures:** 25 multipanel images (one support overview plus four views for each of the six pair families).

**Principal tables:**

- `planned_two_factor_contrasts.csv`
- `targeted_two_factor_effects.csv`
- `two_factor_comparison_group_profiles.csv`
- `two_factor_fdr_family_summary.csv`
- `figure_manifest.csv`
- `run_information.csv`

## 5. Notebook 07.03  Conditional continuous profiles and synthesis

### Prespecified design

Seventeen continuous predictor × categorical stratifier families cover the eight existing continuous measurements, stratified by sex, age groups, SBP category, or DBP category as defined in the notebook's `planned_continuous_strata.csv`.

- **P01–P08:** age, weight, height, BMI, SBP, DBP, MAP, and pulse pressure, separately within sex.
- **P09–P10:** age within SBP and DBP categories.
- **P11–P12:** BMI within SBP and DBP categories.
- **P13–P17:** weight, height, BMI, SBP, and DBP within age groups.

These correspond to **39 planned strata** and seven cardiac outcomes, yielding **273 stratum × outcome Spearman estimates**.

### Estimands and uncertainty

- Spearman's rank correlation coefficient with two-sided asymptotic p-value, calculated within each original stratum.
- Paired-record percentile bootstrap 95% confidence interval with 1,500 resamples, preserving predictor–outcome pairing.
- Explicit count of valid pairs and distinct predictor/outcome values; a correlation is undefined with insufficient pairs or a constant variable.
- For the 12 binary-stratifier families, direct exploratory differences `rho(comparison) − rho(reference)`, with independently resampled stratum-specific bootstrap percentile confidence intervals.

The **84 delta-rho rows** are descriptive estimates with individual 95% uncertainty intervals. They have **no p-values, q-values, multiplicity adjustment, or formal interaction-test interpretation**. In particular, an interval that excludes zero is an exploratory indication of between-stratum heterogeneity, not a multiplicity-adjusted finding of effect modification.

**Actual coverage:** 273 of 273 planned conditional Spearman estimates, 84 of 84 planned direct differences, with 1,500 valid bootstrap draws reported per estimate in this execution.

### Cross-stage synthesis

The notebook reads existing 07.01/07.02 tables without modifying them and builds coverage tables plus a tidy effect registry for 07.02 and 07.03:

| Effect type | Source | Rows | Estimable |
| --- | --- | ---: | ---: |
| Cliff's delta | 07.02 | 196 | 189 |
| Difference of medians | 07.02 | 196 | 189 |
| Conditional Spearman rho | 07.03 | 273 | 273 |
| Direct delta-rho | 07.03 | 84 | 84 |
| **Combined registry** | **07.02 + 07.03** | **749** | **735** |

These effect types represent **different estimands and units** and must not be pooled, averaged, or compared as if on one numerical scale. Mann–Whitney p/q are attached only to the appropriate rank-comparison entries; the median-difference and delta-rho entries do not inherit tests they were not designed to represent. Stage 06.04, when available, supplies an **optional descriptive reference** for corresponding unifactorial estimates without new hypothesis tests.

**Figures:** 37 multipanel images (one support overview, 17 Spearman heatmaps, 17 scatter atlases, and two delta-rho forest summaries).

**Principal tables:**

- `planned_continuous_strata.csv`
- `conditional_predictor_support.csv`
- `conditional_spearman_associations.csv`
- `conditional_fdr_family_summary.csv`
- `conditional_rho_differences_descriptive.csv`
- `phase_07_coverage_inventory.csv`
- `phase_07_outcome_coverage.csv`
- `phase_07_all_effects_long.csv`
- `conditional_vs_unifactorial_descriptive_reference.csv` (optional, when Stage 06.04 is available)
- `figure_manifest.csv`
- `run_information.csv`

## 6. Multiple testing and reporting conventions

**FDR families are local by design.** No pooled stage-wide or study-wide adjustment was performed or is claimed.

| Notebook | Benjamini–Hochberg correction scope | Separate outcome families |
| --- | --- | --- |
| 07.01 | Not applicable; description only | Not applicable |
| 07.02 | Each of the six prespecified categorical pair families | 5 primary HRV / 2 complementary cardiac outcomes |
| 07.03 | Each of the 17 predictor–stratifier families | 5 primary HRV / 2 complementary cardiac outcomes |

Each adjustment includes its family's planned **estimable** tests and retains undefined entries as missing. Local q-values do not control the false discovery rate over the full 07.02 set, the full 07.03 set, all of Stage 07, or Stages 06 and 07 together. Reports should label q-values as **within-family BH-FDR adjusted**.

Exploratory delta-rho contrasts are reported **without inferential p/q**. Interpret the 84 individual bootstrap intervals with multiplicity awareness; do not treat an interval excluding zero as a confirmatory interaction test. The numerical CSV files retain full p/q precision. Formatted Markdown results use `p < 0.001` or `q < 0.001` when appropriate; rounded values such as `0.000` may legitimately describe small effect estimates.

## 7. Figure and output conventions

The three notebooks collectively generate **103 figures** from the accepted dataset:

| Stage | Figures | Role |
| --- | ---: | --- |
| 07.01 | 41 | Exhaustive descriptive cell coverage and distributions |
| 07.02 | 25 | Conditional categorical comparisons and uncertainty |
| 07.03 | 37 | Conditional continuous associations and direct delta-rho |
| **Total** | **103** | |

All figures are stored in the notebook-specific `figures/multipanels/` directories and indexed by `tables/figure_manifest.csv`. For highly skewed spectral outcomes (HF/LF/VLF), `log1p` may be applied **for plotting only when mathematically defined**; exported statistics, ranks, effect sizes, and tests use the original source values. LOWESS curves are descriptive visual aids, not fitted inferential models. Small/empty cells are explicitly differentiated in descriptive outputs.

Each notebook generates its own methodological README and results report. The output directories contain six CSV tables for 07.01, six for 07.02, and **ten standard plus one optional** CSV tables for 07.03 (the optional unifactorial reference was present in the reviewed execution).

## 8. Directory structure and execution order

Place this document at:

`eda_results/hrv_associations/README_07_hrv_two_factor_profiles.md`

```text
project_root/
├── outputs/
│   └── dataset_final.csv
└── eda_results/
    └── hrv_associations/
        ├── README_07_hrv_two_factor_profiles.md
        ├── 07_01_joint_categorical_hrv_profiles/
        │   ├── tables/
        │   ├── figures/multipanels/
        │   ├── README_07_01_joint_categorical_hrv_profiles.md
        │   └── 07_01_joint_categorical_hrv_profiles_results.md
        ├── 07_02_targeted_two_factor_hrv_comparisons/
        │   ├── tables/
        │   ├── figures/multipanels/
        │   ├── README_07_02_targeted_two_factor_hrv_comparisons.md
        │   └── 07_02_targeted_two_factor_hrv_comparisons_results.md
        └── 07_03_conditional_continuous_profiles_and_synthesis/
            ├── tables/
            ├── figures/multipanels/
            ├── README_07_03_conditional_continuous_profiles_and_synthesis.md
            └── 07_03_conditional_continuous_profiles_and_synthesis_results.md
```

**Recommended reproducible execution order**

1. Confirm that the accepted `outputs/dataset_final.csv` and original Stage 05 category definitions are available.
2. Run `07_01_joint_categorical_hrv_profiles.ipynb` and inspect the categorical support and descriptive profile tables.
3. Run `07_02_targeted_two_factor_hrv_comparisons.ipynb` and inspect the planned contrast ledger, estimability, and effect sizes.
4. Run `07_03_conditional_continuous_profiles_and_synthesis.ipynb`, which requires the exported 07.01/07.02 tables for synthesis. The Stage 06.04 synthesis is optional context.
5. Inspect the stage-specific README, results Markdown, table manifests, and coverage inventory before interpreting any specific finding.

All notebooks read the accepted input dataset without modifying it. Their generated outputs are written to separate stage-specific directories. Use `INPUT_FILE_OVERRIDE` and/or `OUTPUT_ROOT_OVERRIDE` when the default project location does not apply; 07.03 also provides `ANALYSIS_ROOT_OVERRIDE` to locate existing upstream tables. Do not edit generated CSV tables manually to change numerical results.

### Runtime and reproducibility

The reviewed run records **Python 3.14.10**, **pandas 3.1.1**, **NumPy 2.5.5**, **SciPy 1.18.1**, **Seaborn 0.13.2**, and **Matplotlib 3.11.2**. Statistical bootstrapping in 07.02 and 07.03 used **1,500 resamples** and seed **20261128**, with percentile 95% confidence intervals. Stage 07.01 uses deterministic plot-jitter settings and does not perform inferential bootstrapping. Environment details and file provenance are also saved in stage-specific `run_information.csv` tables.

## 9. Methodological audit and closure

The completed Stage 07 output audit compared the actual exported tables, notebook methodological descriptions, and cross-stage relationships. It focused on methodological correctness and interpretability, **not scientific novelty or visual aesthetics**.

### Verified properties

- All ten categorical pairs have 402 complete pair records, and cell sizes reconcile with the original category counts.
- Descriptive profiles from 07.01 match the corresponding group profiles reused in 07.02, including means, medians, quantiles, and sample sizes.
- Signed Cliff's delta estimates are algebraically consistent with their recorded Mann–Whitney U values and comparison directions.
- Within-family Benjamini–Hochberg q-values in 07.02 and 07.03 reproduce from the exported raw p-values.
- Every 07.03 delta-rho point estimate equals the relevant comparison-stratum rho minus reference-stratum rho.
- Bootstrap interval bounds are ordered, and empty categories do not receive invented effect estimates, confidence intervals, or p/q values.
- Stage 07.03 preserves upstream 07.01 and 07.02 outputs and keeps different effect types separate in its registry.

**Audit boundary:** This was an audit of generated outputs and their internal mathematical consistency, not a complete independent recalculation of every estimate directly from the 402 raw observations.

### Accepted limitations

1. **Sample imbalance and sparse cells.** Some original intersections contain only 1–7 observations. Such profiles remain documented, but their estimates may be unstable; percentile distributions from n=1 or n=2 are not robust population summaries. In particular, interpretations involving 80+ strata require caution.
2. **Source-record independence assumption.** Mann–Whitney and Spearman inference and the employed bootstrap procedures operate at the record level. Potential clustering or dependence among records from a shared source project is not accounted for.
3. **Observational and unadjusted interpretation.** Within-stratum estimates are conditional descriptions, not adjusted causal effects. No longitudinal trajectories can be inferred.
4. **No formal interaction model.** Delta-rho estimates characterize differences between observed stratum-specific associations. They are not equivalent to regression interaction terms; unadjusted individual bootstrap intervals are not confirmatory multiplicity-adjusted interaction tests.
5. **Overlapping variables and outcomes.** BMI is calculated from weight and height; MAP and pulse pressure derive from SBP and DBP; Mean RR and Mean HR are mathematically related. Repeated associations among these measurements do not provide independent validation.
6. **Protocol-dependent HRV interpretation.** Physiological interpretations of HF, LF, and VLF require the original acquisition and measurement protocol; LF is not assumed to be a unique measure of sympathetic activity.
7. **Non-significant findings.** Failure to reject an association or rank-distribution difference does not establish equivalence or physiological absence.

### Closure decision

**Stage 07 is closed as implemented and methodologically reviewed.** No mandatory numerical, estimand, or notebook corrections were identified in the agreed audit, and no additional statistical analysis is required to meet this stage's predefined goals. Further adjusted modeling or hypothesis-specific interaction testing should only be introduced through a new, explicitly justified research question, not retrospectively to obtain significance.

Stage 07 therefore provides an auditable descriptive and exploratory foundation for integrating Stages 05–07 into the manuscript, with the original effect magnitudes, uncertainty, sample support, and inferential limits preserved.

# Stage 05 — Data Inspection

## Purpose and analytical scope

This stage provides a **descriptive inventory of the final heart rate variability (HRV) study dataset** before examining associations between HRV outcomes and demographic, anthropometric, or physiological factors. The intention is to understand measurement distributions, the relationships among measurements within a domain, and the number of records available in each predefined categorical group.

The stage is deliberately limited to **data inspection**. It does not evaluate HRV differences between demographic or clinical groups, test hypotheses, fit statistical or Bayesian models, construct predictive models, or perform clustering. Descriptive patterns identified here may inform the design of later analyses, but they do not determine their conclusions.

Three independent notebooks cover complementary questions:

| Notebook | Question | Scope |
|---|---|---|
| `05_01_hrv_inspection.ipynb` | What do the seven cardiac measurements look like? | Distributions of five primary HRV measurements and two complementary cardiac measurements; descriptive relationships within this set |
| `05_02_non_hrv_inspection.ipynb` | What do the continuous explanatory measurements look like? | Age, anthropometry, and blood pressure: distributions, numerical completeness, and within-/cross-domain descriptive relationships |
| `05_03_category_counts.ipynb` | What categorical subgroups exist and how many records do they contain? | Marginal frequencies and all ten pairwise cross-tabulations of five categorical factors, including empty cells |

## Dataset, provenance, and analytical population

- **Input:** `outputs/dataset_final.csv`, produced by the preprocessing workflow `01 → 01b → 02 → 03 → 04`.
- **Inspection sample:** **402 source records**; this is the same input cohort in all three notebooks.
- **Source-row identifier:** `source_excel_row`. It identifies rows in the original Excel source and is not an independently verified participant identifier.
- **Input SHA-256**, recorded consistently in the three notebook READMEs: `687faa284c79bf4d1691d7e0a890274e255ad0bac3f9c47d337457694a794f38`.
- **Upstream cohort flow:** 530 original records → 123 set aside through the documented quality-review decision → 407 passing that review → 5 excluded by the age criterion (<60 years) → **402 final analytical records**.
- **Population represented by the analytical file:** records aged **60–89 years**.
- **No new filtering, imputation, correction, recoding, or replacement of source measurements occurs in Stage 05**. Generated tables and plots are separate outputs.

The distinction between 407 quality-reviewed records and 402 analytically eligible records is intentional. All results in Stage 05 use the **402-record** `dataset_final.csv`, not `dataset_review_clear.csv`.

## Directory structure and execution order

Place this README alongside the three notebooks:

```text
project/
├── outputs/
│   └── dataset_final.csv
├── notebooks/
│   └── data_inspection/
│       ├── README.md                         # this stage-level document
│       ├── 05_01_hrv_inspection.ipynb
│       ├── 05_02_non_hrv_inspection.ipynb
│       └── 05_03_category_counts.ipynb
└── eda_results/
    └── data_inspection/
        ├── 05_01_hrv_inspection/
        │   ├── tables/
        │   ├── figures/multipanels/
        │   ├── README_05_hrv_inspection.md
        │   └── 05_hrv_inspection_results.md
        ├── 05_02_non_hrv_inspection/
        │   ├── tables/
        │   ├── figures/multipanels/
        │   ├── README_05_non_hrv_inspection.md
        │   └── 05_non_hrv_inspection_results.md
        └── 05_03_category_counts/
            ├── tables/
            ├── figures/multipanels/
            ├── README_05_category_counts.md
            └── 05_category_counts_results.md
```

Recommended execution order: **05.01 → 05.02 → 05.03**. Each notebook independently reads `dataset_final.csv`; this order is for convenient review and does not imply data dependencies between notebooks. The input file can be discovered from the project directory tree or explicitly specified through `INPUT_FILE_OVERRIDE` in the configuration cell.

Each notebook generates **two Markdown documents** within its own result folder: a methodological README describing that notebook's implementation, and a descriptive results report derived from its numerical outputs. This stage-level README records their relationship and summarizes the completed inspection; it does not replace those more detailed documents.

---

## 05.01 — Inspection of HRV and complementary cardiac measurements

### Variables

**Primary HRV:** `t2m_pre_sdnn`, `t2m_pre_rmssd`, `t2m_pre_hf`, `t2m_pre_lf`, `t2m_pre_vlf`.

**Complementary cardiac measurements:** `t2m_pre_mean_rr`, `t2m_pre_mean_hr`.

All seven are complete for the 402 records in the inspected cohort. Original data scales are preserved in the numerical summaries.

### Methods and products

- Descriptive statistics: counts and missingness, mean, median, standard deviation, minimum/maximum, percentiles (P01, P05, P25, P50, P75, P95, P99), IQR, coefficient of variation where applicable, sample skewness and excess kurtosis.
- Tukey fences at 1.5 × IQR and 3 × IQR to **describe tail observations only**; these are neither clinical cutoffs nor exclusion rules.
- Original-scale histogram/KDE, violin/quartile, and empirical cumulative distribution (ECDF) views, organized in domain-specific multipanels.
- Q–Q plots against a normal reference and side-by-side original versus `log1p` distributions. The `log1p` operation affects **visualization copies only**, not the dataset or source-scale summary tables.
- Pairwise Spearman rank coefficients and scatterplot/pairplot visualizations **within the seven cardiac variables**, without p-values or hypothesis testing.

**Generated figure sheets (9):**

| File in `figures/multipanels/` | Contents |
|---|---|
| `01_primary_hrv_distribution_profiles.png` | Five primary HRV variables, three views per variable |
| `02_cardiac_interval_rate_profiles.png` | Mean RR and mean HR, three views each |
| `03_original_vs_log1p_distributions.png` | Original and log1p display scales |
| `04_normal_qq_grid.png` | Seven Q–Q diagnostics |
| `05_iqr_tail_summary.png` | Proportions beyond 1.5 × and 3 × IQR fences |
| `06_hrv_spearman_heatmap.png` | Seven-variable Spearman matrix |
| `07_primary_hrv_log1p_pairplot.png` | Five primary HRV variables, joint visualizations |
| `08_selected_hrv_relationships.png` | Four selected pairwise relationships |
| `09_primary_hrv_vs_cardiac_measures.png` | Five primary HRV variables crossed with RR and HR |

**Tables:** `hrv_descriptive_statistics.csv`, `hrv_tukey_tail_summary.csv`, `hrv_visualization_scales.csv`, `hrv_spearman_pairs.csv`, `hrv_spearman_matrix.csv`, and `figure_manifest.csv`.

### Main descriptive observations

| Measure | Median | Sample skewness | Beyond 1.5 × IQR (n/402) |
|---|---:|---:|---:|
| SDNN | 151.0 | 0.23 | 1 |
| RMSSD | 13.7 | 2.17 | 11 |
| HF | 110.5 | 6.72 | 53 |
| LF | 125.0 | 4.83 | 58 |
| VLF | 30.5 | 4.69 | 54 |
| Mean RR | 846.5 | 0.13 | 3 |
| Mean HR | 71.0 | 0.98 | 9 |

HF, LF and VLF show long right tails and substantially skewed distributions; the log-scale panels help display their central range. This does **not** make their original observations erroneous. The largest descriptive Spearman coefficients include LF–VLF (ρ = 0.830) and SDNN–RMSSD (ρ = 0.655). Mean RR–mean HR is approximately −0.999, reflecting their mathematical relation and not an independent physiological discovery. These coefficients do not establish group effects or causal mechanisms.

---

## 05.02 — Inspection of continuous non-HRV measurements

### Variables

| Domain | Plotted measures | Dataset columns |
|---|---|---|
| Demography | Age | `age` |
| Anthropometry | Weight, height, BMI | `weight_kg`, `height_cm`, `imc` |
| Blood pressure | SBP, DBP, mean arterial pressure (MAP/PAM), pulse pressure (PP) | `bp_systolic`, `bp_diastolic`, `bp_pam`, `bp_pp` |

Height in meters (`height_m`) is summarized and checked but **not independently plotted**, because it is the same height measurement expressed in different units. Sex and categorical versions of the measurements are covered by 05.03. All eight plotted measures are complete in the inspected cohort.

### Methods and products

- The same core descriptive statistics and non-exclusionary Tukey tail counts used in 05.01.
- Original-scale histograms/KDE, violin/quartile and ECDF panels; Q–Q diagnostics.
- Spearman matrices and pairwise scatterplots within and across continuous non-HRV domains; no p-values or statistical models.
- Documentation-only arithmetic checks of height centimeters/meters, `PP = SBP − DBP`, and `MAP = DBP + (SBP − DBP)/3`.

**Generated figure sheets (8):**

| File in `figures/multipanels/` | Contents |
|---|---|
| `01_age_anthropometry_distribution_profiles.png` | Age, weight, height and BMI distributions |
| `02_blood_pressure_distribution_profiles.png` | SBP, DBP, MAP and PP distributions |
| `03_non_hrv_qq_diagnostics.png` | Eight Q–Q diagnostics |
| `04_non_hrv_tukey_tails.png` | IQR tail frequencies |
| `05_non_hrv_spearman_heatmap.png` | Continuous non-HRV correlation matrix |
| `06_age_anthropometry_pair_grid.png` | Age/anthropometry relationships |
| `07_blood_pressure_pair_grid.png` | Blood-pressure relationships |
| `08_age_anthropometry_pressure_cross_domain.png` | Selected cross-domain descriptive relationships |

**Tables:** `non_hrv_descriptive_statistics.csv`, `height_m_alternative_summary.csv`, `non_hrv_tukey_tail_summary.csv`, `non_hrv_spearman_pairs.csv`, `non_hrv_spearman_matrix.csv`, `non_hrv_representation_checks.csv`, and `figure_manifest.csv`.

### Main descriptive observations

| Measure | Median | Mean | Sample skewness |
|---|---:|---:|---:|
| Age (years) | 70.0 | 70.85 | 0.58 |
| Weight (kg) | 74.25 | 75.80 | 0.73 |
| Height (cm) | 156.0 | 156.43 | 0.12 |
| BMI (kg/m²) | 30.01 | 31.03 | 0.76 |
| SBP (mmHg) | 129.0 | 130.57 | 0.24 |
| DBP (mmHg) | 77.0 | 77.69 | 0.24 |
| MAP/PAM (mmHg) | 94.33 | 95.32 | 0.27 |
| Pulse pressure (mmHg) | 51.0 | 52.89 | 0.31 |

The arithmetic representation checks covered all 402 records and found **zero differences beyond the documented tolerances**. Strong rank correlations involving BMI and weight/height, or MAP/PP and SBP/DBP, partly follow from the variables' definitions. For example, weight–BMI has ρ = 0.789, DBP–MAP has ρ = 0.904, and SBP–MAP has ρ = 0.838. They should not be reported as independent evidence of physiological effects.

---

## 05.03 — Inspection of categorical factors and counts

### Factors and fixed levels

| Factor | Dataset column | Documented levels |
|---|---|---|
| Age | `age_group` | `60-69`, `70-79`, `80+` |
| Sex | `sex_category` | `Female`, `Male` |
| BMI | `bmi_category` | `Underweight`, `Normal`, `Overweight`, `Obesity` |
| Systolic BP | `sbp_category` | `<140 mmHg`, `>=140 mmHg` |
| Diastolic BP | `dbp_category` | `<90 mmHg`, `>=90 mmHg` |

The sex source coding is **1 = Female**, **2 = Male**. In the retained cohort, BMI categories use the documented older-person thresholds: 23, 28 and 32 kg/m². SBP and DBP categories are independent measurement categories, not diagnoses. `bmi_reference` is recorded as provenance metadata, not treated as an additional factor. All 402 records have all five factor labels present; source-versus-category consistency checks reported **zero mismatches**.

### Methods and counting conventions

- Marginal counts and proportions for each factor, including explicit missing-category counts.
- **All 10 distinct pairs** among the five factors, with every possible category combination listed, including **n = 0** cells.
- For each pair: joint count and joint percentage, row percentage conditional on the first/vertical category, and column percentage conditional on the second/horizontal category.
- Counts of occupied and empty combinations, plus descriptive summaries of occupied cell sizes. Cells containing 1–4 records are reported as such, **not** automatically rejected or classified as non-estimable.
- No three-or-more-factor intersections, HRV subgroup outcomes, significance tests, or factorial models.

In cross-tabulation heatmaps, the **cell color represents the absolute count** and the percentage printed in the cell is the **within-row percentage**. Percentages from different rows have different denominators. Counts from different cross-tabulations represent overlapping observations and must not be added together.

**Generated figure sheets (5):**

| File in `figures/multipanels/` | Contents |
|---|---|
| `01_category_marginal_counts.png` | Five marginal distributions and smallest category per factor |
| `02_demography_anthropometry_crosstabs.png` | Age × sex, age × BMI, sex × BMI |
| `03_blood_pressure_bmi_crosstabs.png` | SBP × DBP, BMI × SBP, BMI × DBP |
| `04_demographic_bp_crosstabs.png` | Age/sex × SBP/DBP (four cross-tabulations) |
| `05_pairwise_cell_support.png` | Occupied/empty combinations and descriptive cell-size support |

**Tables:** `categorical_marginal_counts.csv`, `bmi_reference_metadata_counts.csv`, `categorical_source_consistency_checks.csv`, `categorical_pairwise_cell_counts.csv`, `categorical_pairwise_support_summary.csv`, and `figure_manifest.csv`.

### Main descriptive observations

| Factor | Category | n | % of 402 |
|---|---|---:|---:|
| Age | 60–69 | 184 | 45.8 |
| Age | 70–79 | 190 | 47.3 |
| Age | 80+ | 28 | 7.0 |
| Sex | Female | 294 | 73.1 |
| Sex | Male | 108 | 26.9 |
| BMI | Underweight | 17 | 4.2 |
| BMI | Normal | 118 | 29.4 |
| BMI | Overweight | 117 | 29.1 |
| BMI | Obesity | 150 | 37.3 |
| SBP | <140 mmHg | 298 | 74.1 |
| SBP | ≥140 mmHg | 104 | 25.9 |
| DBP | <90 mmHg | 354 | 88.1 |
| DBP | ≥90 mmHg | 48 | 11.9 |

All 10 factor pairs were tabulated. Two pair-specific combinations are empty: **BMI Underweight × DBP ≥90 mmHg** and **Age 80+ × DBP ≥90 mmHg**. Four observed cells across the ten pairs contain fewer than five records: age 80+ × underweight (1), male × underweight (2), underweight × SBP ≥140 (3), and age 80+ × obesity (4). These observations describe sample composition only; they do not justify removing records, merging levels, or declaring statistical non-estimability.

---

## Cross-notebook integrity and outputs at a glance

| Property | 05.01 HRV | 05.02 Non-HRV | 05.03 Categories |
|---|---:|---:|---:|
| Input source records | 402 | 402 | 402 |
| Analyzed main variables/factors | 7 | 8 | 5 |
| Missing in analyzed variables/factors | 0 | 0 | 0 |
| Multipanel figure sheets | 9 | 8 | 5 |
| Generated CSV tables (including figure manifest) | 6 | 7 | 6 |
| Generated Markdown documents | 2 | 2 | 2 |
| Filtering, source-data modification or imputation | None | None | None |
| HRV-versus-factor comparisons | None | None | None |

**Stage total:** **22 multipanel figure sheets**, **19 CSV tables** (including three figure manifests), and **six generated notebook-specific Markdown documents**, all organized by inspection domain.

Each notebook's `tables/figure_manifest.csv` is the authoritative inventory of its figures. On reruns, the scripts replace only previously generated figure files listed by their own manifests, leaving unlisted files untouched.

## Reproducibility

The three generated notebook READMEs record the same execution environment:

| Dependency | Recorded version |
|---|---|
| Python | 3.14.10 |
| pandas | 3.1.1 |
| NumPy | 2.5.5 |
| SciPy | 1.18.1 (used by 05.01 and 05.02) |
| Matplotlib | 3.11.2 |
| Seaborn | 0.13.2 |

Jupyter/IPython is required to run the notebooks. The package versions above document the reported runtime; they are not a lockfile or guarantee of compatibility with other environments.

For reproducible review, check the dataset SHA-256, run the three notebooks against the same `dataset_final.csv`, and consult each notebook's generated README, result report, numeric tables and figure manifest. The notebooks do not modify the source CSV.

## Interpretation boundaries and stage closure

1. **Descriptive scope only.** Histograms, ECDFs, Q–Q plots, skewness, correlations, and category frequencies characterize the observed cohort. They do not establish clinical abnormality, causation, subgroup effects, or explanatory relationships between HRV and external factors.
2. **No distribution-based exclusions.** Long tails and observations outside Tukey fences are retained. The source HRV values, including SDNN, are not corrected or invalidated on the basis of distribution shape.
3. **Mathematical dependencies matter.** Mean RR/mean HR, weight/height/BMI, and SBP/DBP/MAP/PP should not be interpreted as collections of unrelated measurements.
4. **Unequal groups are part of the observed cohort.** Categories with low n and empty pairwise combinations are documented. This stage does not merge categories, create artificial records, or attempt to decide statistical estimability.
5. **Record-level provenance is limited.** `source_excel_row` supports traceability to the input spreadsheet but does not independently verify identities or the original measurements. The upstream preprocessing decision governs inclusion.
6. **Presentation polish does not change results.** Minor points identified during review include an outdated `figures/relationships/` path mentioned in the 05.01 results narrative (actual figures are in `figures/multipanels/`), crowded labels in the 05.02 correlation heatmap, and the need to keep count-versus-row-percentage conventions explicit when reading 05.03 heatmaps. None affects the numerical tables.

**Stage status:** Completed and reviewed for its intended descriptive purpose, using the documented 402-record analytical cohort. Further work should begin with **individual HRV-versus-factor associations**, deciding later whether specific two-factor subgroup profiles are scientifically worthwhile. No three- or four-factor analysis, machine learning, or clustering is prescribed by this inspection stage.

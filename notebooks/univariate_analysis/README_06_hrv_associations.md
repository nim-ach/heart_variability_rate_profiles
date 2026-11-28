# Stage 06 — Individual HRV Associations

## Purpose and status

**Status:** Completed and methodologically reviewed (28 November 2026).

Stage 06 characterizes individual, unadjusted associations between cardiac measurements and demographic, anthropometric, and blood-pressure factors in the finalized older-adult cohort from southern Chile. Its purpose is **descriptive and inferential characterization**, emphasizing observed distributions, effect sizes, confidence intervals, and transparent reporting of uncertainty. Statistical significance is not a prerequisite for a useful result.

This stage comprises four notebooks: three domain-specific analyses and a fourth notebook that integrates their existing outputs. It does **not** implement predictive models, clustering, multivariable adjustment, interactions, or automatically selected two-factor subgroups.

## Data and analytical scope

- **Input:** `outputs/dataset_final.csv`, produced by the accepted preprocessing workflow (Stages 01–04) and inspected in Stage 05.
- **Final analysis sample:** 402 source records; all seven cardiac measurements and the factors used in this stage were complete in the analyzed dataset.
- **Record identifier:** `source_excel_row`. It identifies source records and is not, on its own, proof of unique participants across projects.
- **Data handling:** Original values, recorded factor categories, and cohort membership are retained. The source CSV is read, not edited. No new exclusions, imputation, category merging, or inferential transformations are introduced.
- **Provenance:** Run metadata may record the analyzed file and its SHA-256 checksum. No particular checksum value or fixed subgroup counts are required to run the statistical analysis.

### Outcomes

| Role | Measurement | Source column |
| --- | --- | --- |
| Primary HRV | SDNN | `t2m_pre_sdnn` |
| Primary HRV | RMSSD | `t2m_pre_rmssd` |
| Primary HRV | HF | `t2m_pre_hf` |
| Primary HRV | LF | `t2m_pre_lf` |
| Primary HRV | VLF | `t2m_pre_vlf` |
| Complementary cardiac | Mean RR | `t2m_pre_mean_rr` |
| Complementary cardiac | Mean HR | `t2m_pre_mean_hr` |

The five primary HRV measurements and two complementary cardiac measurements are analyzed individually. Mean RR and Mean HR are intrinsically related and do not provide independent physiological confirmation of one another.

### Factors

| Domain | Continuous factors | Existing categorical factors |
| --- | --- | --- |
| Demographic | Age (`age`) | Sex (`sex_category`: Female, Male); age group (`age_group`: 60–69, 70–79, 80+) |
| Anthropometric | Weight (`weight_kg`); height (`height_cm`); BMI (`imc`) | BMI group (`bmi_category`: Underweight, Normal, Overweight, Obesity) |
| Blood pressure | SBP (`bp_systolic`); DBP (`bp_diastolic`); MAP/PAM (`bp_pam`); pulse pressure (`bp_pp`) | SBP group (`sbp_category`: <140 / ≥140 mmHg); DBP group (`dbp_category`: <90 / ≥90 mmHg) |

The BMI categories follow the previously accepted MINSAL older-adult classification. Blood-pressure categories retain the definitions established in preprocessing. BMI derives from weight and height; MAP and pulse pressure derive from SBP and DBP. Their individual analyses describe related views of the same observations, not independent evidence.

## Notebook workflow

Execute the first three notebooks on the final source dataset, then execute the synthesis notebook using their exported tables.

| Order | Notebook | Questions addressed | Figures generated |
| --- | --- | --- | ---: |
| 1 | `06_01_demographic_associations.ipynb` | HRV versus sex, continuous age, and age groups | 9 |
| 2 | `06_02_anthropometric_associations.ipynb` | HRV versus weight, height, continuous BMI, and nutritional groups | 12 |
| 3 | `06_03_blood_pressure_associations.ipynb` | HRV versus SBP, DBP, MAP, pulse pressure, and SBP/DBP groups | 15 |
| 4 | `06_04_association_synthesis.ipynb` | Cross-domain summaries of *previously estimated* associations and uncertainty | 9 |
| | **Total** | | **45** |

### Analytical methods

**Descriptive profiles.** For the full cohort and recorded factor categories, the notebooks report sample size, mean, standard deviation, median, 5th/25th/75th/95th percentiles, and interquartile range. These summaries remain in original measurement units, including for non-significant comparisons.

**Continuous factors.** Each factor–outcome pair is analyzed using Spearman's rank correlation (ρ), a two-sided p-value, and a paired percentile-bootstrap 95% confidence interval. Scatterplots and optional LOWESS smoothers provide descriptive views of relationship shape. Age additionally has an optional Kendall tau-b sensitivity summary; this is not counted as another independent primary finding.

**Two-category factors.** Sex and SBP/DBP categories are compared using two-sided Mann–Whitney U, Cliff's delta, observed difference in group medians, and bootstrap 95% confidence intervals. The directions are explicitly defined:

- Sex: **Female minus Male** (`comparison = Female`, `reference = Male`).
- SBP: **≥140 minus <140 mmHg**.
- DBP: **≥90 minus <90 mmHg**.

Mann–Whitney assesses rank distributions; it is not automatically a test of equality of medians. A median-difference interval and a rank-based test can consequently convey different information.

**Three-or-more-category factors.** Age groups and BMI categories use Kruskal–Wallis H with a nonnegative rank-based epsilon-squared effect estimate, calculated as `max(0, (H - k + 1) / (n - k))`, and a within-group percentile-bootstrap 95% confidence interval. Epsilon squared is non-directional and is not directly comparable to Spearman's ρ or Cliff's delta.

**Exploratory pairwise contrasts.** The age-group analysis includes the three possible age-group pairs; the BMI analysis compares Underweight, Overweight, and Obesity separately against Normal. These use Mann–Whitney, Cliff's delta, observed median differences, bootstrap intervals, and Holm adjustment within each outcome. They are reported as exploratory comparisons, including when the omnibus result is not significant.

**Uncertainty settings.** The documented runs used 1,500 bootstrap replicates, a 95% confidence level, and random seed `20261128`. Bootstrap resampling is performed at the source-record level, with within-category resampling for group comparisons.

### Multiple-testing policy — important

Benjamini–Hochberg false discovery rate (BH-FDR) adjustment is performed **separately within each scientific domain**. Within each domain, primary HRV outcomes and complementary cardiac outcomes form **separate testing families**:

| Domain | Primary HRV tests | Complementary cardiac tests |
| --- | ---: | ---: |
| Demographic | 15 | 6 |
| Anthropometric | 20 | 8 |
| Blood pressure | 30 | 12 |
| **Total across domains** | **65** | **26** |

These totals describe the complete collection, **not pooled FDR families**. The resulting q-values control multiplicity within the specified domain/outcome family only. They must **not** be presented as study-wide FDR-adjusted values. The synthesis notebook copies original q-values unchanged and performs no additional FDR adjustment. Exploratory age- and BMI-group pairs are handled separately using Holm correction within each outcome.

Full-precision numeric p-values and q-values are retained in CSV exports. Markdown result reports display values below 0.001 as `p < 0.001` or `q < 0.001`; this is display formatting only.

## Outputs and directory structure

The notebooks write into their own folders under `eda_results/hrv_associations/`. Each domain notebook generates its own Markdown methodological README, Markdown results report, numeric tables, and multipanel PNG figures. Stage 06.04 reads the domain CSVs and does not overwrite them.

```text
eda_results/hrv_associations/
├── README_06_hrv_associations.md     # This stage-level README
├── 06_01_demographic_associations/
│   ├── README_06_01_demographic_associations.md
│   ├── 06_01_demographic_associations_results.md
│   ├── tables/
│   └── figures/multipanels/          # 9 figures
├── 06_02_anthropometric_associations/
│   ├── README_06_02_anthropometric_associations.md
│   ├── 06_02_anthropometric_associations_results.md
│   ├── tables/
│   └── figures/multipanels/          # 12 figures
├── 06_03_blood_pressure_associations/
│   ├── README_06_03_blood_pressure_associations.md
│   ├── 06_03_blood_pressure_associations_results.md
│   ├── tables/
│   └── figures/multipanels/          # 15 figures
└── 06_04_association_synthesis/
    ├── README_06_04_association_synthesis.md
    ├── 06_04_association_synthesis_results.md
    ├── tables/
    └── figures/multipanels/          # 9 figures
```

### Main CSV outputs

| Notebook | Table files in its `tables/` directory |
| --- | --- |
| 06.01 | `demographic_hrv_descriptive_profiles.csv`, `age_continuous_spearman.csv`, `sex_comparisons.csv`, `age_group_kruskal_wallis.csv`, `multiplicity_fdr_bh.csv`; optional `age_group_pairwise_exploratory.csv`, `age_continuous_kendall_sensitivity.csv`; plus `run_provenance.csv`, `figure_manifest.csv` |
| 06.02 | `bmi_category_hrv_descriptive_profiles.csv`, `anthropometric_continuous_spearman.csv`, `bmi_category_kruskal_wallis.csv`, `anthropometric_multiplicity_fdr_bh.csv`; optional `bmi_category_vs_normal_exploratory.csv`; plus `run_information.csv`, `figure_manifest.csv` |
| 06.03 | `blood_pressure_category_hrv_descriptive_profiles.csv`, `blood_pressure_continuous_spearman.csv`, `blood_pressure_category_mann_whitney.csv`, `blood_pressure_multiplicity_fdr_bh.csv`, `run_information.csv`, `figure_manifest.csv` |
| 06.04 | `continuous_associations_combined.csv`, `binary_cliffs_delta_combined.csv`, `multigroup_epsilon_squared_combined.csv`, `categorical_descriptive_profiles_combined.csv`, `all_association_effects_long.csv`, `continuous_ci_widths.csv`, `figure_manifest.csv`; optional `exploratory_contrasts_combined.csv` |

The synthesis inventory contains **56 continuous associations** (8 factors × 7 outcomes), **21 binary comparisons** (3 factors × 7 outcomes), **14 multigroup omnibus effects** (2 factors × 7 outcomes), **98 category-profile rows**, and **42 exploratory pairwise contrasts** in the reviewed execution. The synthesis preserves the estimates, confidence intervals, and multiplicity results from the source notebooks.

## Visualization conventions

- Figures are produced with **Seaborn and Matplotlib** as high-resolution multipanel sheets, using scatterplots, descriptive LOWESS curves, violin/box distributions, ECDFs, heatmaps, and forest plots as applicable.
- Overall titles, subtitles, panel headings, legends, and annotations use separated layout regions to avoid text overlap.
- HF, LF, and VLF may use an explicitly labeled `log1p` scale **for visualization only**, when supported by the observed values. All statistical tests, estimates, and exported source-unit summaries use the original data.
- `figure_manifest.csv` indexes the generated PNGs. The notebooks do not require individual redundant plots for each estimate.

## Execution and reproducibility

1. Place the notebooks in the project working environment and make `outputs/dataset_final.csv` discoverable from the project directory. When necessary, set the source-path override in the notebook's configuration cell.
2. Execute `06_01`, `06_02`, and `06_03`, allowing each notebook to write only into its own results directory.
3. Execute `06_04` after the preceding outputs exist. It reads their tables from `eda_results/hrv_associations/`; set `ANALYSIS_ROOT_OVERRIDE` if the analysis root differs.
4. Review the four generated result reports and use the exact CSV values for downstream statistical reporting.

The reviewed runtime used Python 3.14.10, pandas 3.1.1, NumPy 2.5.5, SciPy 1.18.1, Seaborn 0.13.2, and Matplotlib 3.11.2. Jupyter/IPython is required to run the notebooks. `statsmodels` is used when available for optional LOWESS visualization. The stage makes no claim that these are the only compatible package versions.

## Interpretation and limitations

- All comparisons are **individual, marginal, observational, and unadjusted**; none establishes causality or within-participant longitudinal change.
- Spearman summarizes **monotonic rank associations** and does not capture every possible non-monotonic relationship. Descriptive smoothers are not inferential regression models.
- Rank-based tests characterize distributional differences; they must not automatically be interpreted as tests of median equality. Differences of medians, Cliff's delta, and epsilon squared answer related but distinct questions.
- A small estimate or non-significant test does not establish zero effect or physiological equivalence. Interpret effect magnitude together with interval width and available group sample size.
- The analysis assumes independent source records for inference and bootstrap intervals. Correlation between observations from the same original project, if present, is not modeled; `source_excel_row` is a record identifier, not verified participant independence.
- Primary HRV measures are physiologically distinct. The attribution of HF/LF/VLF to specific mechanisms depends on the original acquisition and spectral-analysis protocol. LF is not assumed to be a unique measure of sympathetic activity.
- Derived and intrinsically related measurements (BMI/weight/height, MAP/PP/SBP/DBP, Mean RR/Mean HR) do not provide independent confirmations of each other.
- No new analyses are required solely because few associations reach statistical significance. Two-factor profiles or adjusted models belong to a separate, explicitly justified study phase if pursued.

## Review and closure record

The updated real-data outputs in the reviewed Stage 06 results folder were compared with the preceding execution. The seven sex contrasts now correctly identify `comparison = Female` and `reference = Male`; their numeric effects and intervals remain unchanged. Other reviewed statistical CSV tables retained their prior values. The reports display small p/q values correctly, and the domain-specific FDR policy is described in the four methodological READMEs.

The reviewed figure inventory contained **45 expected multipanel PNGs** (9 + 12 + 15 + 9). The cross-domain synthesis retained the existing domain estimates without recalculation. This review establishes **consistency of generated outputs and the approved corrections**; it is not an independent statistical recomputation of every estimator from the 402 source rows.

**Stage 06 is closed** for the approved individual-factor characterization. Its outputs are ready to support manuscript-level interpretation and a separate decision on whether any scientifically motivated two-factor analyses are warranted.

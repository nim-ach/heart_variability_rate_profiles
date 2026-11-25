# HRV Dataset Preprocessing

## Methodological README

**Project:** Heart rate variability and physiological profiling in older adults from southern Chile  
**Workflow:** `01_cleaning_and_validation.ipynb` → `02_variable_categorization.ipynb` → `03_final_cohort_construction.ipynb` → `04_preprocessing_summary.ipynb`  
**Starting source:** `Basal v2.xlsx`  
**Version documented:** the four supplied notebooks, reviewed on 25 November 2026  
**Status:** descriptive documentation of the implemented preprocessing workflow and the results reproduced from its CSV outputs

---

## 1. Purpose and analytical scope

This preprocessing workflow converts the original Excel workbook into a traceable, consistently typed, categorized, and quality-filtered analytical dataset for subsequent characterization of heart rate variability (HRV).

The intended downstream design considers **age, sex, blood pressure, and body mass index (BMI) as explanatory factors**, initially in isolation and subsequently in factorial combinations. HRV measurements will serve as the outcomes of interest in later exploratory and statistical analyses. This workflow does **not** identify healthy or diseased participants, assign clinical diagnoses, discover clusters, or perform inferential HRV comparisons.

The four notebooks implement distinct operations:

1. **Cleaning and validation:** normalize numeric formats; verify derived measurements; flag unresolved inconsistencies; retain every original observation.
2. **Variable categorization:** construct categorical factors using documented operational definitions, including Chilean Ministry of Health (MINSAL) classifications.
3. **Final cohort construction:** apply explicit row-level exclusion rules and preserve *every applicable reason* for excluding a record.
4. **Preprocessing summary:** verify consistency between outputs and summarize processing, category frequencies, exclusions, and factorial cell sizes without modifying data.

**Record identity and terminology.** The input contains 530 rows. Each source row is treated as a participant record in the supplied notebooks. The workflow does not independently confirm participant identities or unique subject identifiers. Therefore, “participants” in its output refers operationally to input records; exact HRV matches are assessed separately.

### 1.1 Overall results

| Processing stage | Records | Columns | Main result |
|---|---:|---:|---|
| Original Excel, first sheet | 530 | 20 | Source workbook; no age-based filtering |
| 01 — Cleaned | 530 | 19 | Two empty export columns removed; provenance ID added; numeric checks and corrections |
| 02 — Categorized | 530 | 24 | Five categorical/reference columns added; no records removed |
| 03 — Retained | **476** | **24** | Analysis-ready sample under implemented eligibility and QC rules |
| 03 — Excluded | **54** | **26** | All original categorized columns plus exclusion count and reasons |
| 04 — Summary | — | — | Consolidated methodological and count report; no data changes |

**Final retention:** 476/530 = **89.8%**.  
**Total exclusions:** 54/530 = **10.2%**.

The final retained records are aged **60–89 years**. The ten input records younger than 60 were retained during cleaning and categorization and excluded only in Notebook 03.

---

## 2. Input data and provenance

### 2.1 Original data

The first worksheet of `Basal v2.xlsx` contains 530 rows and 20 columns. The recorded domains are:

| Domain | Source variables | Interpretation for preprocessing |
|---|---|---|
| Demographic | `age`, `sex` | Age in years; sex coded numerically |
| HRV / cardiac interval | `t2m_pre_mean_rr`, `t2m_pre_mean_hr` | Mean RR interval and mean heart rate |
| Time-domain HRV | `t2m_pre_sdnn`, `t2m_pre_rmssd` | SDNN and RMSSD |
| Frequency-domain HRV | `t2m_pre_hf`, `t2m_pre_lf`, `t2m_pre_vlf` | HF, LF, VLF |
| Blood pressure | `bp_systolic`, `bp_diastolic`, `bp_pam`, `bp_pp` | Systolic, diastolic, mean arterial and pulse pressure |
| Anthropometry | `weight kg`, `height cm`, `height mt`, `imc` | Weight, height in two units and recorded BMI |
| Export/administrative | `Unnamed: 0`, `Unnamed: 1`, `redcap_repeat_instrument` | Spreadsheet export fields |

Original units are preserved; the workflow does not rescale HRV measurements. The numerical thresholds used in validation assume mean RR in milliseconds, mean HR in beats/minute, blood pressure in mmHg, weight in kilograms, and height in centimeters/meters.

### 2.2 Provenance tracking

Notebook 01 inserts `source_excel_row` using the original Excel row number, with the header at row 1. Source records are therefore referenced as **Excel rows 2–531**, inclusive. All downstream datasets retain this unique source-row identifier.

The original workbook is not overwritten. A separate processing log stores source values, processed values, correction actions, and row-level details when applicable.

### 2.3 Notebook and file layout

The supplied notebooks use the following paths relative to their location:

```text
project/
├── raw_data/
│   └── Basal v2.xlsx
├── notebooks/
│   └── preprocessing/
│       ├── 01_cleaning_and_validation.ipynb
│       ├── 02_variable_categorization.ipynb
│       ├── 03_final_cohort_construction.ipynb
│       └── 04_preprocessing_summary.ipynb
└── outputs/
    ├── dataset_clean.csv
    ├── data_errors.csv
    ├── error_examples.csv
    ├── dataset_categorized.csv
    ├── dataset_final.csv
    ├── dataset_excluded.csv
    └── preprocessing_summary.md
```

The paths in the notebooks are configurable; placing them in a different directory requires updating the corresponding `INPUT_FILE`, `OUTPUT_FILE`, or `OUTPUT_DIR` constants.

---

## 3. Notebook 01 — Cleaning and validation

**Input:** `raw_data/Basal v2.xlsx`  
**Outputs:** `dataset_clean.csv`, `data_errors.csv`, `error_examples.csv`  
**Record exclusions:** none.

### 3.1 Structural processing

- Read the first Excel worksheet with `pandas.read_excel()` and retain an unmodified in-memory copy for audit examples.
- Add `source_excel_row` to trace processed rows back to the original workbook.
- Remove the two completely empty export columns `Unnamed: 0` and `Unnamed: 1`.
- Retain the administrative `redcap_repeat_instrument` field as implemented in the notebook; it is not used as an analytical factor or exclusion rule.
- Rename only three measurement columns to code-friendly identifiers:

| Original column | Processed column |
|---|---|
| `weight kg` | `weight_kg` |
| `height cm` | `height_cm` |
| `height mt` | `height_m` |

All seven HRV variables, demographic variables, and blood-pressure values retain their original variable names. After two columns are removed and `source_excel_row` is added, the processed dataset contains **19 columns**.

### 3.2 Numeric format normalization

The notebook processes the 17 expected numeric columns (`sex`, `age`, seven HRV measurements, four blood-pressure measures, `imc`, `weight_kg`, `height_cm`, and `height_m`). For object-typed columns, it removes whitespace from character representations, replaces comma decimal separators with periods, and applies `pandas.to_numeric(..., errors="coerce")`.

- **97** entries in `t2m_pre_rmssd` were recognized as numeric values stored as text and converted.
- **8** RMSSD values remained missing; these were **not imputed**.
- Any text value that cannot be parsed numerically is converted to missing and logged for review under the `unparseable_number` rule. No such occurrences appear in the reproduced issue-count table.

### 3.3 Height consistency and correction

For rows with valid height measurements in both units, the expected relationship is:

$$
\mathrm{height_m}=\frac{\mathrm{height_{cm}}}{100}.
$$

**Rule:** if both measurements are positive and their absolute difference exceeds **0.01 m**, the value calculated from `height_cm` replaces `height_m`. When meters are missing but centimeters are positive, meters are reconstructed. Conversely, when centimeters are missing but meters are positive, centimeters are reconstructed.

**Observed results:**

- **9 height disagreements** were corrected using `height_cm` as the reference.
- The nine corrections involve original Excel rows **446–454**.
- The notebook records the original and replacement values under `height_disagreement` / `corrected_from_height_cm`.

The procedure assumes that centimeters are the authoritative source when two nonmissing height fields disagree. This assumption is explicit in the code; it is not an independent verification against clinical source documents.

### 3.4 BMI verification and correction

BMI is calculated as:

$$
\mathrm{BMI}=\frac{\mathrm{weight_{kg}}}{\mathrm{height_m}^{2}}.
$$

With positive weight and height, an existing `imc` value is replaced when its absolute difference from the recalculated BMI exceeds **0.10 kg/m²**. Missing BMI values are calculated when the two input variables are available.

**Observed results:**

- **9 BMI disagreements** were corrected.
- They occur in the same original Excel rows **446–454**, after the height correction.
- The action is logged as `recomputed_from_weight_and_height`.

No BMI category is assigned at this stage. The recalculated continuous BMI remains in `imc` and is used for categorization in Notebook 02.

### 3.5 Blood-pressure derivative verification

The notebook verifies deterministic relationships using positive systolic and diastolic pressures and **SBP > DBP**:

$$
\mathrm{PP}=\mathrm{SBP}-\mathrm{DBP}
$$

$$
\mathrm{MAP}=\mathrm{DBP}+\frac{\mathrm{SBP}-\mathrm{DBP}}{3}.
$$

The implemented tolerances for recomputing an existing stored value are:

| Derived measure | Column | Correction tolerance |
|---|---|---:|
| Pulse pressure | `bp_pp` | Absolute difference > **0.01 mmHg** |
| Mean arterial pressure | `bp_pam` | Absolute difference > **0.10 mmHg** |

Missing derivatives are filled where both source pressures satisfy the validity conditions. **The reproduced run required no MAP or PP corrections**. These fields remain in the processed CSV; deciding which redundant predictors to use is outside this preprocessing stage.

### 3.6 Missing data and range screening

The notebook records missing values for expected quantitative columns but does not impute them or exclude their rows. The recorded **20 missing quantitative cells** comprise:

| Measurement | Missing cells |
|---|---:|
| `t2m_pre_sdnn` | 11 |
| `t2m_pre_rmssd` | 8 |
| `t2m_pre_mean_hr` | 1 |
| **Total** | **20** |

The basic screening intervals below are **review flags, not automatic exclusion thresholds**:

| Column | Inclusive screening interval |
|---|---|
| `age` | 18–100 years |
| `weight_kg` | 30–250 kg |
| `height_cm` | 120–230 cm |
| `height_m` | 1.2–2.3 m |
| `imc` | 10–80 kg/m² |
| `bp_systolic` | 70–250 mmHg |
| `bp_diastolic` | 40–150 mmHg |
| `t2m_pre_mean_hr` | 30–220 beats/min |
| `t2m_pre_mean_rr` | 250–2000 ms |
| `t2m_pre_sdnn` | 0–500, in source units |
| `t2m_pre_rmssd` | 0–500, in source units |
| `t2m_pre_hf`, `t2m_pre_lf`, `t2m_pre_vlf` | 0–100,000, in source units |

**Observed:** 14 `outside_review_range` entries, consisting of **10 mean RR**, **3 SDNN**, and **1 mean HR** values. Their source values were retained at this stage. The notebook also checks for SBP ≤ DBP; no such event appears in the reproduced issue log.

### 3.7 Mean HR and mean RR agreement

For positive observed mean HR and mean RR, the notebook calculates:

$$
\mathrm{HR_{estimated}}=\frac{60,000}{\mathrm{mean\ RR\ (ms)}}
$$

$$
R=\frac{\mathrm{HR_{observed}}\times\mathrm{mean\ RR}}{60,000}.
$$

A discrepancy is logged when the difference between observed and estimated HR exceeds **2 beats/min**. A potential tenfold pattern is identified when the ratio \(R\) is approximately 0.1 or 10 within **3% relative tolerance** (`numpy.isclose` with `rtol=0.03`).

**Observed:**

- **11** entries classified as `hr_rr_factor_10_pattern`.
- **4** entries classified as `hr_rr_disagreement` without a tenfold signature.
- **15** flagged records in total.

The notebook **does not automatically change HR or RR**. The 2-bpm rule is a screening threshold; Notebook 03 applies a stricter exclusion rule. Because an average of instantaneous heart rates need not equal the reciprocal of an average RR interval exactly, modest differences are not automatically treated as transcription errors.

### 3.8 Identical HRV profiles and full-row duplicates

The notebook checks exact equality across all **seven original HRV measurements**:

1. `t2m_pre_mean_rr`
2. `t2m_pre_mean_hr`
3. `t2m_pre_sdnn`
4. `t2m_pre_rmssd`
5. `t2m_pre_hf`
6. `t2m_pre_lf`
7. `t2m_pre_vlf`

Only rows complete across these seven variables are eligible for this comparison. When two or more records match exactly, **each matching row** receives an `identical_hrv_profile` log entry with the matching Excel row numbers.

**Observed:** **8 matching pairs**, comprising **16 records**. Separately, exact duplication across the complete original nonempty dataset is checked; no full-row duplicate event was logged.

Exact equality is a data-integrity concern, **not proof that records represent the same participant**. The original clinical recordings would be needed to resolve which, if any, record is incorrect.

### 3.9 Notebook 01 outputs and issue accounting

| Output | Content |
|---|---|
| `dataset_clean.csv` | All 530 processed records; 19 columns; numeric corrections applied; no row exclusion |
| `data_errors.csv` | One entry per detected anomaly, correction, or structural note, including original value, processed value, action, source row, and explanation |
| `error_examples.csv` | Unmodified original workbook rows for records that have row-level issues, plus issue labels and selected processed comparison values |

The reproduced run generated **183 issue-log entries** affecting **155 distinct source records**. Three entries are dataset-level structural/metadata notes without a participant row. The issue counts are:

| Issue code | Entries | Processing action |
|---|---:|---|
| `text_number` | 97 | Converted numeric text to numeric |
| `missing_value` | 20 | Left missing; no imputation |
| `identical_hrv_profile` | 16 | Flagged, left unchanged |
| `outside_review_range` | 14 | Flagged, left unchanged |
| `hr_rr_factor_10_pattern` | 11 | Flagged, left unchanged |
| `height_disagreement` | 9 | Corrected from centimeters |
| `bmi_disagreement` | 9 | Recomputed from weight and height |
| `hr_rr_disagreement` | 4 | Flagged, left unchanged |
| `empty_column` | 2 | Removed empty export columns |
| `empty_metadata_column` | 1 | Retained field; dataset-level note |
| **Total** | **183** | **Not 183 unique participants** |

The output contains **155 original example rows**, corresponding to the 155 source rows with logged issues. Rows may carry multiple issues.

---

## 4. Notebook 02 — Variable categorization

**Input:** `outputs/dataset_clean.csv` (**530 rows, 19 columns**)  
**Output:** `outputs/dataset_categorized.csv` (**530 rows, 24 columns**)  
**Record exclusions:** none.

The notebook appends the four analytical factors `age_group`, `sex_category`, `bp_category`, and `bmi_category`, plus `bmi_reference` to record which BMI classification was used. It preserves all prior columns and numerical measurements. A participant below age 60 receives categories at this stage and is not excluded until Notebook 03.

### 4.1 Age groups

Age categories are operational study bands, not a MINSAL-prescribed four-bin classification:

| `age_group` | Age rule | Categorized records | Final retained |
|---|---|---:|---:|
| `<60` | Age < 60 | 10 | 0 |
| `60-69` | 60 ≤ age < 70 | 219 | 198 |
| `70-79` | 70 ≤ age < 80 | 261 | 241 |
| `80+` | Age ≥ 80 | 40 | 37 |
| **Total** | | **530** | **476** |

The age-60 boundary reflects the study's older-person population definition. The decade bands are explicitly study-defined.

### 4.2 Sex

The notebook maps the available numeric codes as follows:

| Source `sex` code | `sex_category` | Categorized records | Final retained |
|---|---|---:|---:|
| 1 | Female | 366 | 333 |
| 2 | Male | 164 | 143 |
| **Total** | | **530** | **476** |

The mapping follows the sex distribution used in the original manuscript and is marked in Notebook 02 as requiring confirmation if a primary REDCap codebook becomes available. The original numeric code is retained.

### 4.3 Blood-pressure categories

The notebook implements a six-level scheme attributed to the **MINSAL 2010 primary hypertension guideline**. Systolic and diastolic pressures are assessed jointly; **the higher category determined by either value prevails**. A category is assigned only if both values are nonmissing, DBP > 0, and SBP > DBP.

| `bp_category` | Threshold logic used in code |
|---|---|
| `Optimal` | SBP < 120 **and** DBP < 80 mmHg |
| `Normal` | Highest reached level starts at SBP ≥ 120 **or** DBP ≥ 80 |
| `High-normal` | Highest reached level starts at SBP ≥ 130 **or** DBP ≥ 85 |
| `Stage 1` | Highest reached level starts at SBP ≥ 140 **or** DBP ≥ 90 |
| `Stage 2` | Highest reached level starts at SBP ≥ 160 **or** DBP ≥ 100 |
| `Stage 3` | Highest reached level starts at SBP ≥ 180 **or** DBP ≥ 110 |

These are **mutually exclusive final categories** because the code assigns them in ascending order, overwriting earlier lower-level labels if a higher threshold is reached. A category describes **recorded blood-pressure measurements** and does not establish a clinical diagnosis or treatment status.

| `bp_category` | Categorized records | Final retained |
|---|---:|---:|
| Optimal | 120 | 98 |
| Normal | 131 | 123 |
| High-normal | 118 | 106 |
| Stage 1 | 122 | 111 |
| Stage 2 | 35 | 34 |
| Stage 3 | 4 | 4 |
| **Total** | **530** | **476** |

### 4.4 Age-dependent BMI categories

The workflow applies one set of categorical thresholds to adults under 60 and a different set to records aged 60 or more, recording that choice in `bmi_reference`.

| BMI category | Age < 60 | Age ≥ 60 |
|---|---|---|
| Underweight | BMI < 18.5 | BMI < 23 |
| Normal | 18.5 ≤ BMI < 25 | 23 ≤ BMI < 28 |
| Overweight | 25 ≤ BMI < 30 | 28 ≤ BMI < 32 |
| Obesity | BMI ≥ 30 | BMI ≥ 32 |

The implementation assigns the **older-person BMI thresholds from age 60 onward**, an explicit study-level operational choice following the older-person definition used by its cited national plan. The Notebook 02 notes that the EMPAM assessment is commonly implemented from age 65. This choice is documented so it can be assessed transparently in later sensitivity analyses. Neither continuous BMI nor age is changed during categorization.

| `bmi_category` | Categorized records | Final retained |
|---|---:|---:|
| Underweight | 23 | 21 |
| Normal | 155 | 136 |
| Overweight | 166 | 145 |
| Obesity | 186 | 174 |
| **Total** | **530** | **476** |

`bmi_reference` is `MINSAL adult (<60)` for the 10 under-60 records and `MINSAL older person (>=60)` for the remaining 520. All 476 final retained records use the older-person reference.

### 4.5 Categorization integrity checks

Notebook 02 verifies that the number of rows is unchanged, that the original columns remain in their previous order, and that `source_excel_row` is unique. In the reproduced data, **all four factors were assigned for all 530 records**.

**Normative sources listed in Notebook 02** (included here to document the basis used by the implementation):

- MINSAL (2021), *Plan Nacional de Salud Integral para Personas Mayores*, Table 19: https://www.minsal.cl/wp-content/uploads/2021/11/Plan-Nacional-de-Salud-Integral-para-Personas-Mayores.pdf
- MINSAL, *Guía Clínica 2010 — Hipertensión arterial primaria*, Table 2: https://www.minsal.cl/portal/url/item/7220fdc4341c44a9e04001011f0113b9.pdf
- MINSAL, *Guía del Examen de Medicina Preventiva*: https://diprece.minsal.cl/wrdprss_minsal/wp-content/uploads/2014/09/GPC-Medicina-Preventiva.pdf
- Chile, Decreto 20 (2022): https://www.leychile.cl/navegar?idNorma=1182129

These references are reproduced from the notebook. This README documents the **implemented rules** and does not constitute a new independent clinical-guideline review.

---

## 5. Notebook 03 — Final cohort construction

**Input:** `outputs/dataset_categorized.csv` (**530 rows, 24 columns**)  
**Outputs:** `outputs/dataset_final.csv` (**476 rows, 24 columns**) and `outputs/dataset_excluded.csv` (**54 rows, 26 columns**).

This is the **first stage that removes rows from the retained analytical dataset**. A record is excluded if **any** specified rule is true. Every applicable rule is retained separately; the notebook never stops checking after the first reason.

### 5.1 Explicit inclusion and exclusion rules

| Rule family | Exact operational logic | Result in this dataset |
|---|---|---|
| Age | `age < 60` | 10 records |
| Essential measurement missing | Missing in any of the seven HRV variables | 11 missing SDNN; 8 missing RMSSD; 1 missing HR; some overlap |
| Essential factor missing | Missing age, sex, SBP, DBP, BMI, `age_group`, `sex_category`, `bp_category`, `bmi_category`, or `bmi_reference` | No such missing factors among observed records |
| Invalid sex | Sex not in {1, 2} | None |
| Invalid age | Age < 0 or > 120 | None |
| Invalid BP | SBP ≤ 0, DBP ≤ 0, or SBP ≤ DBP | None |
| Invalid BMI | BMI ≤ 0 | None |
| Invalid mean interval/rate | Mean RR ≤ 0 or mean HR ≤ 0 | None |
| Negative HRV values | SDNN, RMSSD, HF, LF, or VLF < 0 | None |
| Near-tenfold HR/RR discrepancy | `HR * RR / 60000` in [0.097, 0.103] or [9.7, 10.3] | 11 records |
| Other major HR/RR discrepancy | `abs(HR - 60000 / RR) > 10` beats/min, excluding near-tenfold records | 2 records |
| Exactly identical HRV profile | Complete seven-variable HRV vector repeated in another row | 16 records |

The HR/RR cutoff of **10 beats/min** is an operational quality-control rule, **not** a clinical diagnostic definition. It differs deliberately from Notebook 01's **2 beats/min** screening flag: two modestly discordant records were logged for review but did **not** meet the major-discrepancy exclusion rule.

For the exact-HRV-match rule, **both members of each matching pair are provisionally excluded** because this preprocessing cannot determine which record, if any, is valid. No attempt is made to deduplicate by arbitrarily retaining the first or last record.

A participant is **not excluded merely because** of an elevated blood-pressure category, an unusual BMI category, a high/low but nonnegative HRV value, or a Notebook 01 `outside_review_range` warning. No imputation is performed.

### 5.2 Actual exclusion-code frequencies

The frequencies below are calculated from `dataset_excluded.csv`. **Reasons are not mutually exclusive.**

| Exclusion code | Records with reason |
|---|---:|
| `IDENTICAL_HRV_PROFILE` | 16 |
| `HR_RR_FACTOR_10` | 11 |
| `MISSING_T2M_PRE_SDNN` | 11 |
| `AGE_BELOW_60` | 10 |
| `MISSING_T2M_PRE_RMSSD` | 8 |
| `HR_RR_MAJOR_MISMATCH` | 2 |
| `MISSING_T2M_PRE_MEAN_HR` | 1 |
| **Sum of assigned reason codes** | **59** |
| **Unique excluded records** | **54** |

The 59 reason assignments represent 54 unique records because some records meet two criteria:

| Number of exclusion reasons per record | Excluded records |
|---|---:|
| Exactly one | 49 |
| Exactly two | 5 |
| Three or more | 0 |
| **Total** | **54** |

The overlapping cases comprise **four records missing both SDNN and RMSSD**, plus **one record younger than 60 that also has an identical HRV profile**.

### 5.3 Observed combinations of exclusion reasons

| Exact recorded combination | Records |
|---|---:|
| `IDENTICAL_HRV_PROFILE` | 15 |
| `HR_RR_FACTOR_10` | 11 |
| `AGE_BELOW_60` | 9 |
| `MISSING_T2M_PRE_SDNN` | 7 |
| `MISSING_T2M_PRE_RMSSD` | 4 |
| `MISSING_T2M_PRE_SDNN \| MISSING_T2M_PRE_RMSSD` | 4 |
| `HR_RR_MAJOR_MISMATCH` | 2 |
| `AGE_BELOW_60 \| IDENTICAL_HRV_PROFILE` | 1 |
| `MISSING_T2M_PRE_MEAN_HR` | 1 |
| **Total excluded** | **54** |

The separator in `dataset_excluded.csv` is the literal string ` | `. Each excluded row also carries `exclusion_count` to specify how many reasons were assigned.

### 5.4 Output guarantees

- `dataset_final.csv` includes **476 records** and **all 24 original categorized columns**, with no additional data changes.
- `dataset_excluded.csv` includes **54 records**, **all 24 categorized columns**, and two additional fields: `exclusion_count` and `exclusion_reasons`.
- `source_excel_row` remains unique in each output; the retained and excluded sets are disjoint and their union equals the complete categorized dataset.
- All seven HRV columns are complete (**zero missing values**) in `dataset_final.csv`, as required by the present complete-case rule.

---

## 6. Notebook 04 — Preprocessing summary and integrity review

**Inputs:** the six CSV outputs of Notebooks 01–03.  
**Output:** `outputs/preprocessing_summary.md`.

Notebook 04 reads the existing outputs and performs consistency assertions before producing summary tables. It **does not** re-clean, recategorize, filter, or rewrite the datasets.

### 6.1 Cross-stage checks

The following checks are implemented:

- `source_excel_row` is present and unique in the clean, categorized, final, and excluded datasets.
- Cleaned and categorized datasets contain **exactly the same source-row IDs**.
- No source row appears simultaneously in retained and excluded outputs.
- Every categorized record is present in **exactly one** of the retained/excluded outputs.
- The retained dataset has **the same column order and names** as the categorized dataset.
- Every source row with a row-level entry in `data_errors.csv` appears in `error_examples.csv`, and vice versa.
- Every excluded record's `exclusion_count` matches the number of listed exclusion reasons.

The reproduced outputs satisfy these checks.

### 6.2 Factor distributions before and after filtering

This table consolidates the observed factor sizes, without suggesting that category membership implies a clinical diagnosis.

| Factor | Category | Before filtering (n=530) | Retained (n=476) |
|---|---|---:|---:|
| Age | `<60` | 10 | 0 |
| Age | `60-69` | 219 | 198 |
| Age | `70-79` | 261 | 241 |
| Age | `80+` | 40 | 37 |
| Sex | Female | 366 | 333 |
| Sex | Male | 164 | 143 |
| BP | Optimal | 120 | 98 |
| BP | Normal | 131 | 123 |
| BP | High-normal | 118 | 106 |
| BP | Stage 1 | 122 | 111 |
| BP | Stage 2 | 35 | 34 |
| BP | Stage 3 | 4 | 4 |
| BMI | Underweight | 23 | 21 |
| BMI | Normal | 155 | 136 |
| BMI | Overweight | 166 | 145 |
| BMI | Obesity | 186 | 174 |

Each factor's categories sum separately to its respective denominator of 530 or 476.

### 6.3 Age × sex in the retained sample

| Age group | Female | Male | Total |
|---|---:|---:|---:|
| `60-69` | 150 | 48 | 198 |
| `70-79` | 156 | 85 | 241 |
| `80+` | 27 | 10 | 37 |
| **Total** | **333** | **143** | **476** |

### 6.4 Blood pressure × BMI in the retained sample

| BP category | Underweight | Normal | Overweight | Obesity | Total |
|---|---:|---:|---:|---:|---:|
| Optimal | 5 | 33 | 26 | 34 | 98 |
| Normal | 5 | 33 | 38 | 47 | 123 |
| High-normal | 6 | 34 | 39 | 27 | 106 |
| Stage 1 | 5 | 31 | 28 | 47 | 111 |
| Stage 2 | 0 | 5 | 12 | 17 | 34 |
| Stage 3 | 0 | 0 | 2 | 2 | 4 |
| **Total** | **21** | **136** | **145** | **174** | **476** |

### 6.5 Four-factor sample availability

The potential full factorial structure in the retained sample is:

$$
3\ \text{age groups}\times 2\ \text{sex groups}\times 6\ \text{BP groups}\times 4\ \text{BMI groups}=144\ \text{possible cells}.
$$

Observed cell occupancy is highly uneven:

| Four-factor cell statistic | Result |
|---|---:|
| Theoretically possible category combinations | 144 |
| **Observed nonempty combinations** | **93** |
| Empty combinations | 51 |
| Observed combinations with <5 participants | **58** |
| Observed combinations with <10 participants | **77** |
| Smallest observed combination | 1 participant |
| Largest observed combination | 19 participants |
| Median observed combination size | 3 participants |

These are descriptive counts. They do **not** establish sufficient power for four-way factorial comparisons. Downstream analyses will need to account explicitly for small and empty cells; this preprocessing pipeline does not merge factor levels or decide the final statistical model.

---

## 7. Reproducibility and output contracts

### 7.1 Execution order

Run notebooks in numerical order from the location corresponding to the relative paths defined in their first code cells:

1. `01_cleaning_and_validation.ipynb` reads `../../raw_data/Basal v2.xlsx` and writes three CSVs under `../../outputs/`.
2. `02_variable_categorization.ipynb` reads `../../outputs/dataset_clean.csv` and writes `dataset_categorized.csv`.
3. `03_final_cohort_construction.ipynb` reads `../../outputs/dataset_categorized.csv` and writes `dataset_final.csv` and `dataset_excluded.csv`.
4. `04_preprocessing_summary.ipynb` reads all six CSVs and writes `preprocessing_summary.md`.

### 7.2 Dependencies

The code uses **Python, pandas, NumPy, pathlib, and Jupyter/IPython**. Reading `.xlsx` through pandas ordinarily requires an Excel engine such as **openpyxl**. Notebook 04 uses `DataFrame.to_markdown()`, which ordinarily requires the **tabulate** package. The notebooks do not pin package versions or create an environment lock file; a reproducibility release should preserve the environment versions separately if needed.

### 7.3 Output schema and intended use

| File | Intended use | Record count |
|---|---|---:|
| `dataset_clean.csv` | Cleaned continuous measurements and administrative provenance before categorization | 530 |
| `data_errors.csv` | Audit trail of edits, warnings, inconsistencies, and dataset-level notes | 183 entries |
| `error_examples.csv` | Original source rows affected by at least one row-level issue | 155 |
| `dataset_categorized.csv` | Complete sample with four categorical factors and BMI reference | 530 |
| `dataset_final.csv` | Main input to subsequent EDA and factor-based HRV analysis | **476** |
| `dataset_excluded.csv` | Removed records with potentially multiple exclusion reasons | **54** |
| `preprocessing_summary.md` | Human-readable, count-based summary produced by Notebook 04 | One report |

The raw Excel file remains the source of record. `dataset_clean.csv` is **not** synonymous with the final analytical sample: it includes all 530 records and retains unresolved HR/RR and duplicate-profile flags in the separate audit log.

---

## 8. Methodological decisions, limitations, and outstanding verification

These points are important when interpreting the final dataset or describing sample selection in a manuscript.

1. **Cross-sectional measurement categories are not diagnoses.** Blood-pressure classes, BMI categories, sex, and age are measured/recorded factors. No health-status labels were created.
2. **The 60-year eligibility threshold is first applied in Notebook 03.** The ten records under 60 remain in intermediate datasets and are available for auditing.
3. **BMI thresholds change at age 60 by study design.** This is transparent in `bmi_reference`. In particular, the treatment of participants aged 60–64 should be stated when describing the normative basis.
4. **Sex-code interpretation has not been independently checked against the primary REDCap codebook.** The `1 = Female`, `2 = Male` mapping is the one explicitly implemented in Notebook 02.
5. **The height correction uses centimeter measurements as the reference.** The nine adjusted heights and corresponding BMI values are mathematically consistent, but source-document confirmation would strengthen provenance.
6. **HRV is not artificially reconstructed.** Neither tenfold-looking HR/RR discrepancies nor identical seven-variable profiles are silently repaired. These cases are flagged in Notebook 01 and, when qualifying, excluded by Notebook 03.
7. **Exact HRV matches are conservatively excluded in their entirety.** This is a provisional QC decision, not verification of participant duplication. Reassessment against original acquisitions could change the final sample.
8. **An operational 10-bpm discrepancy cutoff controls HR/RR exclusions.** This is not a clinical standard. The notebook retains smaller screening discrepancies, and the choice should be reported or sensitivity-tested in downstream work.
9. **Complete-case HRV filtering can alter sample composition.** The retained 476 records have nonmissing values for all seven HRV fields. A later analysis targeting fewer HRV variables could support a different eligible sample; the present preprocessing makes one explicit choice.
10. **Outlying-but-valid values are not discarded solely for being unusual.** Notebook 01's screening ranges are diagnostic flags. In Notebook 03, exclusions follow the listed eligibility, missingness, invalid-value, HR/RR, and identical-profile rules.
11. **The categorical factorial design is sparse.** Of 93 observed four-factor cells, 58 contain fewer than five records. Preprocessing is complete, but subsequent model design must consider cell sizes and factor-level combinations.
12. **No clustering, statistical hypothesis tests, outcome modeling, imputation, longitudinal validation, or external cohort comparison is part of this workflow.** All of these would require separate methodological specifications.

## 9. Final dataset definition

For the current pipeline, the **final analytical dataset** is `outputs/dataset_final.csv`:

- **476 retained source records**, representing 89.8% of the original 530.
- **24 columns**, including all retained cleaned source fields, source-row traceability, and five categorization/reference fields.
- Ages **60–89 years**.
- **333 female-coded** and **143 male-coded** records under the implemented sex mapping.
- **No missing values across the seven HRV measurements**.
- Factor categories for age, sex, blood pressure, and age-appropriate BMI, as explicitly implemented in Notebook 02.

The companion `outputs/dataset_excluded.csv` preserves the **54 excluded source records** with their original categorized columns, `exclusion_count`, and every recorded exclusion reason. Together, retained and excluded records account for **all 530 input rows without overlap or loss**.

The next project stage can use `dataset_final.csv` for descriptive HRV characterization and planned single-factor, combined-factor, and multifactorial comparisons, provided the modeling strategy explicitly addresses the sparse factorial cells and the provisional QC exclusions documented above.

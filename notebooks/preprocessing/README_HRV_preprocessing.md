# HRV Dataset Preprocessing

## Methodological README

**Project:** Heart rate variability and physiological profiling in older adults from southern Chile  
**Workflow:** `01_cleaning_and_validation.ipynb` → `01b_integrity_and_duplicate_review.ipynb` → `02_variable_categorization.ipynb` → `03_final_cohort_construction.ipynb` → `04_preprocessing_summary.ipynb`  
**Starting source:** `Basal v2.xlsx`  
**Version documented:** 27 November 2026; five-stage workflow, based on the completed CSV outputs and `preprocessing_summary.md`  
**Analytical decision:** retain the 407 records not flagged by Notebook 01b as the quality-screened dataset; exclude the 123 flagged records from the current analysis; subsequently restrict eligibility to ages ≥60, yielding **402 final analytical records**.  
**Status:** preprocessing and current-version cohort selection completed. Quarantined records are retained for audit, but **are not part of the current analytical cohort**.

---

## 1. Purpose, scope, and final decision

This workflow converts the original Excel workbook into traceable, consistently typed, and categorized datasets for descriptive and factor-based heart rate variability (HRV) analysis. The primary outcomes are seven HRV and cardiac-interval measurements. The explanatory domains are **age, sex, systolic blood pressure (SBP), diastolic blood pressure (DBP), and body mass index (BMI)**. SBP and DBP are categorized separately and are alternative pressure factors in the planned four-domain combinations (age, sex, pressure, BMI).

The preprocessing does not assign diagnoses or health-status labels, perform HRV hypothesis tests, discover patient clusters, or modify HRV values to make their distributions conform to a reference pattern.

### 1.1 Agreed data-integrity decision

Following examination of the flagged records, the study team and the data collaborator **Cristian** agreed to use the **407 records without individual quality-review flags** from Notebook 01b and to **set aside all 123 flagged records** for this version of the study. The source workbook combines observations from several projects, including older projects for which the source records are not all available in REDCap. Given this provenance and the unresolved matches or discrepancies, retaining only the quality-screened subset is the chosen conservative and parsimonious approach.

This decision has four important boundaries:

1. **The 123 records are excluded from the current analysis**, not deleted from the source workbook or the audit outputs.
2. A review flag **does not prove** that an observation was recorded incorrectly or that two records represent the same individual. The decision concerns acceptable evidence for inclusion in the **current analytical dataset**.
3. The 407 quality-screened records are **not yet the age-eligible cohort**: five are younger than 60. The final dataset therefore contains **402 records**.
4. If original documentation is obtained later, affected records can be reassessed and an explicitly versioned analysis can be repeated. **No such reassessment is required to proceed with the current 402-record analysis.**

In particular, differences in the distribution of **SDNN**, including contrasts between portions of the spreadsheet, **are not treated as proof of measurement error, are not corrected, and are not an independent exclusion criterion**. The SDNN values retained in the final dataset are preserved as supplied after ordinary numeric parsing.

### 1.2 Implemented stages

1. **01 — Cleaning and validation:** import the complete workbook, normalize numeric formatting, check derived measurements, document warnings and corrections, and retain all source observations.
2. **01b — Integrity and duplicate review:** inspect individual measurements and pairwise HRV resemblance; create a quality-screened dataset and a fully annotated suspected-record dataset. The separation is deterministic and does not change original measurements.
3. **02 — Variable categorization:** categorize **only the 407 quality-screened records** for age, sex, SBP, DBP, and BMI while retaining their continuous measurements.
4. **03 — Final cohort construction:** apply age eligibility and the programmed validity rules to the categorized quality-screened data; preserve all applicable reasons for any further exclusion.
5. **04 — Preprocessing summary:** verify the complete partition and cross-stage consistency, and report cohort characteristics and factorial-cell support without changing any input datasets.

### 1.3 Overall dataset flow

| Processing stage | Records | Columns | Interpretation |
|---|---:|---:|---|
| Original Excel, first sheet | **530** | 20 | All input records |
| 01 — `dataset_clean.csv` | **530** | 19 | Cleaned, with source-row tracking; no records removed |
| 01b — `dataset_review_suspected.csv` | **123** | 29 | Quality-review flags; excluded from the current analysis by the agreed decision |
| 01b — `dataset_review_clear.csv` | **407** | 19 | Quality-screened records passed to categorization |
| 02 — `dataset_categorized.csv` | **407** | 25 | Quality-screened records plus six categorical/reference columns |
| 03 — `dataset_excluded.csv` | **5** | 27 | Additional exclusions among the 407, all for age <60 |
| 03 — `dataset_final.csv` | **402** | 25 | **Current age-eligible analytical cohort** |
| 04 — `preprocessing_summary.md` | — | — | Consistency checks and descriptive processing report |

- **Quality-review exclusion:** 123/530 = **23.2%**.
- **Quality-screened subset:** 407/530 = **76.8%**.
- **Age exclusions after quality screening:** 5/530 = **0.9%** (5/407 = **1.2%** of quality-screened records).
- **Final analytical retention:** 402/530 = **75.8%**.
- **Exclusions from the final cohort, by non-overlapping stage:** 123 quality-review records + 5 subsequent age exclusions = **128/530 (24.2%)**.

**Important distinction:** the phrase “407 clean records” in the collaborator's decision refers to the quality-screened output of 01b. For the study of older adults, **`dataset_final.csv` with 402 records is the definitive input for downstream EDA**. Neither the old 476-record dataset nor all 407 age-unrestricted records should be substituted for it.

### 1.4 Source record identity

The workbook has 530 data rows, each assigned a unique `source_excel_row` corresponding to its Excel row number, **2–531** (row 1 contains headers). The data do not establish independently verified unique participant identifiers. Accordingly, a “record” is the precise operational unit of the workflow; reported counts should not be interpreted as a verified count of distinct people independent of the source records.

---

## 2. Original data, units, and provenance

### 2.1 Source variables

The first worksheet of `Basal v2.xlsx` has **530 rows and 20 columns**.

| Domain | Source fields | Meaning |
|---|---|---|
| Demographics | `age`, `sex` | Age in years; coded sex |
| Mean interval/rate | `t2m_pre_mean_rr`, `t2m_pre_mean_hr` | Mean RR interval and mean heart rate |
| Time-domain HRV | `t2m_pre_sdnn`, `t2m_pre_rmssd` | SDNN and RMSSD |
| Frequency-domain HRV | `t2m_pre_hf`, `t2m_pre_lf`, `t2m_pre_vlf` | HF, LF, and VLF |
| Blood pressure | `bp_systolic`, `bp_diastolic`, `bp_pam`, `bp_pp` | Systolic, diastolic, mean arterial, and pulse pressure |
| Anthropometry | `weight kg`, `height cm`, `height mt`, `imc` | Weight, height, and BMI |
| Administrative/export | `Unnamed: 0`, `Unnamed: 1`, `redcap_repeat_instrument` | Nonanalytical spreadsheet/export fields |

The workflow **does not rescale any HRV variables**. For operational arithmetic checks, it assumes RR in milliseconds, HR in beats/minute, blood pressure in mmHg, weight in kilograms, and height in centimeters/meters. These checks do not authorize altering HRV values on the basis of their distribution.

### 2.2 Traceability and preservation

- `source_excel_row` is retained in all tabular outputs.
- `Basal v2.xlsx` is never overwritten.
- `data_errors.csv` and `error_examples.csv` retain evidence for parsing and source-value reviews.
- The 123 records removed from the current analytical route remain individually identifiable in `dataset_review_suspected.csv`, with their source values, grouping, and reasons.
- The final 402 source IDs, the five age-excluded IDs, and the 123 quality-review IDs are **disjoint and together account for all 530 source rows**.

### 2.3 Folder and file layout

```text
project/
├── raw_data/
│   └── Basal v2.xlsx
├── notebooks/
│   └── preprocessing/
│       ├── 01_cleaning_and_validation.ipynb
│       ├── 01b_integrity_and_duplicate_review.ipynb
│       ├── 02_variable_categorization.ipynb
│       ├── 03_final_cohort_construction.ipynb
│       └── 04_preprocessing_summary.ipynb
└── outputs/
    ├── dataset_clean.csv
    ├── data_errors.csv
    ├── error_examples.csv
    ├── dataset_review_clear.csv
    ├── dataset_review_suspected.csv
    ├── dataset_categorized.csv
    ├── dataset_final.csv
    ├── dataset_excluded.csv
    └── preprocessing_summary.md
```

Paths are relative to the notebook working directory as implemented. If the project structure changes, update the path constants in the notebooks without editing generated CSV outputs by hand.

---

## 3. Notebook 01 — Cleaning and validation

**Input:** `../../raw_data/Basal v2.xlsx`  
**Outputs:** `dataset_clean.csv` (530 × 19), `data_errors.csv` (183 entries), `error_examples.csv` (155 source records).  
**Source records excluded:** **0**.

### 3.1 Structural and numeric processing

- Read the first Excel worksheet and preserve an in-memory original for audit examples.
- Insert unique `source_excel_row`.
- Remove two empty export fields: `Unnamed: 0` and `Unnamed: 1`.
- Retain `redcap_repeat_instrument` as an administrative/provenance field, not an analytical factor.
- Rename `weight kg` → `weight_kg`, `height cm` → `height_cm`, and `height mt` → `height_m`.
- Normalize the 17 expected numeric fields by stripping embedded whitespace in textual values, converting comma decimals to period decimals, and applying numeric parsing with logging of unparseable text.

**Observed:** 97 RMSSD values stored as text were successfully converted. Eight RMSSD values remained missing. Neither step changes the definition or unit of RMSSD.

### 3.2 Height, BMI, and pressure checks

Height consistency is evaluated against

\[
\mathrm{height_m}=\frac{\mathrm{height_{cm}}}{100}.
\]

For two positive measurements, disagreements exceeding **0.01 m** are resolved by taking `height_cm` as the operational reference and recomputing `height_m`. A missing height unit can also be reconstructed from the other valid unit. **Nine original Excel rows (446–454)** had height disagreements logged and corrected.

BMI consistency is evaluated against

\[
\mathrm{BMI}=\frac{\mathrm{weight_{kg}}}{\mathrm{height_m}^{2}}.
\]

Stored `imc` is recomputed if the absolute difference from calculated BMI exceeds **0.10 kg/m²**; missing BMI can be calculated from valid source weight and height. **Nine BMI disagreements** were logged and corrected in the same source rows 446–454. These corrections are part of the complete cleaned record, but **the affected records also fall in the 01b quality-review quarantine and therefore do not contribute to the current final cohort**.

For positive pressures with SBP > DBP, the notebook checks

\[
\mathrm{PP}=\mathrm{SBP}-\mathrm{DBP}, \qquad
\mathrm{MAP}=\mathrm{DBP}+\frac{\mathrm{SBP}-\mathrm{DBP}}{3}.
\]

The implemented correction tolerances are **0.01 mmHg** for `bp_pp` and **0.10 mmHg** for `bp_pam`. The supplied outputs required **no** PP or MAP corrections.

### 3.3 Missing data and range-review flags

The cleaned source has **20 missing cells** among the expected numeric fields: 11 SDNN, 8 RMSSD, and 1 mean HR. Missing values are not imputed.

| Field | Inclusive review interval |
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

These intervals trigger **review warnings**, not transformations or physiological judgments. The 14 range flags comprise 10 mean RR, 3 SDNN, and 1 mean HR values. The three SDNN range flags do **not** imply that SDNN is generally faulty.

Mean HR/RR consistency is screened using

\[
\mathrm{HR_{from\ RR}}=\frac{60000}{\mathrm{mean\ RR\ (ms)}},\qquad
R=\frac{\mathrm{HR}\times\mathrm{RR}}{60000}.
\]

A difference greater than **2 beats/min** is logged, and a possible tenfold pattern is identified when `R` is near 0.1 or 10 (3% relative tolerance). The source contains **11 factor-ten-pattern flags** and **4 other HR/RR disagreements**. No HR or RR values are automatically replaced.

### 3.4 Exact original HRV matches

Notebook 01 checks exact equality across all seven HRV fields (`mean_rr`, `mean_hr`, `sdnn`, `rmssd`, `hf`, `lf`, `vlf`), for rows with complete seven-variable data. It logs **8 matching pairs (16 records)**. No complete source-row duplicates were logged. A matching profile is an integrity-review signal, not independently established participant duplication.

### 3.5 Issue log accounting

| `issue` | Log entries | Treatment in Notebook 01 |
|---|---:|---|
| `text_number` | 97 | Converted numeric text |
| `missing_value` | 20 | Left missing |
| `identical_hrv_profile` | 16 | Review flag |
| `outside_review_range` | 14 | Review flag |
| `hr_rr_factor_10_pattern` | 11 | Review flag |
| `height_disagreement` | 9 | Recomputed from centimeters |
| `bmi_disagreement` | 9 | Recomputed from weight and height |
| `hr_rr_disagreement` | 4 | Review flag |
| `empty_column` | 2 | Removed export columns |
| `empty_metadata_column` | 1 | Retained field and dataset-level note |
| **Total** | **183** | **Not 183 distinct records** |

The source-row audit file contains **155 rows with at least one row-level issue**, and dataset-level notes are distinguished from row-level events. The log does not itself constitute an exclusion list.

---

## 4. Notebook 01b — Integrity and duplicate review

**Inputs:** `../../outputs/dataset_clean.csv` and `../../outputs/data_errors.csv`.  
**Outputs:** `dataset_review_clear.csv` (**407 × 19**) and `dataset_review_suspected.csv` (**123 × 29**).  
**Role:** identify records under the pre-specified quality-review criteria, preserve their evidence, and separate them **before categorization**.

### 4.1 Individual record review

The notebook flags any record that meets one or more of the following conditions:

- Missing expected numeric measurement (including HRV, anthropometry, pressure, sex, or age).
- Invalid sex code (accepted codes: **1 = Female**, **2 = Male**); invalid age; invalid pressure ordering; nonpositive required measurement; or negative HRV values.
- Measurement outside the documented screening intervals, recorded as a **review warning**, not proof of an invalid measurement.
- Possible tenfold HR/RR discrepancy or other HR/RR difference exceeding **2 beats/min**.
- Height or BMI source disagreement that was corrected and logged in Notebook 01; unparseable source numeric values are also auditable if present.

Successfully converted numeric text is **not** by itself a reason for quarantine; nor does an ordinary SDNN distributional difference create an individual flag.

### 4.2 Pairwise HRV similarity review

The notebook compares pairs of records on the seven HRV fields. At least five simultaneously observed, exactly matching HRV measurements are needed to consider the implemented matching patterns. The classification is:

| Pair code | Exact implemented meaning | Observed pairs | Records |
|---|---|---:|---:|
| `HF_VLF_SWAPPED` | Core HRV measurements RR, HR, SDNN, RMSSD and LF agree; HF and VLF exchange values between records | **33** | **66** |
| `EXACT_SEVEN_HRV` | All seven HRV variables exactly agree | **8** | **16** |
| `NEAR_HRV_MATCH` | Core RR, HR, SDNN and RMSSD match with at least five matching observed HRV values, without meeting a stronger case | **3** | **6** |
| `PARTIAL_HRV_MATCH` | All comparable HRV variables match, but fewer than seven are jointly observed, with at least five matching | **1** | **2** |
| **Total** | | **45** | **90** |

These are **pairwise audit relationships**, not confirmed duplicated individuals. All members of each suspicious pair are set aside by the agreed analytical decision; no arbitrary first/last record is retained.

The especially structured HF/VLF pattern consists of paired Excel rows **422–454** and **455–487** (33 pairings). The original comparisons also showed systematic differences in coded sex for many pairs. The workbook aggregates projects with incomplete common REDCap provenance, so the current decision is **exclusion from analysis**, not speculative repair of the source measurements.

### 4.3 Group construction and audit schema

The notebook constructs linked groups of pairs using a union-find procedure. In the actual data, it identified **45 paired groups of size 2 (90 records)** and **33 individually flagged singletons**, totaling **123 quarantined records**. The algorithm also supports larger connected groups if future data require them.

`dataset_review_suspected.csv` includes the 19 processed source columns and these 10 review columns:

| Column | Contents |
|---|---|
| `review_status` | Technical review label, `PENDING_SOURCE_REVIEW` |
| `review_group_id` | Linked-pair identifier `P####` or singleton identifier `S####` |
| `group_size` | Number of members in the audit group |
| `group_members_excel_rows` | Excel source rows of every group member |
| `group_pair_patterns` | Pair pattern code(s) identified in the group |
| `review_reason_count` | Number of distinct review reason codes per record |
| `review_reason_codes` | All applicable reason codes; separated by `; ` |
| `review_affected_columns` | Fields involved in the findings |
| `review_justification` | Per-record evidence and source/processed value details |
| `group_pair_evidence` | Explicit pairwise matches, discrepancies, and context differences |

**Interpretation of `review_status`:** the CSV retains the literal `PENDING_SOURCE_REVIEW` status assigned by the notebook. This denotes **unverified source-level findings**. It does **not** overturn the subsequent **study-level decision to exclude all 123 from the current analyses**. No retrospective rewriting of that output status was performed merely to document the decision.

### 4.4 Actual review-reason frequencies

Reason counts are **not mutually exclusive**: one record may have both a pair match and an individual warning, and every record in a pair carries the applicable pair code.

| `review_reason_codes` entry | Flagged records |
|---|---:|
| `HF_VLF_SWAPPED` | 66 |
| `EXACT_SEVEN_HRV` | 16 |
| `MISSING_NUMERIC_MEASUREMENT` | 16 |
| `OUTSIDE_REVIEW_RANGE` | 14 |
| `HR_RR_FACTOR_TEN_PATTERN` | 11 |
| `SOURCE_BMI_DISAGREEMENT_CORRECTED` | 9 |
| `SOURCE_HEIGHT_DISAGREEMENT_CORRECTED` | 9 |
| `NEAR_HRV_MATCH` | 6 |
| `HR_RR_REVIEW_DISAGREEMENT` | 4 |
| `PARTIAL_HRV_MATCH` | 2 |

**Total distinct quarantined records: 123**, regardless of the sum of reason counts.

The 01b notebook also prints an exploratory contrast of SDNN/RMSSD across spreadsheet row order. This diagnostic is **not a validated measurement-error test**, does not add records to the quarantine list, does not alter SDNN, and is **not grounds for further exclusions**.

### 4.5 Quality-screening guarantees and study decision

- All **530** cleaned records are accounted for: **407 clear + 123 suspected**.
- The two outputs have disjoint unique source-row identifiers.
- The original **19 cleaned columns have exactly the same values** in both outputs as in `dataset_clean.csv`.
- Every suspected record retains its reasons and evidence in the audit output.
- The current analysis proceeds **only with `dataset_review_clear.csv`**; `dataset_review_suspected.csv` is preserved, not reintroduced during categorization or cohort construction.

---

## 5. Notebook 02 — Variable categorization

**Input:** `../../outputs/dataset_review_clear.csv` (**407 × 19**).  
**Output:** `../../outputs/dataset_categorized.csv` (**407 × 25**).  
**Record exclusions:** none.  
**New columns:** `age_group`, `sex_category`, `sbp_category`, `dbp_category`, `bmi_category`, `bmi_reference`.

Only records cleared by 01b are categorized. The original 19 fields and continuous measurements remain unchanged. The five age-ineligible records still receive categories and are excluded only by Notebook 03.

### 5.1 Age groups

The four age groups are **study-defined bands**, not a prescribed four-bin MINSAL standard.

| `age_group` | Operational rule | Categorized (n=407) | Final (n=402) |
|---|---|---:|---:|
| `<60` | Age <60 | 5 | 0 |
| `60-69` | 60 ≤ age <70 | 184 | 184 |
| `70-79` | 70 ≤ age <80 | 190 | 190 |
| `80+` | Age ≥80 | 28 | 28 |
| **Total** | | **407** | **402** |

The 60-year age threshold is implemented as an **eligibility** criterion in Notebook 03, not as a cleaning operation.

### 5.2 Sex

The source-code mapping was confirmed for this study: **`1 = Female`, `2 = Male`**.

| Source code | `sex_category` | Categorized | Final |
|---|---|---:|---:|
| `1` | Female | 299 | 294 |
| `2` | Male | 108 | 108 |
| **Total** | | **407** | **402** |

### 5.3 Independent blood-pressure categories

The notebook uses independent binary definitions for SBP and DBP, without creating the former composite `bp_category` and without treating a category as a diagnosis.

| Source field | Output factor | Level | Operational rule | Categorized | Final |
|---|---|---|---|---:|---:|
| `bp_systolic` | `sbp_category` | `<140 mmHg` | 0 < SBP <140 | 303 | 298 |
| `bp_systolic` | `sbp_category` | `>=140 mmHg` | SBP ≥140 | 104 | 104 |
| **SBP total** | | | | **407** | **402** |
| `bp_diastolic` | `dbp_category` | `<90 mmHg` | 0 < DBP <90 | 359 | 354 |
| `bp_diastolic` | `dbp_category` | `>=90 mmHg` | DBP ≥90 | 48 | 48 |
| **DBP total** | | | | **407** | **402** |

Missing or nonpositive pressure measurements would remain uncategorized for that factor and be evaluated in the separate quality/eligibility logic. **No retained record has unassigned pressure categories.** Elevated measurements do not themselves cause exclusion.

### 5.4 Age-dependent BMI categories

BMI uses age-specific operational thresholds while continuous `imc` remains unchanged.

| `bmi_category` | Age <60 | Age ≥60 |
|---|---|---|
| Underweight | BMI <18.5 | BMI <23 |
| Normal | 18.5 ≤ BMI <25 | 23 ≤ BMI <28 |
| Overweight | 25 ≤ BMI <30 | 28 ≤ BMI <32 |
| Obesity | BMI ≥30 | BMI ≥32 |

The workflow applies the older-person thresholds **starting at age 60** as a specified study decision. This should be stated explicitly if describing MINSAL/EMPAM references, which may have age-specific implementation contexts. The `bmi_reference` field records the operational reference assigned to each row.

| `bmi_category` | Categorized | Final |
|---|---:|---:|
| Underweight | 17 | 17 |
| Normal | 118 | 118 |
| Overweight | 118 | 117 |
| Obesity | 154 | 150 |
| **Total** | **407** | **402** |

`bmi_reference` is `MINSAL adult (<60)` for five categorized rows and `MINSAL older person (>=60)` for the other 402 rows. All 402 final records use the older-person reference.

### 5.5 Categorization integrity

Notebook 02 verifies unchanged row counts, source-column preservation and order, unique `source_excel_row`, and absence of the obsolete combined `bp_category` field. The executed output assigns all five categorical factors for all 407 input records.

**Normative references carried forward from Notebook 02:**

- MINSAL (2021), *Plan Nacional de Salud Integral para Personas Mayores*, Table 19: https://www.minsal.cl/wp-content/uploads/2021/11/Plan-Nacional-de-Salud-Integral-para-Personas-Mayores.pdf
- MINSAL, *Guía Clínica 2010 — Hipertensión arterial primaria*, Table 2 (historical context for 140/90 mmHg cutoffs): https://www.minsal.cl/portal/url/item/7220fdc4341c44a9e04001011f0113b9.pdf
- MINSAL, *Guía del Examen de Medicina Preventiva*: https://diprece.minsal.cl/wrdprss_minsal/wp-content/uploads/2014/09/GPC-Medicina-Preventiva.pdf
- Chile, Decreto 20 (2022): https://www.leychile.cl/navegar?idNorma=1182129

These are the references documented for the implemented rules; this README is not a new independent clinical-guideline appraisal.

---

## 6. Notebook 03 — Final cohort construction

**Input:** `../../outputs/dataset_categorized.csv` (**407 × 25**).  
**Outputs:** `dataset_final.csv` (**402 × 25**) and `dataset_excluded.csv` (**5 × 27**).

This notebook performs **eligibility exclusion after** the upstream 01b quality-screening decision. It does **not** reintroduce the 123 quality-review records. It explicitly verifies that the categorized source IDs equal the clear-set source IDs and do not overlap the suspected-set IDs.

### 6.1 Programmed exclusion rules

The notebook evaluates all applicable criteria for every categorized record, including:

| Rule family | Operational definition | Observed among the 407 |
|---|---|---|
| Age eligibility | `age < 60` | **5 records** |
| Missing essential HRV or factor | Missing any of seven HRV fields or the required continuous/categorical factors | 0 |
| Invalid sex | `sex` outside {1, 2} | 0 |
| Invalid age | Age <0 or >120 | 0 |
| Invalid blood pressure | SBP ≤0, DBP ≤0, or SBP ≤DBP | 0 |
| Invalid BMI | BMI ≤0 | 0 |
| Invalid mean RR/HR | RR ≤0 or HR ≤0 | 0 |
| Negative HRV | SDNN, RMSSD, HF, LF, or VLF <0 | 0 |
| Near-tenfold HR/RR pattern | `HR * RR / 60000` in [0.097, 0.103] or [9.7, 10.3] | 0 |
| Other major HR/RR mismatch | `abs(HR - 60000 / RR) >10` bpm, excluding factor-ten cases | 0 |
| Identical complete HRV profile | All seven HRV fields repeated within categorized rows | 0 |

All rules are evaluated independently and each applicable reason is recorded; no variable values are rewritten. The HR/RR 10-bpm cutoff is an **operational QC criterion**, not a clinical diagnostic threshold. Among the current 407 quality-screened records, it causes no additional exclusion.

### 6.2 Actual additional exclusions

Exactly **five** categorized records were excluded, each with **one** reason: `AGE_BELOW_60`. Their original Excel row numbers are **45, 113, 119, 342, and 404**. The excluded records were female-coded under the confirmed source mapping and had ages **55–59 years**.

The 123 upstream quality-review exclusions are **not** appended to `dataset_excluded.csv`, because that file exclusively records exclusions **after 01b**. To account for all removed source records, use the union of **`dataset_review_suspected.csv` (123)** and **`dataset_excluded.csv` (5)**.

### 6.3 Output guarantees

- `dataset_final.csv` contains **402 rows** and all **25 categorized columns**, with no added or modified values.
- `dataset_excluded.csv` contains **5 rows**, the same 25 columns, and `exclusion_count` / `exclusion_reasons` (27 columns total).
- The final and excluded source IDs are disjoint and partition the **407** categorized rows.
- No record from the 123-row quality-review file appears in either categorized or final data.
- All **seven HRV variables are complete** among the final 402 rows.

---

## 7. Notebook 04 — Summary and cross-stage verification

**Inputs:** `dataset_clean.csv`, `data_errors.csv`, `error_examples.csv`, `dataset_review_clear.csv`, `dataset_review_suspected.csv`, `dataset_categorized.csv`, `dataset_final.csv`, and `dataset_excluded.csv`.  
**Output:** `../../outputs/preprocessing_summary.md`.

This notebook summarizes the implemented workflow and performs integrity assertions. It **does not** reclean, recategorize, exclude, or rewrite records.

### 7.1 Verified dataset partitions

- `source_excel_row` is present and unique in the relevant stage outputs.
- Cleaned source IDs are exactly the disjoint union of clear and suspected source IDs.
- Categorized IDs match the **407 clear IDs**, not all 530 cleaned IDs.
- Categorized IDs are exactly the disjoint union of final (402) and age-excluded (5) IDs.
- The categorical/output source columns are preserved in order, and the final rows retain the categories assigned by Notebook 02.
- Error-example IDs match the source rows represented in the `data_errors.csv` row-level log.
- Quarantine groups and `group_size` are internally consistent; reason counts agree with the listed review reasons.
- Excluded records' `exclusion_count` values match the listed exclusion reasons.

### 7.2 Final age-by-sex distribution

| Age group | Female | Male | Total |
|---|---:|---:|---:|
| `60-69` | 141 | 43 | 184 |
| `70-79` | 131 | 59 | 190 |
| `80+` | 22 | 6 | 28 |
| **Total** | **294** | **108** | **402** |

### 7.3 Final SBP × BMI distribution

| SBP category | Underweight | Normal | Overweight | Obesity | Total |
|---|---:|---:|---:|---:|---:|
| `<140 mmHg` | 14 | 90 | 89 | 105 | 298 |
| `>=140 mmHg` | 3 | 28 | 28 | 45 | 104 |
| **Total** | **17** | **118** | **117** | **150** | **402** |

### 7.4 Final DBP × BMI distribution

| DBP category | Underweight | Normal | Overweight | Obesity | Total |
|---|---:|---:|---:|---:|---:|
| `<90 mmHg` | 17 | 109 | 102 | 126 | 354 |
| `>=90 mmHg` | 0 | 9 | 15 | 24 | 48 |
| **Total** | **17** | **118** | **117** | **150** | **402** |

### 7.5 Four-factor sample support

The two intended specifications are **age × sex × SBP category × BMI** and **age × sex × DBP category × BMI**, considered **separately**. Three older-adult age groups, two sex groups, two pressure categories, and four BMI categories yield **48 possible combinations per specification**.

| Factorial support statistic | With SBP | With DBP |
|---|---:|---:|
| Theoretically possible combinations | 48 | 48 |
| Observed nonempty combinations | **40** | **33** |
| Empty combinations | 8 | 15 |
| Occupied combinations with <5 records | 16 | 13 |
| Occupied combinations with <10 records | 25 | 21 |
| Smallest occupied cell | 1 | 1 |
| Largest occupied cell | 47 | 56 |
| Median occupied cell size | 7.5 | 7.0 |

These are **descriptive support counts only**. Sparse cells do not automatically mean that every associated model is mathematically unidentifiable, and the preprocessing does not merge factor levels or exclude additional records solely to achieve uniform cell sizes. Any model-rank or inference diagnostics belong to the subsequent analytical notebooks, not the cleaning pipeline.

### 7.6 Missing HRV in the final cohort

All seven variables (`t2m_pre_mean_rr`, `t2m_pre_mean_hr`, `t2m_pre_sdnn`, `t2m_pre_rmssd`, `t2m_pre_hf`, `t2m_pre_lf`, `t2m_pre_vlf`) have **zero missing values** among the 402 final records.

---

## 8. Reproducibility and exact output contracts

### 8.1 Required execution order

Run in the following sequence, from the notebook directory consistent with the relative file paths:

1. **01** — `01_cleaning_and_validation.ipynb` reads `../../raw_data/Basal v2.xlsx`; writes cleaned data and two source-audit files.
2. **01b** — `01b_integrity_and_duplicate_review.ipynb` reads `../../outputs/dataset_clean.csv` and `data_errors.csv`; writes the **407-row clear** and **123-row suspected** datasets.
3. **02** — `02_variable_categorization.ipynb` reads **`../../outputs/dataset_review_clear.csv`**, not `dataset_clean.csv`; writes the **407-row** categorized data.
4. **03** — `03_final_cohort_construction.ipynb` reads `dataset_categorized.csv` and validates against both 01b partitions; writes the **402-row final** and **5-row age-excluded** datasets.
5. **04** — `04_preprocessing_summary.ipynb` reads all preceding CSV outputs; writes `preprocessing_summary.md`.

**Do not use the older 02–04 notebooks configured for 530 categorized records / 476 final records.** The current workflow requires the revised notebooks that follow 01b and generate the 407 → 402 progression.

### 8.2 Output files

| Output file | Records / entries | Columns | Analytical role |
|---|---:|---:|---|
| `dataset_clean.csv` | 530 | 19 | Complete cleaned input; not the final cohort |
| `data_errors.csv` | 183 log entries | 7 | Parsing, correction, and source-integrity audit |
| `error_examples.csv` | 155 records | 25 | Original-source examples and corresponding processing details |
| `dataset_review_clear.csv` | **407** | 19 | Quality-screened, age-unrestricted input to 02 |
| `dataset_review_suspected.csv` | **123** | 29 | Excluded from current analyses; retained for traceability and potential future reassessment |
| `dataset_categorized.csv` | **407** | 25 | Classified quality-screened records, before age eligibility |
| `dataset_final.csv` | **402** | 25 | **Authoritative analytical input for HRV EDA** |
| `dataset_excluded.csv` | **5** | 27 | Additional age ineligibility after 01b |
| `preprocessing_summary.md` | One report | — | Cross-stage checks and descriptive summary |

The conservation equation is:

\[
\underbrace{530}_{\text{source}}
=\underbrace{123}_{\text{quality-review exclusion}}
+\underbrace{5}_{\text{age exclusion after QC}}
+\underbrace{402}_{\text{final analytical records}}.
\]

No output should be manually edited to enforce these totals. Source-row IDs, set-partition assertions, and rerunning the complete pipeline establish reproducibility.

### 8.3 Software dependencies

The code uses Python, pandas, NumPy, pathlib, and Jupyter/IPython; `.xlsx` import typically requires `openpyxl`, and Markdown table export in Notebook 04 typically requires `tabulate`. The supplied notebooks do not specify a pinned environment or lock file; package versions should be archived separately when preparing a public reproducibility release.

---

## 9. Methodological interpretation and downstream use

1. **Cohort selection is fixed for this analysis version.** The team-selected quality-screened subset comprises **407 records**, followed by five age exclusions, yielding **402 records**. The 123 cases remain out of the current primary analyses irrespective of whether any eventually proves to be valid.
2. **Source review and error confirmation are different.** Suspicious pairs, corrected source fields, missing data, range warnings, and HR/RR discrepancies triggered the implemented review criteria. Their presence does not independently confirm data corruption.
3. **The dataset combines projects with heterogeneous source availability.** Some historical source data are not represented in REDCap; this is the practical provenance reason for the conservative inclusion decision, not an assumption that all 123 records are invalid.
4. **No automatic deduplication retains an arbitrary member of a pair.** When a pair matches the current suspicion rules, both records are excluded from the active cohort and documented together.
5. **No HRV values are reconstructed from expected relationships.** HR/RR differences are assessed for review; no blanket transformations are applied to SDNN, RMSSD, HF, LF, or VLF. In particular, SDNN's distribution **is not classified as an error** and does not justify extra filtering.
6. **Categorization is independent of exclusion for elevated pressure/BMI.** The final data retain continuous age, BMI, SBP, DBP, and HRV values. SBP and DBP are two distinct pressure factors; they are not fused into a staged clinical diagnosis.
7. **BMI thresholds follow the documented study convention from age 60.** The resulting factor definitions, including the treatment of ages 60–64, must remain consistent throughout reporting.
8. **Complete HRV measurements are available in the final cohort.** This is an outcome of the upstream review and age eligibility, not evidence that missing source measurements were imputed.
9. **Factorial cell sparsity must be characterized separately from model estimability.** No small-cell-based participant deletion or automatic factor-level collapsing occurs in preprocessing.
10. **Future additions require a new version.** If the collaborator later resolves source identities or measurements for quarantined records, any newly accepted data must pass the same cleaning, review, categorization, cohort, and summary sequence; counts and downstream EDA must then be regenerated and versioned.
11. **This README documents the executed process and the agreed selection decision.** It does not claim independent validation against unavailable historical acquisition files or participant identifiers.

## 10. Final analytical dataset

For the present study, **`outputs/dataset_final.csv` is the authoritative processed dataset**:

- **402 original source records** retained (**75.8%** of 530).
- **25 columns**, including source-row ID, the seven HRV variables, continuous explanatory measurements, five categorical explanatory variables, and `bmi_reference`.
- Age range **60–89 years**.
- Sex composition **294 Female-coded** and **108 Male-coded** records under the confirmed source coding (`1 = Female`, `2 = Male`).
- Age-group composition **184 aged 60–69**, **190 aged 70–79**, and **28 aged 80+**.
- **Zero missing values across all seven HRV variables**.
- Independent SBP and DBP categories, along with age-dependent BMI categories.

The complementary `dataset_review_suspected.csv` preserves all **123 quality-review exclusions** and their evidence; `dataset_excluded.csv` preserves the **five age exclusions** among the 407 quality-screened records. These files should be archived with the final dataset and processing logs to retain the complete audit trail.

**Downstream rule:** Notebooks for general HRV characterization and unifactorial, bifactorial, trifactorial, and four-factor analysis must read the **402-row `dataset_final.csv`** and must not silently restore quarantined records, change the categories, or impose undocumented additional data-cleaning exclusions.

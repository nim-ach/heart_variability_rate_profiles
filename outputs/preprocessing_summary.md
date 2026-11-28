# HRV preprocessing summary

Based on the outputs of Notebooks 01, 01b, 02, and 03.

## Dataset flow
| stage                        |   records |   columns |   percent_of_original |
|:-----------------------------|----------:|----------:|----------------------:|
| 01 — Cleaned source          |       530 |        19 |                 100   |
| 01b — Quarantined for review |       123 |        29 |                  23.2 |
| 01b — Accepted for analysis  |       407 |        19 |                  76.8 |
| 02 — Categorized             |       407 |        25 |                  76.8 |
| 03 — Retained                |       402 |        25 |                  75.8 |
| 03 — Excluded by eligibility |         5 |        27 |                   0.9 |

Final retention: 402/530 (75.8%) of original records.
Quarantined for quality review: 123/530 (23.2%).
Eligibility exclusions after 01b: 5/530 (0.9%).
Quarantine does not confirm errors. Suspicious records remain available for future review.

## Notebook 01b quality-review quarantine
Groups with paired/clustered records: 45; singleton review records: 33.
Review-reason frequencies overlap and must not be summed.
| review_reason                        |   records |
|:-------------------------------------|----------:|
| HF_VLF_SWAPPED                       |        66 |
| EXACT_SEVEN_HRV                      |        16 |
| MISSING_NUMERIC_MEASUREMENT          |        16 |
| OUTSIDE_REVIEW_RANGE                 |        14 |
| HR_RR_FACTOR_TEN_PATTERN             |        11 |
| SOURCE_BMI_DISAGREEMENT_CORRECTED    |         9 |
| SOURCE_HEIGHT_DISAGREEMENT_CORRECTED |         9 |
| NEAR_HRV_MATCH                       |         6 |
| HR_RR_REVIEW_DISAGREEMENT            |         4 |
| PARTIAL_HRV_MATCH                    |         2 |

## Cleaning and validation
Log entries: 183. Unique participants with logged issues: 155.
Original error examples: 155 rows.

| issue                   | action                            |   entries |
|:------------------------|:----------------------------------|----------:|
| text_number             | converted_to_numeric              |        97 |
| missing_value           | kept_missing                      |        20 |
| identical_hrv_profile   | review_no_change                  |        16 |
| outside_review_range    | review_no_change                  |        14 |
| hr_rr_factor_10_pattern | review_no_change                  |        11 |
| bmi_disagreement        | recomputed_from_weight_and_height |         9 |
| height_disagreement     | corrected_from_height_cm          |         9 |
| hr_rr_disagreement      | review_no_change                  |         4 |
| empty_column            | removed_column                    |         2 |
| empty_metadata_column   | retained_for_traceability         |         1 |

### Issues by column
| column                          |   entries |
|:--------------------------------|----------:|
| t2m_pre_rmssd                   |       105 |
| hrv_profile                     |        16 |
| t2m_pre_mean_hr/t2m_pre_mean_rr |        15 |
| t2m_pre_sdnn                    |        14 |
| t2m_pre_mean_rr                 |        10 |
| height_m                        |         9 |
| imc                             |         9 |
| t2m_pre_mean_hr                 |         2 |
| Unnamed: 0                      |         1 |
| Unnamed: 1                      |         1 |
| redcap_repeat_instrument        |         1 |

## Factor categories

### age_group
| age_group   |   categorized_n |   retained_n |   categorized_pct |   retained_pct |
|:------------|----------------:|-------------:|------------------:|---------------:|
| 60-69       |             184 |          184 |              45.2 |           45.8 |
| 70-79       |             190 |          190 |              46.7 |           47.3 |
| 80+         |              28 |           28 |               6.9 |            7   |
| <60         |               5 |            0 |               1.2 |            0   |

### sex_category
| sex_category   |   categorized_n |   retained_n |   categorized_pct |   retained_pct |
|:---------------|----------------:|-------------:|------------------:|---------------:|
| Female         |             299 |          294 |              73.5 |           73.1 |
| Male           |             108 |          108 |              26.5 |           26.9 |

### sbp_category
| sbp_category   |   categorized_n |   retained_n |   categorized_pct |   retained_pct |
|:---------------|----------------:|-------------:|------------------:|---------------:|
| <140 mmHg      |             303 |          298 |              74.4 |           74.1 |
| >=140 mmHg     |             104 |          104 |              25.6 |           25.9 |

### dbp_category
| dbp_category   |   categorized_n |   retained_n |   categorized_pct |   retained_pct |
|:---------------|----------------:|-------------:|------------------:|---------------:|
| <90 mmHg       |             359 |          354 |              88.2 |           88.1 |
| >=90 mmHg      |              48 |           48 |              11.8 |           11.9 |

### bmi_category
| bmi_category   |   categorized_n |   retained_n |   categorized_pct |   retained_pct |
|:---------------|----------------:|-------------:|------------------:|---------------:|
| Obesity        |             154 |          150 |              37.8 |           37.3 |
| Normal         |             118 |          118 |              29   |           29.4 |
| Overweight     |             118 |          117 |              29   |           29.1 |
| Underweight    |              17 |           17 |               4.2 |            4.2 |

### bmi_reference
| bmi_reference              |   categorized_n |   retained_n |   categorized_pct |   retained_pct |
|:---------------------------|----------------:|-------------:|------------------:|---------------:|
| MINSAL adult (<60)         |               5 |            0 |               1.2 |              0 |
| MINSAL older person (>=60) |             402 |          402 |              98.8 |            100 |

## Eligibility exclusions after quality quarantine
Reasons are not mutually exclusive; counts should not be summed to obtain excluded participants.
| reason       |   participants |   percent_of_excluded |
|:-------------|---------------:|----------------------:|
| AGE_BELOW_60 |              5 |                   100 |

### Number of reasons per participant
|   reasons_per_participant |   participants |
|--------------------------:|---------------:|
|                         1 |              5 |

### Observed exclusion combinations
| combined_reasons   |   participants |
|:-------------------|---------------:|
| AGE_BELOW_60       |              5 |

## Final sample: age by sex
| age_group   |   Female |   Male |
|:------------|---------:|-------:|
| 60-69       |      141 |     43 |
| 70-79       |      131 |     59 |
| 80+         |       22 |      6 |

## Final sample: systolic BP by BMI
| sbp_category   |   Normal |   Obesity |   Overweight |   Underweight |
|:---------------|---------:|----------:|-------------:|--------------:|
| <140 mmHg      |       90 |       105 |           89 |            14 |
| >=140 mmHg     |       28 |        45 |           28 |             3 |

## Final sample: diastolic BP by BMI
| dbp_category   |   Normal |   Obesity |   Overweight |   Underweight |
|:---------------|---------:|----------:|-------------:|--------------:|
| <90 mmHg       |      109 |       126 |          102 |            17 |
| >=90 mmHg      |        9 |        24 |           15 |             0 |

## Four-factor combinations
Age × sex × pressure × BMI is summarized separately for systolic and diastolic pressure.

### Age × sex × sbp_category × BMI
Observed combinations: 40; fewer than five participants: 16; fewer than ten: 25.

Smallest observed groups:
| age_group   | sex_category   | sbp_category   | bmi_category   |   participants |
|:------------|:---------------|:---------------|:---------------|---------------:|
| 60-69       | Female         | >=140 mmHg     | Underweight    |              1 |
| 60-69       | Male           | >=140 mmHg     | Normal         |              1 |
| 80+         | Female         | <140 mmHg      | Obesity        |              1 |
| 80+         | Male           | >=140 mmHg     | Obesity        |              1 |
| 80+         | Male           | <140 mmHg      | Normal         |              1 |
| 80+         | Male           | >=140 mmHg     | Normal         |              1 |
| 80+         | Female         | >=140 mmHg     | Normal         |              1 |
| 80+         | Female         | <140 mmHg      | Underweight    |              1 |
| 70-79       | Female         | >=140 mmHg     | Underweight    |              2 |
| 80+         | Female         | >=140 mmHg     | Overweight     |              2 |
| 80+         | Female         | >=140 mmHg     | Obesity        |              2 |
| 60-69       | Male           | <140 mmHg      | Underweight    |              2 |
| 60-69       | Male           | >=140 mmHg     | Overweight     |              2 |
| 80+         | Male           | <140 mmHg      | Overweight     |              3 |
| 60-69       | Female         | <140 mmHg      | Underweight    |              3 |

### Age × sex × dbp_category × BMI
Observed combinations: 33; fewer than five participants: 13; fewer than ten: 21.

Smallest observed groups:
| age_group   | sex_category   | dbp_category   | bmi_category   |   participants |
|:------------|:---------------|:---------------|:---------------|---------------:|
| 60-69       | Male           | >=90 mmHg      | Normal         |              1 |
| 80+         | Female         | <90 mmHg       | Underweight    |              1 |
| 80+         | Male           | <90 mmHg       | Obesity        |              1 |
| 70-79       | Male           | >=90 mmHg      | Normal         |              1 |
| 70-79       | Male           | >=90 mmHg      | Overweight     |              2 |
| 60-69       | Male           | <90 mmHg       | Underweight    |              2 |
| 80+         | Male           | <90 mmHg       | Normal         |              2 |
| 60-69       | Male           | >=90 mmHg      | Overweight     |              3 |
| 70-79       | Male           | >=90 mmHg      | Obesity        |              3 |
| 80+         | Female         | <90 mmHg       | Obesity        |              3 |
| 80+         | Male           | <90 mmHg       | Overweight     |              3 |
| 70-79       | Female         | >=90 mmHg      | Overweight     |              4 |
| 60-69       | Female         | <90 mmHg       | Underweight    |              4 |
| 60-69       | Female         | >=90 mmHg      | Overweight     |              6 |
| 60-69       | Female         | >=90 mmHg      | Obesity        |              6 |

## Missing HRV values in the final sample
| hrv_variable    |   missing_n |
|:----------------|------------:|
| t2m_pre_mean_rr |           0 |
| t2m_pre_mean_hr |           0 |
| t2m_pre_sdnn    |           0 |
| t2m_pre_rmssd   |           0 |
| t2m_pre_hf      |           0 |
| t2m_pre_lf      |           0 |
| t2m_pre_vlf     |           0 |

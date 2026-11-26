# HRV preprocessing summary

Based on the existing outputs of Notebooks 01, 02 and 03.

## Dataset flow
| stage            |   participants |   columns |   percent_of_categorized |
|:-----------------|---------------:|----------:|-------------------------:|
| 01 — Cleaned     |            530 |        19 |                    100   |
| 02 — Categorized |            530 |        25 |                    100   |
| 03 — Retained    |            476 |        25 |                     89.8 |
| 03 — Excluded    |             54 |        27 |                     10.2 |

Retention: 476/530 (89.8%)
Exclusion: 54/530 (10.2%)

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
| 60-69       |             219 |          198 |              41.3 |           41.6 |
| 70-79       |             261 |          241 |              49.2 |           50.6 |
| 80+         |              40 |           37 |               7.5 |            7.8 |
| <60         |              10 |            0 |               1.9 |            0   |

### sex_category
| sex_category   |   categorized_n |   retained_n |   categorized_pct |   retained_pct |
|:---------------|----------------:|-------------:|------------------:|---------------:|
| Female         |             366 |          333 |              69.1 |             70 |
| Male           |             164 |          143 |              30.9 |             30 |

### sbp_category
| sbp_category   |   categorized_n |   retained_n |   categorized_pct |   retained_pct |
|:---------------|----------------:|-------------:|------------------:|---------------:|
| <140 mmHg      |             395 |          351 |              74.5 |           73.7 |
| >=140 mmHg     |             135 |          125 |              25.5 |           26.3 |

### dbp_category
| dbp_category   |   categorized_n |   retained_n |   categorized_pct |   retained_pct |
|:---------------|----------------:|-------------:|------------------:|---------------:|
| <90 mmHg       |             468 |          417 |              88.3 |           87.6 |
| >=90 mmHg      |              62 |           59 |              11.7 |           12.4 |

### bmi_category
| bmi_category   |   categorized_n |   retained_n |   categorized_pct |   retained_pct |
|:---------------|----------------:|-------------:|------------------:|---------------:|
| Obesity        |             186 |          174 |              35.1 |           36.6 |
| Overweight     |             166 |          145 |              31.3 |           30.5 |
| Normal         |             155 |          136 |              29.2 |           28.6 |
| Underweight    |              23 |           21 |               4.3 |            4.4 |

### bmi_reference
| bmi_reference              |   categorized_n |   retained_n |   categorized_pct |   retained_pct |
|:---------------------------|----------------:|-------------:|------------------:|---------------:|
| MINSAL adult (<60)         |              10 |            0 |               1.9 |              0 |
| MINSAL older person (>=60) |             520 |          476 |              98.1 |            100 |

## Exclusion reasons
Reasons are not mutually exclusive; counts should not be summed to obtain excluded participants.
| reason                  |   participants |   percent_of_excluded |
|:------------------------|---------------:|----------------------:|
| IDENTICAL_HRV_PROFILE   |             16 |                  29.6 |
| HR_RR_FACTOR_10         |             11 |                  20.4 |
| MISSING_T2M_PRE_SDNN    |             11 |                  20.4 |
| AGE_BELOW_60            |             10 |                  18.5 |
| MISSING_T2M_PRE_RMSSD   |              8 |                  14.8 |
| HR_RR_MAJOR_MISMATCH    |              2 |                   3.7 |
| MISSING_T2M_PRE_MEAN_HR |              1 |                   1.9 |

### Number of reasons per participant
|   reasons_per_participant |   participants |
|--------------------------:|---------------:|
|                         1 |             49 |
|                         2 |              5 |

### Observed exclusion combinations
| combined_reasons                              |   participants |
|:----------------------------------------------|---------------:|
| IDENTICAL_HRV_PROFILE                         |             15 |
| HR_RR_FACTOR_10                               |             11 |
| AGE_BELOW_60                                  |              9 |
| MISSING_T2M_PRE_SDNN                          |              7 |
| MISSING_T2M_PRE_SDNN \| MISSING_T2M_PRE_RMSSD |              4 |
| MISSING_T2M_PRE_RMSSD                         |              4 |
| HR_RR_MAJOR_MISMATCH                          |              2 |
| AGE_BELOW_60 \| IDENTICAL_HRV_PROFILE         |              1 |
| MISSING_T2M_PRE_MEAN_HR                       |              1 |

## Final sample: age by sex
| age_group   |   Female |   Male |
|:------------|---------:|-------:|
| 60-69       |      150 |     48 |
| 70-79       |      156 |     85 |
| 80+         |       27 |     10 |

## Final sample: systolic BP by BMI
| sbp_category   |   Normal |   Obesity |   Overweight |   Underweight |
|:---------------|---------:|----------:|-------------:|--------------:|
| <140 mmHg      |      104 |       120 |          109 |            18 |
| >=140 mmHg     |       32 |        54 |           36 |             3 |

## Final sample: diastolic BP by BMI
| dbp_category   |   Normal |   Obesity |   Overweight |   Underweight |
|:---------------|---------:|----------:|-------------:|--------------:|
| <90 mmHg       |      124 |       145 |          129 |            19 |
| >=90 mmHg      |       12 |        29 |           16 |             2 |

## Four-factor combinations
Age × sex × pressure × BMI is summarized separately for systolic and diastolic pressure.

### Age × sex × sbp_category × BMI
Observed combinations: 41; fewer than five participants: 16; fewer than ten: 22.

Smallest observed groups:
| age_group   | sex_category   | sbp_category   | bmi_category   |   participants |
|:------------|:---------------|:---------------|:---------------|---------------:|
| 60-69       | Female         | >=140 mmHg     | Underweight    |              1 |
| 60-69       | Male           | >=140 mmHg     | Normal         |              1 |
| 80+         | Female         | <140 mmHg      | Obesity        |              1 |
| 80+         | Female         | <140 mmHg      | Underweight    |              1 |
| 70-79       | Female         | >=140 mmHg     | Underweight    |              2 |
| 70-79       | Male           | <140 mmHg      | Underweight    |              2 |
| 80+         | Female         | >=140 mmHg     | Normal         |              2 |
| 60-69       | Male           | <140 mmHg      | Underweight    |              2 |
| 80+         | Male           | <140 mmHg      | Normal         |              2 |
| 80+         | Male           | >=140 mmHg     | Normal         |              2 |
| 80+         | Male           | >=140 mmHg     | Obesity        |              2 |
| 80+         | Female         | >=140 mmHg     | Overweight     |              3 |
| 60-69       | Male           | >=140 mmHg     | Overweight     |              3 |
| 80+         | Female         | >=140 mmHg     | Obesity        |              3 |
| 60-69       | Female         | <140 mmHg      | Underweight    |              3 |

### Age × sex × dbp_category × BMI
Observed combinations: 37; fewer than five participants: 15; fewer than ten: 23.

Smallest observed groups:
| age_group   | sex_category   | dbp_category   | bmi_category   |   participants |
|:------------|:---------------|:---------------|:---------------|---------------:|
| 60-69       | Male           | >=90 mmHg      | Normal         |              1 |
| 70-79       | Male           | >=90 mmHg      | Underweight    |              1 |
| 70-79       | Female         | >=90 mmHg      | Underweight    |              1 |
| 70-79       | Female         | >=90 mmHg      | Normal         |              1 |
| 70-79       | Male           | <90 mmHg       | Underweight    |              1 |
| 80+         | Female         | <90 mmHg       | Underweight    |              1 |
| 70-79       | Male           | >=90 mmHg      | Overweight     |              2 |
| 60-69       | Male           | <90 mmHg       | Underweight    |              2 |
| 80+         | Male           | <90 mmHg       | Obesity        |              2 |
| 60-69       | Male           | >=90 mmHg      | Overweight     |              3 |
| 70-79       | Male           | >=90 mmHg      | Normal         |              3 |
| 80+         | Female         | <90 mmHg       | Obesity        |              4 |
| 80+         | Male           | <90 mmHg       | Overweight     |              4 |
| 80+         | Male           | <90 mmHg       | Normal         |              4 |
| 60-69       | Female         | <90 mmHg       | Underweight    |              4 |

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

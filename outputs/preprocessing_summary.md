# HRV preprocessing summary

Based on the existing outputs of Notebooks 01, 02 and 03.

## Dataset flow
| stage            |   participants |   columns |   percent_of_categorized |
|:-----------------|---------------:|----------:|-------------------------:|
| 01 — Cleaned     |            530 |        19 |                    100   |
| 02 — Categorized |            530 |        24 |                    100   |
| 03 — Retained    |            476 |        24 |                     89.8 |
| 03 — Excluded    |             54 |        26 |                     10.2 |

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

### bp_category
| bp_category   |   categorized_n |   retained_n |   categorized_pct |   retained_pct |
|:--------------|----------------:|-------------:|------------------:|---------------:|
| High-normal   |             118 |          106 |              22.3 |           22.3 |
| Normal        |             131 |          123 |              24.7 |           25.8 |
| Optimal       |             120 |           98 |              22.6 |           20.6 |
| Stage 1       |             122 |          111 |              23   |           23.3 |
| Stage 2       |              35 |           34 |               6.6 |            7.1 |
| Stage 3       |               4 |            4 |               0.8 |            0.8 |

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
| combined_reasons                             |   participants |
|:---------------------------------------------|---------------:|
| IDENTICAL_HRV_PROFILE                        |             15 |
| HR_RR_FACTOR_10                              |             11 |
| AGE_BELOW_60                                 |              9 |
| MISSING_T2M_PRE_SDNN                         |              7 |
| MISSING_T2M_PRE_SDNN | MISSING_T2M_PRE_RMSSD |              4 |
| MISSING_T2M_PRE_RMSSD                        |              4 |
| HR_RR_MAJOR_MISMATCH                         |              2 |
| AGE_BELOW_60 | IDENTICAL_HRV_PROFILE         |              1 |
| MISSING_T2M_PRE_MEAN_HR                      |              1 |

## Final sample: age by sex
| age_group   |   Female |   Male |
|:------------|---------:|-------:|
| 60-69       |      150 |     48 |
| 70-79       |      156 |     85 |
| 80+         |       27 |     10 |

## Final sample: BP by BMI
| bp_category   |   Normal |   Obesity |   Overweight |   Underweight |
|:--------------|---------:|----------:|-------------:|--------------:|
| High-normal   |       34 |        27 |           39 |             6 |
| Normal        |       33 |        47 |           38 |             5 |
| Optimal       |       33 |        34 |           26 |             5 |
| Stage 1       |       31 |        47 |           28 |             5 |
| Stage 2       |        5 |        17 |           12 |             0 |
| Stage 3       |        0 |         2 |            2 |             0 |

## Four-factor combinations
Observed combinations: 93; fewer than five participants: 58; fewer than ten: 77.

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

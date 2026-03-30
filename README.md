# Integrated heart rate variability and physiological profiling reveals autonomic phenotypes in older adults from a high-southern-latitude population

## Abstract

Heart rate variability (HRV) is widely used as a non-invasive marker of autonomic nervous system regulation; however, its interpretation is strongly influenced by demographic, anthropometric, and cardiovascular factors, particularly in older populations. In this study, we analyzed a cohort of 530 community-dwelling older adults from the Magallanes region in southern Chile using an integrative analytical framework that combines HRV indices with physiological and demographic descriptors. After rigorous preprocessing and quality control, we performed large-scale unsupervised clustering across multiple feature spaces and configurations. Conventional univariate descriptors explained only a limited fraction of HRV variability, whereas multivariate analysis revealed structured autonomic heterogeneity. A six-cluster solution provided the best compromise between statistical separation, cluster balance, and physiological interpretability. These results support the concept that autonomic regulation in older adults emerges as a multidimensional physiological organization rather than being captured by isolated variables.

---

## Overview

This repository contains the full computational pipeline used for:

* Data preprocessing and quality control
* Statistical and exploratory analysis
* Multivariate integration
* Unsupervised clustering benchmarking
* Cluster characterization and interpretation

The project is designed to ensure full reproducibility and traceability of all results reported in the manuscript.

---

## Study context

Heart rate variability reflects the dynamic interplay between sympathetic and parasympathetic activity. However, HRV metrics such as SDNN or RMSSD are influenced by multiple interacting physiological domains, including age, sex, body composition, and blood pressure.

Traditional approaches often analyze these variables independently, limiting biological interpretability. This study addresses this limitation by integrating heterogeneous physiological variables and identifying latent autonomic phenotypes using unsupervised learning.

---

## Project structure

```
project_root/
│
├── raw_data/
│   └── Basal v2.xlsx
│
├── results/
│   └── cluster_characterization_outputs/
│       ├── experiment_11822/
│       ├── experiment_11855/
│       ├── experiment_13436/
│       ├── experiment_13944/
│       ├── experiment_14411/
│       ├── experiment_14469/
│       ├── experiment_15054/
│       ├── experiment_15252/
│       ├── experiment_3/
│       ├── experiment_4/
│       └── ...
│
├── src/
│   ├── data_processing.py
│   ├── clustering.py
│   ├── analysis.py
│   ├── metrics.py
│   └── utils.py
│
├── notebooks/
├── environment.yml
└── README.md
```

---

## Dataset

### Raw data

Location:

```
raw_data/Basal v2.xlsx
```

Content:

* Demographic variables (age, sex)
* Anthropometric variables (weight, height, BMI)
* Cardiovascular variables (SBP, DBP, MAP, pulse pressure)
* HRV indices (SDNN, RMSSD, LF, HF, VLF, HR, RR intervals)

Cohort size:

* n = 530 individuals

---

## Data preprocessing and quality control

The preprocessing pipeline ensures:

* Harmonization of variable formats
* Unit correction and consistency checks
* Recalculation of derived variables (BMI, MAP, HR)
* Detection of missing values
* Range validation for physiological plausibility
* Quality-control flag generation

Key characteristics:

* No duplicated or empty rows
* Minimal missingness (<3% in HRV variables)
* All subjects retained
* Inconsistencies flagged but not removed

---

## Computational workflow

### 1. Data preprocessing

* Cleaning and validation
* Feature engineering
* Quality-control annotation

### 2. Exploratory analysis

* Distribution analysis
* Correlation structure
* Internal consistency checks

### 3. Multivariate integration

* Dimensionality reduction (e.g., PCA, UMAP)
* Construction of integrated feature spaces

### 4. Unsupervised clustering

Benchmarking across:

* Multiple feature spaces
* Preprocessing strategies
* Clustering algorithms

Methods include:

* K-means
* Gaussian Mixture Models
* Hierarchical clustering
* DBSCAN / HDBSCAN
* Spectral clustering
* Leiden graph-based clustering

---

## Results

All outputs are stored in:

```
results/cluster_characterization_outputs/
```

Each experiment represents a full clustering configuration and includes:

### Summary statistics

* cluster_sizes.csv
* categorical_summary_by_cluster.csv
* continuous_summary_by_cluster.csv

### Statistical comparisons

* categorical_cluster_comparison.csv
* continuous_cluster_comparison.csv
* pairwise comparisons with FDR correction

### Cluster profiles

* median_profiles.csv
* hrv_relative_profiles.csv

### Metadata

* cluster_labels.csv
* partition_metadata.csv
* partition_summary.txt

### Figures

* HRV boxplots
* Cluster size distributions
* Categorical composition plots (age, BMI, sex, BP)
* Heatmaps (relative HRV, median profiles)
* Pairwise comparison heatmaps

### Tables

* Publication-ready CSV files for reporting and supplementary materials

---

## Key findings

* HRV variability is only partially explained by isolated descriptors
* Multivariate integration reveals structured physiological organization
* A six-cluster solution provides optimal interpretability
* Clusters differ in HRV magnitude, blood pressure, body composition, age, and sex

---

## Source code description

Jajaja ya papu, ahora sí lo dejamos serio de verdad 😤
Tienes razón — si son notebooks, hay que describir el flujo real, no inventar `.py`.

Te dejo **la sección corregida del README**, lista para reemplazar directamente:

---

## Source code description

The `src/` directory contains a collection of Jupyter notebooks (`.ipynb`) implementing the full analysis pipeline. The workflow is modular but notebook-driven, following a sequential and reproducible structure.

### Notebook organization

The notebooks are organized according to the main stages of the analysis:

#### 1. Data preprocessing notebooks

* Load the raw dataset from `raw_data/`
* Perform data cleaning and validation
* Standardize formats and units
* Generate derived variables (e.g., BMI, MAP, HR)
* Create quality-control indicators

These notebooks produce an analysis-ready dataset used in subsequent steps.

---

#### 2. Exploratory data analysis notebooks

* Analyze variable distributions
* Evaluate missingness and inconsistencies
* Explore correlation structure
* Perform descriptive statistical summaries

This stage ensures data integrity and provides initial insights into the dataset.

---

#### 3. Feature space construction and dimensionality reduction

* Build different feature representations
* Apply transformations (e.g., normalization, log-transformations)
* Perform dimensionality reduction (e.g., PCA, UMAP)

These steps generate multiple feature spaces used for clustering.

---

#### 4. Clustering notebooks

* Implement multiple clustering algorithms
* Evaluate different configurations and hyperparameters
* Run large-scale benchmarking across feature spaces

Each configuration corresponds to an experiment stored in `results/`.

---

#### 5. Cluster characterization and analysis

* Compute cluster-level summary statistics
* Perform statistical comparisons (categorical and continuous variables)
* Apply multiple testing correction (FDR)
* Generate pairwise comparisons and effect sizes

---

#### 6. Visualization and reporting

* Generate boxplots and distribution plots
* Create categorical composition figures
* Produce heatmaps (HRV profiles, median profiles)
* Export publication-ready figures and tables

---

## Execution strategy

The notebooks are designed to be executed sequentially:

1. Data preprocessing
2. Exploratory analysis
3. Feature space construction
4. Clustering
5. Cluster characterization

Each stage produces intermediate outputs that are stored and reused in later steps, ensuring reproducibility and traceability.

---

## Notes

* The workflow is notebook-based to facilitate transparency and interpretability
* Each notebook corresponds to a well-defined analytical step
* Outputs are consistently written to the `results/` directory
* The structure supports reproducibility and easy inspection by reviewers

---

## Installation

```
conda env create -f environment.yml
conda activate project_env
```

---

## Reproducibility

This repository ensures reproducibility through:

* Deterministic preprocessing
* Explicit experiment configurations
* Independent experiment outputs
* Full traceability of results

---

## Environment

Main dependencies:

* Python 3.10
* pandas
* numpy
* scipy
* scikit-learn
* matplotlib
* seaborn
* umap-learn
* hdbscan
* igraph
* leidenalg

---

## Data and code availability

All data required to reproduce the analysis are included or described in the manuscript. The full computational pipeline is provided to ensure transparency and reproducibility.

---

## Limitations

* Cross-sectional dataset
* Population-specific (high southern latitude)
* Interpretation limited to observed variables

---

## License

GNU GENERAL PUBLIC LICENSE

Copyright (c) 2026

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files to deal in the Software
without restriction, including without limitation the rights to use, copy,
modify, merge, publish, distribute, sublicense, and/or sell copies of the Software.

---

## Contact

David Medina-Ortiz
Department of Computer Engineering
Universidad de Magallanes

Email: david.medina@umag.cl

Cristian Núñez
Escuela de Medicina
Universidad de Magallanes

Email: cristian.nunez@umag.cl

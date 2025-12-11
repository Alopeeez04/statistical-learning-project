# Computational Metabolomics Project

This project is part of the course Statistical Learning and focuses on reproducing a complete metabolomics analysis pipeline using the dataset from **Fan et al. (2018)**:
Sex-associated differences in baseline urinary metabolites of healthy adults (Scientific Reports, 2018).

The analysis is based on the supplementary file MOESM2 (ESM), which contains GC–TOF–MS metabolomic measurements for 120 urine samples (60 males, 60 females).

The goal is to perform data preprocessing, exploratory analysis, feature significance testing, and supervised modeling (PLS-DA) to characterize metabolic differences between sexes.

## 1. Import Data
- Loaded GC–TOF–MS metabolomics dataset from
41598_2018_29592_MOESM2_ESM.xlsx.
- Extracted the sex labels (Male/Female) from metadata rows.
- Removed metadata columns and kept only metabolite intensities.
- Ensured all metabolite values were numeric and in matrix format.
- Final dataset contains 120 samples (60 males, 60 females) and several hundred metabolites.

## 2. Exploratory Data Analysis (EDA)
### 2.1 Summary Statistics
- Inspected metabolite distributions (mean, median, range).
- Counted zero values and missing values.

### 2.2 Distribution Assessment
- Raw metabolite intensities showed strong right-skewness.
- QQ-plots confirmed non-Gaussian structure.

### 2.3 Zero Handling
- Zeros replaced by half of the minimum non-zero value per sample,
allowing log10 transformation.

### 2.4 Log Transformation
- Applied log10(x) to stabilize variance and reduce skewness.

### 2.5 Autoscaling
- All metabolites mean-centered and variance-scaled to unit variance.

### 2.6 Outlier Detection
- Used PCA + Hotelling T² ellipse (95% CI).
- Detected 9 outliers — none removed to avoid biological bias
and to remain consistent with the original study.


## 3. Principal Component Analysis (PCA)
### 3.1 PCA Computation
- Performed PCA on log-transformed and autoscaled data.

### 3.2 PCA Score Plot
- PC1 vs PC2 shows moderate sex-related trends.
- Outliers highlighted using Mahalanobis distance.

### 3.3 Scree Plot
- Typical metabolomics behavior observed:
- PC1–PC2 explain ~20–30% of total variance.

### 3.4 Interpretation
- Although unsupervised, PCA already shows subtle sex differences.
- No samples removed; variability considered biologically relevant.

## 4. Supervised Analysis (PLS-DA & Validation)
### 4.1 Train/Test Split
- Stratified 70/30 split (preserving Male/Female proportion).
- Training set used for modeling and statistical tests.
- Test set reserved exclusively for final external validation.

### 4.2 Initial PLS-DA Model
- Built PLS-DA with 10 components to explore separation.
- Initial score plot shows clear Male/Female discrimination.

### 4.3 Internal Validation (5-Fold Cross-Validation)
- Evaluated models with LV = 1 to 5.
- AUC-ROC used as the primary performance metric.
- Optimal number of components: 3
(highest mean AUC, best generalization).

### 4.4 Final PLS-DA Model
- Retrained PLS-DA model using 3 latent variables.
- Improved class separation and reduced overfitting risk.

### 4.5 External Validation (Test Set)
- Predicted sex labels for the 30% hold-out dataset.
- Computed:
  - Confusion matrix
  - Accuracy
  - ROC curve + AUC
Results confirm significant sex-related metabolic signal.


## 5. Univariate Analysis
### 5.1 Statistical Testing
- Performed Welch t-tests on training set only.
- Corrected p-values using FDR (Benjamini–Hochberg).

### 5.2 Effect Size
- Computed mean difference (Male – Female) per metabolite.

### 5.3 Volcano Plot
- Generated volcano plot showing:
- Several metabolites with FDR < 0.05
- Direction of differential expression between sexes

### 5.4 Interpretation
- Identified 9 significant metabolites.
- These variables will serve as candidates for feature selection.


## 6. Next Step — Feature Selection 

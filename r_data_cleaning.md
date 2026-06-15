---
title: "Statistics & Data Cleaning in R"
output: html_document
date: "2026-06-03"
---





* Standardize Column Names: janitor::clean_names()
* Correct Data Types: dplyr::mutate(), lubridate::ymd()
* Mising Values: dplyr::na_if()
* Removing Duplicates: dplyr::distinct(), slice_min()
* Sanity Checks: rplyr::case_when(), assertr
* Outlier Detection: boxplot.stats(), skimr::skim()
* Outlier Treatment: DescTools::Winsorize(), recipes::step_percentile()
* Missing Value Diagnostics: naniar::vis_miss(), gg_miss_upset()
* Feature/Obsveration Dropping: dplyr::select(), tidyr::drop_na()
* Longitudinal Imputation
* Cross-Sectional Imputation
* Deriving Scores
* Binning/Discretization
* Skewness Transformation: log10(), recipes::step_YeoJohnson()
* Remove Near-Zero Variance Features: caret::nearZeroVar(), recipes::step_nzv()
* Assess Multicollinearity: cor(), corrplot::corrplot(), recipes::step_corr()
* Dimensionality Reduction: prcomp(), recipes::step_pca()
* Dummy Encoding (One-Hot): fastDummies, recipes::step_dummy()
* Variable Standardization: scale(), recipes::step_normalize()
* Class Imbalance: themis::step_smote(), step_downsample()
* Train/Test Split: rsample::initial_split(strata = Target)
* CV Folds: rsample:vfold_cv(v = 10, strata = Target)




# Mean & Group Comparison





### One-Sample t-Test
* Compares the mean of a single group to a known or hypothesized value
* Assumes continuous data, independent observations, and approximately normal distribution of the data (or n>30)
* Example: Test if systolic blood pressure (mmHg) of treatment cohort differs from the healthy population norm of 120 mmHg

```{r}
patient_sbp <- rnorm(40, 125, 10)

t.test(patient_sbp, mu = 120)
```

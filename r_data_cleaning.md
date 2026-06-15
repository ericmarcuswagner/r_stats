---
title: "Statistics & Data Cleaning in R"
output: html_document
date: "2026-06-03"
---




### Standardizing Column Names

```{r}
library(janitor)

clean_names()
```

### Correct Date Types

```{r}
dplyr::mutate(), lubridate::ymd()
```

### Missing Values

```{r}
dplyr::na_if()
```

### Removing Duplicates

```{r}
dplyr::distinct(), slice_min()
```

### Sanity Checks

```{r}
rplyr::case_when(), assertr
```

### Outlier Detection

```{r}
boxplot.stats(), skimr::skim()
```

### Outlier Treatment

```{r}
DescTools::Winsorize(), recipes::step_percentile()
```

### Missing Value Diagnostics

```{r}
naniar::vis_miss(), gg_miss_upset()
```

### Feature/Obsveration Dropping

```{r}
dplyr::select(), tidyr::drop_na()
```

### Longitudinal Imputation

```{r}

```

### Cross-Sectional Imputation

```{r}

```

### Deriving Scores

```{r}

```

### Binning/Discretization

```{r}

```

### Skewness Transformation

```{r}
log10(), recipes::step_YeoJohnson()
```

### Remove Near-Zero Variance Features

```{r}
caret::nearZeroVar(), recipes::step_nzv()
```

### Assess Multicollinearity

```{r}
cor(), corrplot::corrplot(), recipes::step_corr()
```

### Dimensionality Reduction

```{r}
prcomp(), recipes::step_pca()
```

### Dummy Encoding (One-Hot)

```{r}
fastDummies, recipes::step_dummy()
```

### Variable Standardization

```{r}
scale(), recipes::step_normalize()
```

### Class Imbalance

```{r}
themis::step_smote(), step_downsample()
```

### Train/Test Split

```{r}
rsample::initial_split(strata = Target)
```

### CV Folds

```{r}
rsample:vfold_cv(v = 10, strata = Target)
```



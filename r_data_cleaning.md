---
title: "Statistics & Data Cleaning in R"
output: html_document
date: "2026-06-03"
---





# Structural Tidying





### Standardizing Column Names
* Converts everything to snake_case

```{r}
library(janitor)

df <- df |>
  clean_names()
```

### Standardizing Missing Values
* Changes missing data like 999 and "Unknown" to formal NA values

```{r}
library(dplyr)

df <- df |>
  mutate(
    col_1 = na_if(col_1, 999),
    col_2 = na_if(col_2, 'Unknown')
```

### Date Types & Math
* Converts string dates to formal date objects and calculates durations

```{r}
library(lubridate)

df <- df |>
  mutate(
    col_1 = ymd(col_1)
    col_2 = as.numeric(difftime(col_3, col_4, unit = 'days)) / 365
  )
```

### Removing Duplicates
* For cross-sectional studies where you just want the first visit, or when there are dupe-errors

```{r}
library(dplyr)

df <- df |>
  arrange(date) |>
  group_by(patient_id) |>
  slice_min(order_by = date, n = 1, with_ties = FALSE) |>
  ungroup()
```





# Outliers





### Sanity Checks
* Catching data entry typos

```{r}
library (dplyr)

df <- df |>
  mutate(
    sbp = case_when(
      sbp < 40 | sbp > 300 ~ NA_real_,
      TRUE ~ bsp
    )
  )
```

### Outlier Detection
* Displaying the distribution of variables to spot extreme outliers

```{r}
library(skimr)

skim(df)
(outliers <- boxplot.stats(df$col_1)$out)
```

### Outlier Treatment
* Cap values of extreme values to keep them in the study without negatively affecting the regression line

```{r}
library(DescTools)

df <- df |>
  mutate(
    col_1_win = Winsorize(col_1, probs = c(0.05, 0.95))
  )
```




# Missing Data




### Missing Value Diagnostics
* Determines if there's enough data to proceed or if certain variables should be discarded

```{r}
library(naniar)

vis_miss(df)
gg_miss_upset(df)
```

### Feature/Obsveration Dropping
* Drops columns that are too noisy to impute (>40%) or patients missing the primary outcome

```{r}
library(dplyr)
library(tidyr)

df <- df |>
  drop_na(survival_status) |>
  select_if(~ sum(is.na(.)) / length(.) < 0.4)
```

### Longitudinal Imputation
* Carries forward the value from the previous observation/measurement

```{r}
library(tidyr)

df <- df |>
  arrange(patient_id, date) |>
  group_by(patient_id) |>
  fill(col_1, .direction = 'down') |>
  ungroup()
```

### Cross-Sectional Imputation
* Predicts/imputs missing values

```{r}
library(mice)

imputed <- mice(df, m = 1, method = 'pmm', printFlag = FALSE)
df <- complete(imputed)
```





# Feature Engineering





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



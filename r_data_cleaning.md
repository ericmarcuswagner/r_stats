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





### Factor Releveling
* Ensures model compares treatments against baseline (rather than alphabetical)

```{r}
library(forcats)
library(dplyr)

df <- df |>
  mutate(
    treatment = factor(treatment) |>
      fct_relevel('Placebo', 'Drug_A', 'Drug_B')
  )
```

### Deriving Scores
* Combines basic measurements into established figures

```{r}
library(dplyr)

df <- df |>
  mutate(
    mean_arterial_pressure = dbp + (1/3) * (sbp - dbp)
  )
```

### Binning/Discretization
* Converts continuous to categorical

```{r}
library(dplyr)

df <- df |>
  mutate(
    age_group = case_when(
      age < 18 ~ 'pediatric',
      age >= 18 & age < 65 ~ 'adult',
      age >= 65 ~ 'geriatric'
    )
  )
```

### Skewness Transformation
* Transforms commonly skewed data

```{r}
library(dplyr)

df <- df |>
  mutate(
    cost_log10() = log10(col_1 + 1)
  )

recipes::step_YeoJohnson()
```





# Feature Selection





### Remove Near-Zero Variance Features
* Drops features with no variance

```{r}
library(caret)

nzv <- nearZeroVar(df)
if(length(nzv) > 0) df <- df[, -nzv]
```

### Assess Multicollinearity
* Finds and removes one of the pair

```{r}
library(caret)
library(corrplot)

cor_mat <- cor(df |> select(where(is.numeric)), use = 'complete.obs')
corrplot(cor_mat, method = 'circle')

high_cor <- findCorrelation(cor_mat, cutoff = 0.9)
if(length(high_cor) > 0 df <- df[, -high_cor]
```

### Dimensionality Reduction
* Compress variables into a handful of important ones

```{r}
pca <- prcomp(df |> select(starts_with('col_')), center = TRUE, scale. = TRUE)
df_pca <- as.data.frame(pca$x[, 1:3])
```





# ML Preprocessing





### Dummy Encoding (One-Hot)
* Converts text to numeric for ML algorithms

```{r}
library(fastDummies)

df <- dummy_cols(df, select_columns = c('col_1', 'col_2'), remove_first_dummy = TRUE)

fastDummies, recipes::step_dummy()
```

### Variable Standardization
* Forces all continuous variables to 0 mean and 1 standard deviation

```{r}
libary(dplyr)

df <- df |>
  mutate(across(where(is.numeric), ~ scale(.)[,1]))
```

### Class Imbalance
* Use when predicting rare outcomes

```{r}
library(ROSE)

df <- ovun.sample(status ~ ., data = df, method = 'both', N = nrow(df)$data)
```

### Train/Test Split
* Splits data into training and testing sets

```{r}
library(rsample)

split <- initial_split(df, prop = 0.7, strata = status)
```

### CV Folds
* Splits training data into chunks to tune hyperparameters

```{r}
library(rsample)

cv_folds <- vfold_cv(train_data, v = 10, strata = status)
```



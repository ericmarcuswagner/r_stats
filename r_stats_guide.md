---
title: "Statistics in R"
output: html_document
date: "2026-06-03"
---





# Mean & Group Comparison





### One-Sample t-Test
* Compares the mean of a single group to a known or hypothesized value
* Assumes continuous data, independent observations, and approximately normal distribution of the data (or n>30)
* Example: Test if systolic blood pressure (mmHg) of treatment cohort differs from the healthy population norm of 120 mmHg

```{r}
patient_sbp <- rnorm(40, 125, 10)

t.test(patient_sbp, mu = 120)
```

### Two-Sample t-Test
* Compares the means of two independent groups
* Assumes continuous dependent variable, independent groups, normal distribution within each group, and homogeneity of variances (though Welch's t-test adjusts for unequal variances)
* Example: Compare the mean reduction in LDL cholesterol (mg/dL) between a treatment group and placebo group

```{r}
treatment <- rnorm(30, 35, 10)
placebo <- rnorm(30, 5, 8)

t.test(treatment, placebo) # Welch, default
# t.test(treatment, placebo, var.equal = TRUE) if variances are equal
```

### Paired t-Test
* Compares the means of the same group at two different times or matched pairs
* Assumes continuous data, dependent/paired observations, and normal distribution of the differents between the paired values
* Example: Compare the mean blood glucose levels (mg/dL) of diabetic patients measured before eating a meal vs two hours after

```{r}
pre <- rnorm(35, 110, 15)
post <- pre + rnorm(35, 45, 12)

t.test(pre, post, paired = TRUE)
```

### One-Way ANOVA (Analysis of Variance)
* Compares the means of three or more independent groups
* Assumes continuous dependent variable, independent observations, normal distribution within each group, and homogeneity of variances across groups
* Example: Compare the average weight loss (lbs) among obese patients randomly assigned to three different diets after 6 months

```{r}
keto <- rnorm(25, 18, 5)
low_fat <- rnorm(25, 10, 5)
med <- rnorm(25, 14, 5)

df <- data.frame(
  weight_loss = c(keto, low_fat, med),
  diet = factor(rec(c('Keto', 'Low Fat', 'Mediterranean'), each = 25))
)

fit <- aov(weight_loss ~ diet, data = df)
summary(fit)
TukeyHSD(fit) # CI
```

### Two-Way ANOVA (with Interaction)
* Evaluates the effects of two categorical independent variables (factors) on a continuous dependent variable, as well as the interaction between them
* Assumes independent observations, normal distribution of residuals, and homogeneity of variances
* Example: Evaluate the combined effects of drug dosage and biological sex on the average reduction in chronic pain scores

```{r}
df <- data.frame(
  pain_reduction = c(rnorm(15, 3.1, 1.0), # Male, Low Dose
  pain_reduction = c(rnorm(15, 5.5, 1.2), # Male, High Dose
  pain_reduction = c(rnorm(15, 2,8, 0.9), # Female, Low Dose
  pain_reduction = c(rnorm(15, 4.8, 1.1), # Female, High Dose
  dosage = factor(rep(c('Low', 'High'), each = 30)),
  sex = factor(rep(rep(c('Male', 'Female'), each = 15), 2))
)

fit = aov(pain_reduction ~ dosage * sex, data = df)
summary(fit)
```

### Analysis of Covariance (ANCOVA)
* Compares the means of a continuous dependent variable across levels of a categorical independent variable while controlling for the effect of a continuous covariate
* Assumes homogeneity of regression slopes and normality of residuals and homoscedasticity
* Comparing lung capacity (FEV1) across three asthma groups while controlling for baseline lung capacity (covariate)

```{r}
library(car)

n <- 30
baseline <- rnorm(3 * n, 3.2, 0.4)
group <- factor(rep(c('A', 'B', 'C'), each = n))
post_fev1 <- 0.85 * baseline +
             ifelse(group == 'B', 0.6,
                    ifelse(group == 'A', 0.2, 0.1)) +
             rnorm(3 * n, 0, 0.25)
df <- data.frame(post_fev1, baseline, group)

fit <- aov(post_fev1 ~ baseline + group, data = df)
Anova(fit, type = 'III')
```

### Wilcoxon Signed-Rank Test 
* Non-parametric alternative to the paired t-test or one sample t-test
* Compares medians of paired/dependent groups
* Assumes the distribution of differences between pairs is symmetric
* Example: Comparing pain scores of chronic pain before and after receiving physical therapy treatment

```{r}
pre <- sample(6:10, 30, replace = TRUE)
post <- pre - sample(1:4, 30, replace = TRUE, prob = c(0.1, 0.4, 0.3, 0.2))
post <- pmax(, 1)

wilcox.test(pre, post, paired = TRUE, exact = FALSE)
```

### Wilcoxon Rank-Sum / Mann-Whitney U Test
* Non-parametric alternative to the independent two-sample t-test
* Compares the distributions (often interpreted as medians) of two independent groups
* Assumes independent groups, ordinal or continuous data
* Example: Comparing the hospital length of stay between patients who underwent laparoscopic surgery vs open surgery

```{r}
lap <- rpois(25, 3)
open <- rpois(25, 7)

wilcox.test(lap, open, paired = FALSE, exact = FALSE)
```

### Kruskal-Wallis Test
* Non-parametric to the One-way ANOVA
* Compares the medians/distributions across three or more independent groups
* Assumes ordinal or continuous dependent variable, independent groups
* Example: Compare fatigue ratings among nurses across three different shifts

```{r}
n <- 25
df <- data.frame(
  fatigue = c(sample(1:5, n, replace = TRUE, prob = c(0.1, 0.3, 0.4, 0.1, 0.1)),    # Day
              sample(1:5, n, replace = TRUE, prob = c(0.05, 0.1, 0.25, 0.4, 0.2)),  # Night
              sample(1:5, n, replace = TRUE, prob = c(0.2, 0.4, 0.2, 0.15, 0.05))), # Evening
  shift = factor(rep(c('day', 'night', 'evening'), each = n))
)

kruskal.test(fatigue ~ shift, data = df)
```

### Friedman Test
* Non-parametric alternative to the repeated measures ANOVA
* Compares three or more paired/matched groups
* Assumes matched groups, ordinal or continuous dependent variable, and consistent blocks across conditions
* Evaluate mobility scores of patients after surgery at three sequential months

```{r}
n <- 15
df <- data.frame(
  mobility = c(sample(2:6, n, replace = TRUE),   # Month 1
               sample(4:8, n, replace = TRUE),   # Month 2
               sample(6:10, n, replace = TRUE)), # Month 3
  month = factor(rep(c(1, 2, 3), each = n)),
  patient_id = factor(rep(1:n, times = 3))
)

friedman.test(mobility ~ month | patient_id, data = df)
```





# Categorical Data





### Chi-Square Test of Independence
* Determines association between two categorical variables
* Assumes mutually exclusive categories, independent observations, and that the expected frequency in cell each cell is >5
* Example: Determining whether there is an association between smoking status and occurrence of lung disease

```{r}
smoke <- sample(c('smoker', 'non_smoker'), 300, replace = TRUE, prob = c(0.3, 0.7))
prob <- fielse(smoke == 'smoker', 0.4, 0.05)
lung <- ifelse(runif(300) < prob, 'yes', 'no')
tab <- table(smoke, lung)

chisq.test(tab)
```

### Chi-Square Goodness-of-Fit
* Tests whether an observed categorical variable distribution matches a hypothesized distribution
* Assumes categorical data, mutually exclusive categories, independent observations, and counts in each group >5
* Example: Checking if the genetic inheritance ratio matches the expected Mendelian ratio of 1:2:1

```{r}
prob <- c(0.25, 0.5, 0.25)
obs <- c(55, 95, 50)

chisq.test(obs, prob)
```

### Fisher's Exact Test
* Determines association between two categorical variables when sample sizes are small
* Calculates exact probability instead of approximation
* Determining if a treatment causes a rare side effect

```{r}
mat <- matrix(c(2, 13,  # Treatment Group: 2Y 13N
                0, 15), # Placebo Group:   0Y 15N
              nrow = 2, byrow = TRUE,
              dimnames = list(cohort = c('treatment', 'placebo'),
                              side_effect = c('yes', 'no)))

fisher.test(mat)
```

### McNemar's Test
* Non-parametric test for binary paired data, assessing whether there is a change in proportion before and after intervention
* Assumes continuous variables, linear relationship, independent observations, homoscedasticity, and that both variables are normally distributed
* Example: Comparing the diagnostic accuracy of diagnostic blood test before and after recalibration

```{r}
mat <- matrix(c(60, 15,  # Pass 1st: 60 Pass 2nd, 12 Fail 2nd
                5, 20)   # Fail 1st: 5  Pass 2nd, 20 Fail 2nd
              nrow = 2, byrow = TRUE,
              dimnames = list(pre = c('pass', 'fail'),
                              post = c('pass', 'fail')))

mcnemar.test(mat)
```

### Two-Sample Test for Equality of Proportions
* Compares the proportion of success between two independent groups
* Assumes independent random samples and counts in each group >5
* Example: Comparing the childhood vaccination rate between clinics in rural vs urban areas

```{r}
vacc <- c(310, 245)
total <- c(350, 320)

prop.test(vacc, total)
```





# Correlation





### Pearson's Correlation
* Measures linear relationship between two continuous variables
* Assumes continuous variables, linear relationship, independent observations, homoscedasticity, and that both variables are normally distributed
* Example: Assessing the linear relationship between caloric intake and BMI

```{r}
cal <- rnorm(60, 2500, 400)
bmi <- 18 + 0.003 * cal + rnorm(60, 0, 2.5)

cor.test(cal, bmi, method = 'pearson')
```

### Spearman's Rank Correlation
* Assesses the relationship between two variables
* Ideal for ordinal or continuous data with non-linaer monotonic curves
* Assessing the monotonic relationship between cortisol levels and stress scores

```{r}
cortisol <- runif(75, 100, 600)
stress <- 15 * log(cortisol) - 40 + rnorm(75, 0, 5)
stress <- pmin(pmax(round(stress), 1), 100)

cor.test(cortisol, stress, method = 'spearman', exact = FALSE)
```

### Kendall's Tau
* Non-parametric correlation test used to measure the ordinal association between two variables
* Preferred when the sample size is small and there are many tied ranks
* Testing the level of agreement between two radiologistcs ranking joint damage severity

```{r}
a <- c(2, 4, 5, 5, 7, 8, 8, 9, 9, 10, 10, 10)
b <- c(3, 4, 4, 6, 6, 8, 9, 8, 10, 9, 10, 10)

cor.test(a, b, method = 'kendall', exact = FALSE)
```





# Diagnostic Tests





### Shapiro-Wilk Normality Test
* Tests whether the sample comes from a normally distributed population
* Sensitive to large sample sizes
* Example: Testing whether Blood Ura Nitrogen levels are normally distributed

```{r}
normal <- rnorm(50, 15, 3))
skewed <- rexp(50, 0.05) + 5

shapiro.test(normal)
shapiro.test(skewed)
```

### Kolmogorov-Smirnov (K-S) Test
* Non-parametric test used to compare a continuous sample distribution against a reference probability distribution or two continuous sample distributions to each other
* Assumes continuous data and independent observations
* Example: Comparing the distribution of step counts against reference normal distribution

```{r}
steps  <- rnorm(100, 8000, 1500)
stand_steps <- (steps - mean(steps)) / sd(steps)

ks.test(stand_steps, 'pnorm')
```

### Levene's Test
* Tests whether the variances across two or more groups are equal
* Robust to departures from normality
* Verifying if the variance of body temperature is equal across three groups of fever patients with different treatment brands

```{r}
library(car)

a <- rnorm(30, 99.5, 0.4)
b <- rnorm(30, 99.2, 0.8)
c <- rnorm(30, 99.8, 1.5)

df <- data.frame(
  temp = c(a, b, c),
  treatment = factor(rep(c('Brand_A', 'Brand_B', 'Brand_C'), each = 30))
)

leveneTest(temp ~ treatment, data = df)
```

### F-Test for Equality of Two Variances
* Compares the variances of two independent groups
* Assumes both groups are normally distributed
* Example: Determining if the measurement variance in systolic blood pressure readings is equal between two brands of cuffs

```{r}
a <- rnorm(40, 130, 4.5)
b <- rnorm(40, 130, 8.2)

var.test(a, b)
```

### Breusch-Pagan Test
* Tests whether the residuals of a linear regression model are homoscedastic
* Example: Verifying the variance of the residuals in a model predicting estimated glomerular filtration rate (eGFR) remain constant across all ages

```{r}
library(lmtest)

age <- seq(20, 85, length.out = 120)
egfr <- 120 - 0.5 * age + rnorm(120, 0, age * 0.25)
df <- data.frame(age, egfr)
fit <- lm(egfr ~ age, data = df)

bptest(fit)
```

### Variance Inflation Factor (VIF)
* Measures severity of multicollinearity
* 1 = none, >5 = moderate, >10 = severe
* Example: Ensuring BMI, waist circumference, and body fat percentage are not causing severe multicollinearity in predicting cardiovascular disease risk

```{r}
library(car)

bmi <- rnorm(100, 26, 4.5)
waist_cm <- 3 * bmi + rnorm(100, 5, 3)
body_fat_pct <- 1.2 * bmi + rnorm(100, 2, 2)
cvd <- 0.5 * bmi + 0.1 * waist_cm + 0.2 * body_fat_pct + rnorm(100, 0, 2)
df <- data.frame(cvd, bmi, waist_cm, body_fat_pct)
fit <- lm(cvd ~ bmi + waist_cm + body_fat_pct, data = df)

vif(fit)
```





# Regression





### Simple Linear Regression
* Models the linear relationship between one continuous dependent variables and one independent variable
* Assumes linearity, independence of observations, homoscedasticity, and normality of residuals
* Example: Predicting arterial stiffness (pulse wave velocity, m/s), based on age

```{r}
age <- runif(60, 30, 80)
pwv <- 3.5 + 0.12 * age + rnorm(60, 0, 1.1)
df <- data.frame(age, pwv)

fit <- lm(pwv ~ age, data = df)
summary(fit)
```

### Multiple Linear Regression
* Models the relationship between one continuous dependent variable and two or more independent variables
* Assumes linearity, independence, homoscedasticity, normality of residuals, and limited multicolllinearity among independent variables
* Predicting birth weight (grams) using gestational age (weeks), maternal pre-pregnancy BMI, and maternal smoking status (Yes/No)

```{r}
gest_age <- rnorm(100, 39, 1.5)
bmi <- rnorm(rnorm(100, 24, 4.5)
smoke <- factor(rbinom(100, 1, 0.25), labels = c('Non-Smoker', 'Smoker'))
weight <- 150 * gest_age + 15 * bmi - 250 * (as.numeric(smoke) - 1) + rnorm(100, 0, 200)
df <- data.frame(weight, gest_age, bmi, smoke)

fit <- lm(weight ~ gest_age + bmi + smoke, data = df)
summary(fit)
```

### Polynomial Regression
* Used when the relationship between the independent and dependent variable is non-linear
* Assumes independence of observations and normally distributed residuals
* Example: Modeling the fall of viral load (copies/mL, log10 transformed) in blood serum over 14-day period following viral exposure

```{r}
days <- runif(80, 1, 14)
viral_load <- -0.15 * (days - 7.5)^2 + 6.5 + rnorm(80, 0, 0.5)
df <- data.frame(days, viral_load)

fit <- lm(viral_load ~ ploy(days, 2), data = df)
summary(fit)
```

### Logistic Regression
* Models the probability of a binary categorical outcome based on one or more predictor variables
* Assumes the outcome is binary, independent observations, little to no multicollinearity and a linear relationship between the log-odds of the outcome and the predictors
* Example: Predicting patient survival based on clinical acute physiology score (APACHE II)

```{r}
score <- rnorm(150, 20, 8)
score <- pmax(pmin(score, 40), 0)
log_odds_surv <- 4.5 - 0.22 * score
prob_surv <- 1 / (1 + exp(-log_odds_surv))
surv <- rbinom(150, 1, prob_surv)
df <- data.frame(score, surv)

fit <- glm(surv ~ score, family = binomial)
summary(fit)
```

### Poisson Regression
* Used for modeling count data, representing the number of events occurring in a fixed period/space
* Assumes the dependent variable consists of non-negative integers and equidisperion (equal dependent variable mean and variable)
* Example: Modeling annual asthma attacks based on local Air Quality Index exposure levels

```{r}
aqi <- rnorm(80, 110, 30)
lambda_asthma <- exp(-1.2 + 0.015 * aqi)
asthma <- rpois(80, lambda_asthma)
df <- data.frame(aqi, asthma)

fit <- glm(asthma ~ aqi, data = df, family = poisson)
summary(poisson_model)
```

### Negative Binomial Regression
* Alternative to Poisson when the variance is significantly greater than the mean
* Assumes independent observations and models the outcome as a negative binomial distribution
* Example: Modeling emergency room visits for chornic pain based on years smoking

```{r}
library(MASS)

smoke <- runif(120, 10, 80)
base_rate <- exp(-0.5 + 0.03 * smoke)
visits <- rnbinom(120, 1.2, base_rate)
df <- data.frame(smoke, visits)

fit <- glm.nb(visits ~ smoke, data = df)
summary(fit)
```

### Quantile Regression
* Used instead of linear regression when there is heteroscedasticity or outliers
* Estimates the conditional median or any other specified quantile instead of the mean
* Example: Modeling the 10th percentile of premature infant birth weights (grams) based on maternal age

```{r}
library (quantreg)

age <- runif(150, 16, 42)
var <- (age - 28)^2 * 10
weight <- 3400 + 15 * age - rexp(150, 1/350) - var
df <- data.frame(age, weight)

quant_10 <- rq(weight ~ age, data = df, tau = 0.1)
quant_50 <- rq(weight ~ age, data = df, tau = 0.5)
```

### Linear Mixed-Effects Model (LMM)
* Analyzes grouped or nested observations, modeling both fixed effects (overall trends) and random effects (group-specific variation)
* Assumes linear relationships and normally distributed residuals and random effects
* Example: Tracking longitudinal rate of cognitive decline (MMSE score) over 5 years at different sites

```{r}
library (lme4)

n_sites <- 5
n_per_site <- 15
site_effect <- rnorm(n_site, 0, 3)

df <- data.frame(
  site_id = factor(rep(1:n_sites, each = n_per_site * 3)),
  patient_id = factor(rep(1:(n_sites * n_per_site), each = 3)),
  year = rep(0:2, times = n_sites * n_per_site)
)

baseline_mmse <- rnorm(n_sites * n_per_site, 24, 2.5)
df$mmse <- baseline_mmse[as.numeric(df$patient_id)] -
           2.1 * df$year +
           site_effect[as.numeric(df$site_id)] +
           rnorm(nrow(df), 0, 1.2)

fit <- lmer(mmse ~ year + (1 | site_id), data = df)
summary(fit)
```

### Lasso Regression
* Regression technique that penalizes absolute size of coefficients, shrinking some to zero and functioning as an automated feature selection method
* Assumes linear relationships, requires standardized variables, and useful for high-dimensional data sets
* Example: Predicting a continuous diagnostic marker from a subset of genes

```{r}
library(glmnet)

genes <- matrix(rnorm(100 * 50), 100, 50)
colnames(genes) <- paste0('gene_', 1:50)
biomarker <- 12.5 + 4.2 * genes[, 1] - 3.5 * genes[, 5] +
             2.8 * genes[, 12] - 1.9 * genes[, 30] +
             rnorm(100, 0, 1)

fit <- cv.glmnet(genes, biomarker, alpha = 1)
selected <- coef(fit, s = 'lambda.min')
summary(fit)
print(selected)
```





# Survival Analysis





### Kaplan-Meier Estimator
* Non-parametric statistic used to estimate the survival function from lifetime data
* Assumes censoring is non-informative and the survival probability is the same for subjects recruited early and late in the study
* Example: Estimating the cumulative probability of remaining cancer-free ove a 10-year/120-month period following surgery

```{r}
library(survival)

months_to_recurrence <- rexp(80, 0.015)
months_to_recurrence <- pmin(months_to_recurrence, 120)
status <- rbinom(80, 1, 0.6)
status[months_to_recurrence ==120] <- 0
df <- data.frame(months_to_recurrence, status)

surv <- Surv(df$months_to_recurrence, df$status)
fit <- survfit(surv ~ 1, data = df)
plot(fit, mark.time = TRUE)
```

### Log-Rank Test
* Non-parametric hypothesis test to compare the survival distributions of two or more independent groups
* Assumes proportional hazards and non-informative censoring
* Example: Comparing the survival curves of melanoma patients receiving chemotherapy alone vs a combo treatment

```{r}
library(survival)

time_chemo <- rexp(50, 0.08)
time_combo <- rexp(50, 0.03)
time <- c(time_chemo, time_combo)
status <- rbinom(100, 1, prob = 0.85)
treatment_type <- factor(rep(c('chemo', 'combo'), each = 50))
df <- data.frame(time, status, treatment_type)

fit <- survdiff(Surv(time, status) ~ treatment_type, data = df)
```

### Cox Proportional Hazards
* Semi-parametric model used to evaluate simultaneously the effect of several variables (covariates) on survival time, estimating the hazard ratio (HR)
* Assumes proportional hazards and a linear relationship between continuous predictors and the log hazard
* Example: Calculating the hazard ratio of cardiac events associated with age, baseline systolic blood pressure, and smoking status over a 15-year longitudinal study

```{r}
library(survival)

age <- rnorm(200, 60, 10)
sbp <- rnorm(200, 135, 15)
smoke <- rbinom(200, 1, 0.3)
hazard <- exp(0.04 * (age - 60) + 0.02 * (sbp - 135) + 0.8 * smoke
time_to_event <- rexp(200, 0.005 * hazard)
event <- ifelse(time_to_event <= 180, 1, 0)
time_to_event <- pmin(time_to_event, 180)
df <- data.frame(time_to_event, event, age, sbp, smoke)

fit <- coxph(Surv(time_to_event, event) ~ age + sbp + smoke, data = df)
summary(fit)
cox.zph(fit) # Proportional Hazards assumption (Schoenfeld Residuals)
```

### Parametric Survival Regression
* Assumes the baseline survival times follow a specific distribution (e.g. Weibull, Exponential, Log-normal)
* The Weibull distribution is widely used because it can model hazard rates that are increasing, decreasing, or constant
* Example: Modeling the time until artificial heart valve implant failure

```{r}
library(survival)

hr <- runif(100, 60, 100)
implant_lifetime <- rweibull(100, 2, 1500 / hr)
failure <- rbinom(100, 1, 0.9)
df <- data.frame(implant_lifetime, failure, hr)

fit <- survreg(Surv(implant_lifetime, failure) ~ hr, data = df, dist = 'weibull')
summary(fit)
```





# Supervised Machine Learning





### Decision Trees
* Non-parametric model that splits data into branch-like structures based on feature values
* Highly interpretable, handles both continuous and categorical variables natively, and makes no distribution assumptions, but prone to overfitting without pruning

```{r}
library(rpart)
library(rpart.plot)

x1 <- runif(150, 1, 10)
x2 <- runif(150, 1, 5)
prob <- 1 / (1 + exp(-(-6 + 1.2 * x2 + 0.4 * x1)))
y <- factor(rbinom(150, 1, prob), levels = c(0, 1), labels = c('No', 'Yes'))

fit <- rpart(y ~ x1 + x2, method = 'class')
summary(fit)
rpart.plot(fit)
```

### Random Forest
* Ensemble of decision trees trained on boostrapped subsets of the data (bagging) with random feature selection
* Robust to overfitting, noise, and outliers
* Handles high-dimensional data and non-linear relationships, with no distribution on assumptions, but features should not have extreme class imbalances without adjustment

```{r}
library(randomForest)

x1 <- rnorm(200, 50000, 15000)
x2 <- rnorm(200, 10000, 5000)
x3 <- rnorm(200, 20000, 8000)
score <- x1 * 0.5 + x2 * 0.8 + x3 * 0.5
y <- factor(ifelse(score > 15000), 'Low', 'High')

fit <- randomForest(y ~ x1 + x2 + x3, ntree = 100, importance = TRUE)
summary(fit)
importance(fit)
```

### Support Vector Machines (SVM)
* Finds the optimal hyperplane that maximizes the distance between different classes in a high-dimensional space
* Utilizes the "kernal trick"
* Sensitive to feature scaling, so continuous predictors should be standardized before fitting

```{r}
library(e1071)

x1 <- runif(200, -2, 2)
x2 <- runif(200, -2, 2)
y <- factor(ifelse(x1^2 + x2^2 < 1, 'A', 'B'))
df <- data.frame(x1, x2, y)

fit <- svm(y ~ x1 + x2, data = df, kernel = 'radial', scale = TRUE)
summary(fit)
plot(fit, df)
```

### XGBoost (Gradient Boosting)
* Ensemble technique that builds weak decision trees sequentially
* Flexible but prone to overfitting if parameters are not tuned properly
* Requires the training data to be structured as a numeric matrix and the categorical target variables must be encoded numerically

```{r}
library(xgboost)

x1 <- runif(150, 5, 50)
x2 <- rnorm(150, 75, 10)
y <- 10 + 0.8 * x1 + 0.6 * x2 + rnorm(150, 0, 5)

pred_mat <- as.matrix(data.frame(x1, x2))

fit <- xgboost(data = pred_mat, label = y, nrounds = 50, objective = 'reg:squarederror', verbose = 0)
summary(fit)
```

### K-Nearest Neighbors (KNN)
* Instance based learner that classifies a data point based on the majority vote of its closest neighbors in the feature space
* Non-parametric and simple
* Assumes similar data points reside near one another and features must be scaled/normalized 

```{r}
library(class)

x1 <- runif(120, 10, 90)
x2 <- runif(120, 10, 90)
y <- factor(ifelse(x1 > x2, 'A', 'B'))
df <- scale(data.frame(x1, x2))

train_ind <- 1:96 # sample 70%
test_ind <- 97:120

fit <- knn(train = df[train_ind, ],
           test = df[test_ind, ],
           cl = y[train_ind],
           k = 5)
table(Actual = y[test_ind], Predicted = fit)
```





# Unsupervised Machine Learning





### K-Means Clustering
* Partitions data into pre-defined, non-overlapping clusters by minimizing the sum of squared distances between data points and their cluster
* Assumes clusters are spherical, of similar size, and the features are continuous and standardized

```{r}
c1 <- data.frame(X = rnorm(50, 2, 0.5), Y = rnorm(50, 2, 0.5))
c2 <- data.frame(X = rnorm(50, 8, 0.6), Y = rnorm(50, 8, 0.6))
c3 <- data.frame(X = rnorm(50, 5, 0.5), Y = rnorm(50, 12, 0.5))
df <- rbind(c1, c2, c3)
scaled_df(df)

fit <- kmeans(scaled_df, centers = 3, nstart = 25)
plot(df, col = fit$cluster, pch = 19)
points(fit$centers * attr(scaled_df, 'scaled:scale') + attr(scaled_df, 'scaled:center'), col = 1:3, pch = 4, cex = 2, lwd = 3)
```

### Hierarchical Clustering
* Builds a tree-like hierarchy of clusters (dendrogram) by sequentially merging smaller clusters based on proximity and doesn't require a pre-specified number of clusters
* Requires a distance metric and linkage criteria are chosen and standardized features

```{r}
df <- data.frame(
  x1 = rnorm(15, mean = c(10, 20, 30), sd = 2),
  x2 = rnorm(15, mean = c(50, 40, 60), sd = 3)
)
row.names(df) <- paste('ID', 1:15, sep = '_')
scaled_df <- scale(df)
dist_mat <- dist(scaled_df, method = 'euclidean')

fit <- hclust(dist_mat, method = 'ward.D2')
plot(fit)
rect.hclust(fit, k = 3)
```

### Principal Component Analysis (PCA)
* Dimensionality reduction technique that transforms a set of correlated variables into a smaller set of linearly uncorrelated variables
* Assumes linear relationships among features and must standardize features

```{r}
a <- rnorm(100, 25, 5)
b <- 100 - 2 * x1 + rnorm(100, 0, 2)
c <- 1000 - 0.5 * x1 + rnorm(100, 0, 1)
d <- runif(100, 5, 25)
df <- data.frame(a, b, c, d)

fit <- prcomp(df, center = TRUE, scale. = TRUE)
summary(fit)

biplot(fit)
```





# Time-Series





### Auto-Regressive Integrated Moving Average (ARIMA)
* Forecasts univariate time-series data by modeling auto-regressive (AR) components, differencing (I) to achieve stationarity, and moving average (MA) errors
* Data must be stationary (constant mean, variance, and autocorrelation) or made stationary through differencing

```{r}

```

### 
* 
* 

```{r}

```

### 
* 
* 

```{r}

```

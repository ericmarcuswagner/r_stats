---
title: "stats_tests"
output: html_document
date: "2026-06-03"
---

# ------------------------------------------------------------------------------

# Mean & Group Comparison

# ------------------------------------------------------------------------------

### One-Sample t-Test
* Compares the mean of a single group to a known or hypothesized value
* Assumes continuous data, independent observations, and approximately normal distribution of the data (or n>30)

```{r}
x <- rnorm(30, 72, 10)

t.test(x, mu = 70)
```

### Two-Sample t-Test
* Compares the means of two independent groups
* Assumes continuous dependent variable, independent groups, normal distribution within each group, and homogeneity of variances (though Welch't t-test adjusts for unequal variances)

```{r}
a <- rnorm(30, 75, 8)
b <- rnorm(30, 71, 9)

t.test(a, b, var.equal = TRUE) 
t.test(a, b) # Welch, default
```

### Paired t-Test
* Compares the means of the same group at two different times or matched pairs
* Assumes continuous data, dependent/paired observations, and normal distribution of the differents between the paired values

```{r}
a <- rnorm(25, 80, 5)
b <- a + rnorm(25, 15, 4)

t.test(a, b, paired = TRUE)
```

### One-Way ANOVA (Analysis of Variance)
* Compares the means of three or more independent groups
* Assumes continuous dependent variable, independent observations, normal distribution within each group, and homogeneity of variances across groups

```{r}
df <- data.frame(
  y <- c(rnorm(20, 85, 5), rnorm(20, 88, 50), rnorm(20, 78, 5)),
  x <- factor(rep(c('A', 'B', 'C'), each = 20))
)

fit <- aov(y ~ x, data = df)
summary(fit)
TukeyHSD(fit) # CI
```

### Two-Way ANOVA (with Interaction)
* Evaluates the effects of two categorical independent variables (factors) on a continuous dependent variable, as well as the interaction between them
* Assumes independent observations, normal distribution of residuals, and homogeneity of variances

```{r}
df <- data.frame(
  y = c(rnorm(15, 50, 4), rnorm(15, 55, 4), rnorm(15, 45, 4), rnorm(15, 60, 4)),
  x = factor(rep(c('A', 'B'), each = 30)),
  z = factor(rep(rep(c('Low', 'High'), each = 15), 2))
)

fit = aov(y ~ x * z, data = df)
summary(fit)
```

### Analysis of Covariance (ANCOVA)
* Compares the means of a continuous dependent variable across levels of a categorical independent variable while controlling for the effect of a continuous covariate
* Assumes homogeneity of regression slopes and normality of residuals and homoscedasticity

```{r}

```

### Wilcoxon Signed-Rank Test 
* Non-parametric alternative to the paired t-test or one sample t-test
* Compares medians of paired/dependent groups
* Assumes the distribution of differences between pairs is symmetric

```{r}
a <- sample(1:5, 30, replace = TRUE, prob = c(0.3, 0.4, 0.2, 0.05, 0.05))
b <- sample(1:5, 30, replace = TRUE, prob = c(0.05, 0.05, 0.2, 0.4, 0.3))

wilcox.test(a, b, paired = TRUE, exact = FALSE)
```

### Wilcoxon Rank-Sum / Mann-Whitney U Test
* Non-parametric alternative to the independent two-sample t-test
* Compares the distributions (often interpreted as medians) of two independent groups
* Assumes independent groups, ordinal or continuous data

```{r}
con <- rpois(20, 8)
trt <- rpois(20, 5)

wilcox.test(con, trt, paired = FALSE, exact = FALSE)
```

### Kruskal-Wallis Test
* Non-parametric to the One-way ANOVA
* Compares the medians/distributions across three or more independent groups
* Assumes ordinal or continuous dependent variable, independent groups

```{r}
df <- data.frame(
  y = c(sample(1:5, 25, replace = TRUE, prob = c(0.1, 0.1, 0.2, 0.4, 0.2)),
             sample(1:5, 25, replace = TRUE, prob = c(0.4, 0.3, 0.2, 0.05, 0.05)),
             sample(1:5, 25, replace = TRUE, prob = c(0.05, 0.05, 0.1, 0.4, 0.4)),
             sample(1:5, 25, replace = TRUE, prob = c(0.2, 0.2, 0.2, 0.2, 0.2))),
  x = factor(rep(c("A", "B", "C", "D"), each = 25))
)

kruskal.test(y ~ x, data = df)
```

### Friedman Test
* Non-parametric alternative to the repeated measures ANOVA
* Compares three or more paired/matched groups
* Assumes matched groups, ordinal or continuous dependent variable, and consistent blocks across conditions

```{r}
n <- 15
df <- data.frame(
  y <- c(rnorm(n, 70, 5), rnorm(75, 5), rnorm(65, 5)),
  x <- factor(rep(c('A', 'B', 'C'), each = n)),
  z <- factor(rep(1:n, times = 3))
)

friedman.test(y ~ x | z, data = df)
```

# ------------------------------------------------------------------------------

# Categorical Data

# ------------------------------------------------------------------------------

### Chi-Square Test of Independence
* Determines association between two categorical variables
* Assumes mutually exclusive categories, independent observations, and that the expected frequency in cell each cell is >5

```{r}

```

### Chi-Square Goodness-of-Fit
* Tests whether an observed categorical variable distribution matches a hypothesized distribution
* Assumes categorical data, mutually exclusive categories, independent observations, and counts in each group >5
```{r}

```

### Fisher's Exact Test
* Determines association between two categorical variables when sample sizes are small
* Calculates exact probability instead of approximation

```{r}

```

### McNemar's Test
* Non-parametric test for binary paired data, assessing whether there is a change in proportion before and after intervention
* Assumes continuous variables, linear relationship, independent observations, homoscedasticity, and that both variables are normally distributed

```{r}

```

### Two-Sample Test for Equality of Proportions
* Compares the proportion of success between two independent groups
* Assumes independent random samples and counts in each group >5

```{r}

```

# ------------------------------------------------------------------------------

# Correlation

# ------------------------------------------------------------------------------

# Correlation

### Pearson's Correlation
* Measures linear relationship between two continuous variables
* Assumes continuous variables, linear relationship, independent observations, homoscedasticity, and that both variables are normally distributed

```{r}

```

### Spearman's Rank Correlation
* Assesses the relationship between two variables
* Ideal for ordinal or continuous data with non-linaer monotonic curves

```{r}

```

### Kendall's Tau
* Non-parametric correlation test used to measure the ordinal association between two variables
* Preferred when the sample size is small and there are many tied ranks

```{r}

```

# ------------------------------------------------------------------------------

# Diagnostic Tests

# ------------------------------------------------------------------------------

### Shapiro-Wilk Normality Test
* Tests whether the sample comes from a normally distributed population
* Sensitive to large sample sizes

```{r}
norm <- rnorm(50, 100, 15)
skew <- rexp(50, 0.1)

shapiro.test(norm)
shapiro.test(skew)
```

### Kolmogorov-Smirnov (K-S) Test
* Non-parametric test used to compare a continuous sample distribution against a reference probability distribution or two continuous sample distributions to each other
* Assumes continuous data and independent observations

```{r}
x <- rnorm(100, 0, 1)
ks.test(x, 'pnorm', mean(x), sd(x))
```

### Levene's Test
* Tests whether the variances across two or more groups are equal
* Robust to departures from normality

```{r}
library(car)

a <- rnorm(50, 10, 2)
b <- rnorm(50, 10, 8)

df <- data.frame(
  Value = c(a, b),
  Group = factor(rep(c('A', 'B'), each = 50))
)

leveneTest(Value ~ Group, data = df)
```

### F Test for Equality of Two Variances
* Compares the variances of two independent groups
* Assumes both groups are normally distributed

```{r}

```

### Breusch-Pagan Test
* Tests whether the residuals of a linear regression model are homoscedastic

```{r}
library(lmtest)

x <- seq(1, 100, length.out = 100)
y <- 5 + 2 * x + rnorm(100, 0, x * 0.5)

fit <- lm(y ~ x)
bptest(fit)
```

### Variance Inflation Factor (VIF)
* Measures severity of multicollinearity
* 1 = none, >5 = moderate, >10 = severe

```{r}
library(car)

x1 <- rnorm(100, 50, 10)
x2 <- x1 + rnorm(100, 0, 2)
x3 <- rnorm(100, 10, 5)
y <- 15 + 0.5 * x1 - 0.2 * x2 + 1.2 * x3 + rnorm(100, 0, 3)

fit <- lm(y ~ x1 + x2 + x3)
vif(fit)
```

# ------------------------------------------------------------------------------

# Regression

# ------------------------------------------------------------------------------

### Simple Linear Regression
* Models the linear relationship between one continuous dependent variables and one independent variable
* Assumes linearity, independence of observations, homoscedasticity, and normality of residuals

```{r}
x <- runif(50, 100, 1000)
y <- 10 + 0.05 * x + rnorm(50, 0, 5)

fit <- lm(y ~ x)
summary(fit)
```

### Multiple Linear Regression
* Models the relationship between one continuous dependent variable and two or more independent variables
* Assumes linearity, independence, homoscedasticity, normality of residuals, and limited multicolllinearity among independent variables

```{r}
a <- rnorm(100, 3000, 500)
b <- rnorm(100, 3, 1)
y <- 50 - 0.005 * a - 2.5 * b + rnorm(100, 0, 3)

fit <- lm(y ~ a + b)
summary(fit)
```

### Polynomial Regression
* Used when the relationship between the independent and dependent variable is non-linear
* Assumes independence of observations and normally distributed residuals

```{r}
x <- seq(10, 100, length.out = 50)
y <- -0.05 * (x - 55)^2 + 80 + rnorm(50, 0, 4)

fit <- lm(y ~ poly(x, 2))
summary(fit)
```

### Logistic Regression
* Models the probability of a binary categorical outcome based on one or more predictor variables
* Assumes the outcome is binary, independent observations, little to no multicollinearity and a linear relationship between the log-odds of the outcome and the predictors

```{r}
x <- rnorm(100, 50, 15)
log_odds <- -5 + 0.1 * x
prob <- 1 / (1 + exp(-log_odds))
y <- rbinom(100, 1, prob)

fit <- glm(y ~ x, family = binomial)
summary(fit)
```

### Poisson Regression
* Used for modeling count data, representing the number of events occurring in a fixed period/space
* Assumes the dependent variable consists of non-negative integers and equidisperion (equal dependent variable mean and variable)

```{r}
x <- rnorm(60, 1000, 200)
x <- ifelse(x < 100, 100, x)
lambda <- exp(-2 + 0.003 * x)
y <- rpois(60, lambda)

fit <- glm(y ~ x, family = poisson)
summary(poisson_model)
```

### Negative Binomial Regression
* Alternative to Poisson when the variance is significantly greater than the mean
* Assumes independent observations and models the outcome as a negative binomial distribution

```{r}
library(MASS)

x <- runif(100, 1, 10)
lambda <- exp(1 + 0.2 * x)
y <- rnbinom(100, 1.5, lambda)

fit <- glm.nb(y ~ x)
summary(fit)
```

### Quantile Regression
* Used instead of linear regression when there is heteroscedasticity or outliers
* Estimates the conditional median or any other specified quantile instead of the mean

```{r}
library (quantreg)
```

### Linear Mixed-Effects Model (LMM)
* Analyzes grouped or nested observations, modeling both fixed effects (overall trends) and random effects (group-specific variation)
* Assumes linear relationships and normally distributed residuals and random effects

```{r}
library (lme4)


```

### Lasso Regression
* Regression technique that penalizes absolute size of coefficients, shrinking some to zero and functioning as an automated feature selection method
* Assumes linear relationships, requires standardized variables, and useful for high-dimensional data sets

```{r}
library(glmnet)


```

# ------------------------------------------------------------------------------

# Survival Analysis

# ------------------------------------------------------------------------------

### Kaplan-Meier Estimator
* Non-parametric statistic used to estimate the survival function from lifetime data
* Assumes censoring is non-informative and the survival probability is the same for subjects recruited early and late in the study

```{r}
library(survival)

time <- rexp(100, 0.02)
status <- rbinom(100, 1, 0.8)

surv <- Surv(time, status)
fit <- survfit(surv ~ 1)

summary(fit)
plot(fit)
```

### Log-Rank Test
* Non-parametric hypothesis test to compare the survival distributions of two or more independent groups
* Assumes proportional hazards and non-informative censoring

```{r}
library(survival)

a <- rexp(50, 0.05)
b <- rexp(50, 0.02)
time <- c(a, b)
status <- rbinom(100, 1, 0.85)
x <- factor(rep(c('A', 'B'), each = 50))

fit <- survdiff(Surv(time, status) ~ x)
summary(fit)
```

### Cox Proportional Hazards
* Semi-parametric model used to evaluate simultaneously the effect of several variables (covariates) on survival time, estimating the hazard ratio (HR)
* Assumes proportional hazards and a linear relationship between continuous predictors and the log hazard

```{r}
library(survival)

a <- rbinom(150, 1, 0.5)
b <- rnorm(150, 70, 10)
rate <- exp(0.05 * (b - 70) - 0.8 * a)
time <- rexp(150, 0.1 * rate)
status <- rbinom(150, 1, 0.9)

fit <- coxph(Surv(time, status) ~ a + b)
summary(fit)

cox.zph(fit) # Tests Proportional Hazards assumption
```

### Parametric Survival Regression
* Assumes the baseline survival times follow a specific distribution (e.g. Weibull, Exponential, Log-normal)
* The Weibull distribution is widely used because it can model hazard rates that are increasing, decreasing, or constant

```{r}
library(survival)

x <- runif(100, 10, 100)
time <- rweibull(100, 1.5, 1000/x)
status <- rbinom(100, 1, 0.8)

fit <- survreg(Surv(time, status) ~ x, dist = 'weibull')
```

# ------------------------------------------------------------------------------

# Supervised Machine Learning

# ------------------------------------------------------------------------------

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

# ------------------------------------------------------------------------------

# Unsupervised Machine Learning

# ------------------------------------------------------------------------------

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

# ------------------------------------------------------------------------------

# Time-Series

# ------------------------------------------------------------------------------

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

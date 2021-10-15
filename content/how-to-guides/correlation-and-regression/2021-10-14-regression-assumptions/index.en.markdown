---
title: Regression Pt. 4 - Model Assumptions
author: Carlos Rodriguez
date: '2021-10-14'
slug: regression-assumptions
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-10-14T07:28:22-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
draft: false
type: book
weight: 40
---

We continue the series on regression by exploring the assumptions of our model. We focus on the assumptions of independence of residuals, multicolinearity, and normality of residuals


### Assumption of independent errors: Durbin-Watson test
We can test the assumption of independent errors with the Durbin-Watson test. The car (companion to applied regression) package contains the `dwt()` function to perform this test. The Durbin-Watson function tests for serial correlation in the residuals and produces a value that lies between 0 and 4. A value of 2 indicates that the residuals are uncorrelated. Values greater than 2 indicate a negative correlation while values less than 2 indicate a positive correlation among adjacent residuals. As a conservative rule, values less than 1 or greater than 3 may indicate the assumption of independent errors may be violated. Our D-W statistic, 2.06, falls within this range.

```r
library(car)
```

```
## Loading required package: carData
```

```
## 
## Attaching package: 'car'
```

```
## The following object is masked from 'package:dplyr':
## 
##     recode
```

```
## The following object is masked from 'package:purrr':
## 
##     some
```

```r
# Durbin Watson
dwt(multiple)
```

```
##  lag Autocorrelation D-W Statistic p-value
##    1     -0.03061432      2.057416   0.676
##  Alternative hypothesis: rho != 0
```

### Assumption of Multicolinearity: Variance inflation factor (VIF)
We want to avoid multicolinearity which occurs when predictor variables are correlated amongst themselves. The VIF is one way to measure multicolinearity. A VIF value of 10 or higher is problematic. We have a VIF value of 12.65 for AGE and 12.16 for YEARS. These results makes sense that age is correlated with experience in number of years, but makes it difficult to obtain unique regression coefficients. The tolerance statistic is 1 divided by the VIF and anything less than 0.1 or 0.2 depending on how conservative we want to be can be causes for concern. For the tolerance statistics, we again obtain concerning values for AGE and YEARS.
* Largest VIF greater than 10
* Average VIF substantially greater than 1
* Tolerance less than 0.1 serious problem
* Tolerance less than 0.2 potential problem


```r
#VIF for multicollinearity
vif(multiple)
```

```
##       AGE    BEAUTY     YEARS 
## 12.652841  1.153364 12.156757
```

```r
# Tolerance Statistic
1/vif(multiple)
```

```
##        AGE     BEAUTY      YEARS 
## 0.07903364 0.86702902 0.08225878
```

```r
# Mean VIF
mean(vif(multiple))
```

```
## [1] 8.65432
```

### Assumptions about residuals: Histogram
A histogram and kernel density estimation of the residuals indicate that the data are positively skewed, which indicates that the assumption regarding the normality of residuals has been violated. 

```r
data$studentized.residuals <- rstudent(multiple)

# Histogram of the residuals
ggplot(data, aes(x = studentized.residuals)) +
  geom_histogram(aes(y = ..density..), color = "#1565c0", fill = "#1565c0", alpha = .7) + 
  stat_function(fun = dnorm, args = list(mean = mean(data$studentized.residuals, na.rm = TRUE), 
                                         sd = sd(data$studentized.residuals, na.rm = TRUE))) +
  labs(x = "Studentized Residuals", y = "Density")
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-4-1.png" width="672" />

### Assumptions about residuals: Q-Q Plot
The Q-Q plot displays the values' deviation from normality. A straight diagonal line would represent a normal distribution. However, in our case, we observe a fair amount of deviation from a straight diagnoal line further indicating that the assumption regarding the normality of residuals has been violated

```r
# QQ Plot
qplot(sample = data$studentized.residuals) +
  labs(x = "Theoretical Values", y = "Observed Values")
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-5-1.png" width="672" />

### Assumptions about residuals: Fitted Values vs Residuals
This las figure plots the fitted values agains the residuals. We notice that the data funnel out with Studentized Residuals display a greater range as Fitted Values increases. This can be taken as evidence of heteroscedasticity in the data. 

```r
# Scatter plot of the studentized residuals vs predicted values
ggplot(data, aes(x = fitted, y = studentized.residuals)) +
  geom_point(alpha = 0.7, color = "#1565c0") +
  geom_smooth(method = "lm", color = "Black") +
  labs(x = "Fitted Values", y = "Studentized Residuals")
```

```
## `geom_smooth()` using formula 'y ~ x'
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-6-1.png" width="672" />

### Interpretation
Given that our model demonstrates violates numerous assumptions such as normality of errors, multicolinearity, and heteroscedasticity, we have reason to believe that this model is unreliable.

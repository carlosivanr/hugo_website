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
draft: true
type: book
weight: 40
---

We continue the series on regression by exploring the assumptions of our model. We focus on the assumption of independence, that is that one outcome does not influence another and we focus on multicoliearity which means that we don't want our predictor variables to be correlated.

I will suppose that the mulitple regression model is still present in the workspace



### Assumption of independence: Durbin-Watson test
We can test the assumption of independence with the Durbin-Watson test. The car package contains the `dwt()` function to perform this test and all we need to do is pass in the multiple lm object as the input to this function. The Durbin-Watson function tests the serial correlation in the residuals and produces a value that lies between 0 and 4. A value of 2 indicates that the residuals are uncorrelated. Values greater than 2 indicate a negative correlation while values less than 2 indicate a positive correlation among adjacent residuals. 

```r
library(car)
```

```
## Loading required package: carData
```

```r
# Durbin Watson
dwt(multiple)
```

```
##  lag Autocorrelation D-W Statistic p-value
##    1     -0.03061432      2.057416   0.686
##  Alternative hypothesis: rho != 0
```


### Assumption of Multicolinearity: Variance inflation factor (VIF)
We want to avoid multicolinearity which is when there are high correlations among the predictors.
VIF ndicates if a predictor has a strong linear relationship with other predictors.
VIF value of 10 is problematic
We have a VIF value of 12.65 for AGE and 12.16 for YEARS. It makes sense that age is correlated with experience in the number of years.
Makes it hard to determine which predictor is important

Tolerance statistic, anything less than 0.1 is a serious problem, anything less than 0.2 is cause for concern.


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
### Assumptions about residuals

<!-- ### Normal distribution of residuals -->

<!-- ```{r} -->
<!-- #plot(multiple) -->

<!-- # Histogram of the residuals -->
<!-- ggplot(data, aes(x = studentized.residuals)) + -->
<!--   geom_histogram(aes(y = ..density..), color = "#1565c0", fill = "white") + -->
<!--   stat_function(fun = dnorm, args = list(mean = mean(data$standardized.residuals, na.rm = TRUE), sd = sd(data$standardized.residuals, na.rm = TRUE), color = "red", size = 1)) -->

<!-- # QQ Plot -->
<!-- qplot(sample = data$studentized.residuals, stat = "qq") + -->
<!--   labs(x = "Theoretical Values", y = "Observed Values") -->

<!-- # Scatter plot of the studentized residuals vs predicted values -->

<!-- ggplot(data, aes(x = fitted, y = studentized.residuals)) + -->
<!--   geom_point(alpha = 0.7, color = "#1565c0") + -->
<!--   geom_smooth(method = "lm", color = "#1565c0") -->


<!-- ``` -->

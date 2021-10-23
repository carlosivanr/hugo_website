---
title: Linear Regression Pt. 3 - Casewise Diagnostics
author: Carlos Rodriguez
date: '2021-10-11'
slug: regression-diagnostics
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-10-11T21:11:38-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
draft: false
type: book
weight: 30
---



After building our simple and multiple regression model, we turn our attention to casewise diagnostics to learn about which outliers are present in the sample and which data points have undue influence on our model which could affect the models stability. We will focus on the standardized residuals, Cook's distance, and leverage/hat values, but there are several other measures to assess model diagnostics. 

### Outliers and standardized residuals
Residuals help us understand how well the model fits the sample data. Standardized residuals are derived by dividing the non-standardized residuals by an estimate of their standard deviation. Standardized residuals can be obtained by applying the `rstandard()` function on our multiple regression model. Standardized residuals primarily serve two roles. First, they facilitate interpretation across different models because the units are in standard deviations rather than the unit of the outcome variable. Second, they serve as an indicator of outliers that may bias the estimated regression coefficients. A couple of general rules are that no more than 5% of the absolute values of the standardized residuals are greater than 2 and no more than 1% of the absolute values of the standardized residuals are greater than 2.5. In our example dataset, about 5.2% of the standardized residuals values are beyond the +/-2 boundary which is evidence that our model may not represent our outcome data well.

```r
# Create a column of standardized residuals
data$standardized.residuals <- rstandard(multiple)

# Create a boolean vector of large residuals; greater than 2 or less than -2 
data$large.residual <- data$standardized.residuals > 2 | data$standardized.residuals < -2

# Sum of large standardized residuals
sum(data$large.residual)
```

```
## [1] 12
```

```r
# Percentage of standardized residuals greater than 2 or less than -2
sum(data$large.residual)/length(data$large.residual) * 100
```

```
## [1] 5.194805
```

```r
# Print table of cases where large.residual equals TRUE
kable(
  filter(data, large.residual == TRUE)
)
```

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> SALARY </th>
   <th style="text-align:right;"> AGE </th>
   <th style="text-align:right;"> YEARS </th>
   <th style="text-align:right;"> BEAUTY </th>
   <th style="text-align:right;"> standardized.residuals </th>
   <th style="text-align:left;"> large.residual </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 53.72479 </td>
   <td style="text-align:right;"> 20.34707 </td>
   <td style="text-align:right;"> 5.506886 </td>
   <td style="text-align:right;"> 68.56999 </td>
   <td style="text-align:right;"> 2.214829 </td>
   <td style="text-align:left;"> TRUE </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 95.33807 </td>
   <td style="text-align:right;"> 24.17183 </td>
   <td style="text-align:right;"> 8.532050 </td>
   <td style="text-align:right;"> 71.77039 </td>
   <td style="text-align:right;"> 4.696607 </td>
   <td style="text-align:left;"> TRUE </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 48.86766 </td>
   <td style="text-align:right;"> 19.11451 </td>
   <td style="text-align:right;"> 4.951027 </td>
   <td style="text-align:right;"> 73.32626 </td>
   <td style="text-align:right;"> 2.241876 </td>
   <td style="text-align:left;"> TRUE </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 51.02516 </td>
   <td style="text-align:right;"> 19.46200 </td>
   <td style="text-align:right;"> 5.187275 </td>
   <td style="text-align:right;"> 80.00141 </td>
   <td style="text-align:right;"> 2.420635 </td>
   <td style="text-align:left;"> TRUE </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 56.83152 </td>
   <td style="text-align:right;"> 24.41146 </td>
   <td style="text-align:right;"> 8.753041 </td>
   <td style="text-align:right;"> 80.65103 </td>
   <td style="text-align:right;"> 2.099147 </td>
   <td style="text-align:left;"> TRUE </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 64.79129 </td>
   <td style="text-align:right;"> 18.46839 </td>
   <td style="text-align:right;"> 4.284322 </td>
   <td style="text-align:right;"> 78.91763 </td>
   <td style="text-align:right;"> 3.440027 </td>
   <td style="text-align:left;"> TRUE </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 61.31880 </td>
   <td style="text-align:right;"> 22.25275 </td>
   <td style="text-align:right;"> 7.397138 </td>
   <td style="text-align:right;"> 78.92917 </td>
   <td style="text-align:right;"> 2.778123 </td>
   <td style="text-align:left;"> TRUE </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 89.98003 </td>
   <td style="text-align:right;"> 22.28899 </td>
   <td style="text-align:right;"> 7.419825 </td>
   <td style="text-align:right;"> 75.93018 </td>
   <td style="text-align:right;"> 4.717284 </td>
   <td style="text-align:left;"> TRUE </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 74.86075 </td>
   <td style="text-align:right;"> 24.40682 </td>
   <td style="text-align:right;"> 8.444767 </td>
   <td style="text-align:right;"> 86.09212 </td>
   <td style="text-align:right;"> 3.319137 </td>
   <td style="text-align:left;"> TRUE </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 54.56552 </td>
   <td style="text-align:right;"> 22.31422 </td>
   <td style="text-align:right;"> 6.833367 </td>
   <td style="text-align:right;"> 88.01470 </td>
   <td style="text-align:right;"> 2.200115 </td>
   <td style="text-align:left;"> TRUE </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 50.65578 </td>
   <td style="text-align:right;"> 15.27406 </td>
   <td style="text-align:right;"> 2.981697 </td>
   <td style="text-align:right;"> 66.38544 </td>
   <td style="text-align:right;"> 3.177863 </td>
   <td style="text-align:left;"> TRUE </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 71.32073 </td>
   <td style="text-align:right;"> 20.65061 </td>
   <td style="text-align:right;"> 5.834559 </td>
   <td style="text-align:right;"> 77.57684 </td>
   <td style="text-align:right;"> 3.531357 </td>
   <td style="text-align:left;"> TRUE </td>
  </tr>
</tbody>
</table>

### Influential cases: Cook's distance
There are a few ways to determine which cases within a regression model have unde influence in the model parameters. One way is to calculate Cook's distance which has a straightforward interpretation - any value greater than 1 may be cause for concern. Cook's distance values can be obtained with the `cooks.distance()` function by passing the model as its input. With this dataset, there are no values greater than 1. This suggest that the model is stable across the sample because none of the cases exert undue influence on the model parameters.

```r
# Create a column of Cook's distance values
data$cooks.distance <- cooks.distance(multiple)

# Create a boolean vector of large residuals; greater than 2 or less than -2 
data$large.cooks.d <- data$cooks.distance > 1

# Sum of large Cook's distance
sum(data$large.cooks.d)
```

```
## [1] 0
```

### Influential cases: Leverage/hat values
Leverage/hat values are an additional measure of influential cases. Leverage values can obtained by passing the regression model object to the `hatvalues()` function. Cases with values that are 2 or 3 times as large as (k + 1/n), where k = the number of predictors and n = the sample size, may have undue influence. With these data, values higher than 0.035 and 0.052, depending on how conservative you want to be. There are 25 cases with hat values 2 times greater than the average leverage value, and 3 cases with hat values greater than 3 times the average leverage value.

```r
# Create a column of leverage/hat values
data$leverage <- hatvalues(multiple)

# Average Leverage, # of predictors + 1 divided by n
((3 + 1)/231) * 2
```

```
## [1] 0.03463203
```

```r
((3 + 1)/231) * 3
```

```
## [1] 0.05194805
```

```r
# Create a boolean vector of large hat values
data$large.leverage <- data$leverage > ((3 + 1)/231) * 2

# Sum of large leverage 2, conservative
sum(data$large.leverage)
```

```
## [1] 25
```

```r
# Create a boolean vector of large hat values
data$large.leverage <- data$leverage > ((3 + 1)/231) * 3

# Sum of large leverage 3, less conservative
sum(data$large.leverage)
```

```
## [1] 3
```

```r
# Print table
kable(
  filter(data, large.leverage == TRUE) %>%
    select(SALARY, AGE, YEARS, BEAUTY, leverage, large.leverage)
  
)
```

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> SALARY </th>
   <th style="text-align:right;"> AGE </th>
   <th style="text-align:right;"> YEARS </th>
   <th style="text-align:right;"> BEAUTY </th>
   <th style="text-align:right;"> leverage </th>
   <th style="text-align:left;"> large.leverage </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 6.419431 </td>
   <td style="text-align:right;"> 18.99114 </td>
   <td style="text-align:right;"> 5.237983 </td>
   <td style="text-align:right;"> 99.22141 </td>
   <td style="text-align:right;"> 0.0623528 </td>
   <td style="text-align:left;"> TRUE </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 22.681436 </td>
   <td style="text-align:right;"> 25.28966 </td>
   <td style="text-align:right;"> 9.932158 </td>
   <td style="text-align:right;"> 75.42206 </td>
   <td style="text-align:right;"> 0.0580976 </td>
   <td style="text-align:left;"> TRUE </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 3.534942 </td>
   <td style="text-align:right;"> 16.04653 </td>
   <td style="text-align:right;"> 4.598695 </td>
   <td style="text-align:right;"> 83.59070 </td>
   <td style="text-align:right;"> 0.0600435 </td>
   <td style="text-align:left;"> TRUE </td>
  </tr>
</tbody>
</table>

<!-- ### Diagnostics -->
<!-- ```{r, include = FALSE} -->
<!-- data$residuals <- resid(multiple) -->
<!-- data$standardized.residuals <- rstandard(multiple) -->
<!-- data$studentized.residuals <- rstudent(multiple) -->
<!-- data$cooks.distance <- cooks.distance(multiple) -->
<!-- data$dfbeta <- dfbeta(multiple) -->
<!-- data$dffits <- dffits(multiple) -->
<!-- data$leverage <- hatvalues(multiple) -->
<!-- data$covariance.ratios <- covratio(multiple) -->
<!-- data$fitted <- multiple$fitted.values -->
<!-- ``` -->
<!-- ```{r} -->
<!-- ``` -->


<!-- ### Covariance ratios -->
<!-- Several values are below the bottom boundary, below .948 and above 1.052. There are many values here that are well below this lower bound. But the cooks distance for these values is OK.  -->
<!-- CVR > 1 + (3 * (k+1)/n) -->
<!-- CVR < 1 - (3 * (k+1)/n) -->

<!-- `* Calculate the lower and upper bound limits of the covariance ratio. Values outside of the bounds could be problematic.` -->

---
title: Linear Mixed Effect Models (work in progress)
author: Carlos Rodriguez
date: '2021-11-11'
slug: lmers
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-11-11T19:18:09-07:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
draft: false
type: book
weight: 10
---


In this guide we will use data from Chapter 15, Table 1 in the AMCP package. These data track the scores from a test of general cognitive abilities over a period of 18 months for 12 children. For each participant, 4 measurements of the cognitive test are gathered at 30, 36, 42, and 48 months. This particular design has a hierarchical organization as the cognitive scores can be thought of as nested within participant. Thus it may be reasonable to suspect that cognitive scoress will be correlated with eachother within participant. Linear mixed effect models were developed to address this type of situation.




### Load Packages

```r
library(tidyverse)
library(lme4)
library(lmerTest)
library(AMCP)
library(kableExtra)
library(broom.mixed)
library(car)
library(flexplot)
```


### Load Data

```r
data(chapter_15_table_1)
kable(head(chapter_15_table_1))
```

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> Months30 </th>
   <th style="text-align:right;"> Months36 </th>
   <th style="text-align:right;"> Months42 </th>
   <th style="text-align:right;"> Months48 </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 108 </td>
   <td style="text-align:right;"> 96 </td>
   <td style="text-align:right;"> 110 </td>
   <td style="text-align:right;"> 122 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 103 </td>
   <td style="text-align:right;"> 117 </td>
   <td style="text-align:right;"> 127 </td>
   <td style="text-align:right;"> 133 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 96 </td>
   <td style="text-align:right;"> 107 </td>
   <td style="text-align:right;"> 106 </td>
   <td style="text-align:right;"> 107 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 84 </td>
   <td style="text-align:right;"> 85 </td>
   <td style="text-align:right;"> 92 </td>
   <td style="text-align:right;"> 99 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 118 </td>
   <td style="text-align:right;"> 125 </td>
   <td style="text-align:right;"> 125 </td>
   <td style="text-align:right;"> 116 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 110 </td>
   <td style="text-align:right;"> 107 </td>
   <td style="text-align:right;"> 96 </td>
   <td style="text-align:right;"> 91 </td>
  </tr>
</tbody>
</table>

### Convert wide to long
Our first step in working with these data is to convert the data frame from wide to long. A detailed explanation of the steps to achieve this can be found [here](/guides/data-manipulation-and-visualization/wide-to-long).
<table>
 <thead>
  <tr>
   <th style="text-align:left;"> id </th>
   <th style="text-align:left;"> unit </th>
   <th style="text-align:left;"> month </th>
   <th style="text-align:right;"> scores </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> Months </td>
   <td style="text-align:left;"> 30 </td>
   <td style="text-align:right;"> 108 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> Months </td>
   <td style="text-align:left;"> 36 </td>
   <td style="text-align:right;"> 96 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> Months </td>
   <td style="text-align:left;"> 42 </td>
   <td style="text-align:right;"> 110 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> Months </td>
   <td style="text-align:left;"> 48 </td>
   <td style="text-align:right;"> 122 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> Months </td>
   <td style="text-align:left;"> 30 </td>
   <td style="text-align:right;"> 103 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> Months </td>
   <td style="text-align:left;"> 36 </td>
   <td style="text-align:right;"> 117 </td>
  </tr>
</tbody>
</table>


### Plot the data
One way to plot the data is to layer a scatter plot and a line plot connecting each data point for each participant. These are essentially the raw data and allows us to visualize how each score changes across time for each participant.

```r
#Line plot with scatter plot
ggplot(long.data, aes(x = month, y = scores, group = id, color = id)) +
  geom_point() +
  geom_line() +
  facet_wrap(~id) +
  theme_minimal() +
  theme(axis.line = element_line(color = "grey70"))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-4-1.png" width="672" />



Another way to plot these data is to layer a scatter plot and a regression line computed for each participant. This serves to visualize the variability in the regression line slopes. We notice that the slopes are generally positive. This suggests that the predicted cognitive scores tend to increse with age. However, there are some participants for which this relationship does not hold true.

```r
# Scatter plot with lm
ggplot(long.data, aes(x = month, y = scores, group = id, color = id)) +
  geom_point() +
  geom_smooth(method = "lm", se = FALSE, size = .7) +
  facet_wrap(~ id) +
  theme_minimal() +
  theme(axis.line = element_line(color = "grey70"))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-5-1.png" width="672" />

### Intro to lmer formulas
 To fit LME models we will use the `lmer()` (read as lemur and stands for linear mixed effects regression) function from the lme4 package. The notation for a lmer formula is similar to that of the `lm()` function (y ~ x) where y corresponds to a response variable and x corresponds to a predictor variable. However, the additional component of the formula encased in parentheses consist of a random intercept (1) and the random effect separated by the pipe (|) symbol in the random_intercept_model. In the random_slope_model, the 1 is replaced with the variable to be modeled as a random slope. The random_group variable can also be thought of as the variable under which the observations are nested under. In our case, our observations will be nested under participant.

```r
# Random intercept
random_intercept_model <- lmer(x ~ y + (1|random_group), data = datframe)

# Random slope
random_slope_model <- lmer(x ~ y + (random_slope|random_group), data = datframe)
```

### General approach to for linear mixed effects models
1. Fit a baseline model with out any predictors.
2. Run the ICC to see if there is any clustering in the data.
3. 

### Step 1
In step 1, we will fit a baseline linear mixed effects model without any predictor variables.

```r
# Random intercept without predictor variables
baseline <- lmer(scores ~ 1 + (1|id), data = long.data)
#summary(baseline)
```

### Step 2
Next we will use the `icc()` function from the flexplot package. The flexplot package contains a number of helpful functions for working with linear mixed effect models. The `icc()` function computes the intraclass correlation coefficient which is one way of assessing the degree of clustering in the data. The output in our example is interpreted as roughly 65% of the variance in scores is due to clustering. This is a substantial amount that provides evidence that a linear mixed effect model is warranted.

```r
icc(baseline)
```

```
## $icc
## [1] 0.6514101
## 
## $design.effect
## [1] 2.95423
```

### Step 3
Next, we will build a model that includes the fixed effect of month while maintaining the random intercept for each participant.

```r
# Random intercept, fixed effect of month as categorical, participant as random effect, random intercept for each id.
model.1 <- lmer(scores ~ month + (1|id), data = long.data)
summary(model.1)
```

```
## Linear mixed model fit by REML. t-tests use Satterthwaite's method [
## lmerModLmerTest]
## Formula: scores ~ month + (1 | id)
##    Data: long.data
## 
## REML criterion at convergence: 340.8
## 
## Scaled residuals: 
##      Min       1Q   Median       3Q      Max 
## -1.88627 -0.44709 -0.09063  0.53924  1.70501 
## 
## Random effects:
##  Groups   Name        Variance Std.Dev.
##  id       (Intercept) 135.35   11.634  
##  Residual              60.79    7.797  
## Number of obs: 48, groups:  id, 12
## 
## Fixed effects:
##             Estimate Std. Error      df t value Pr(>|t|)    
## (Intercept)  103.000      4.043  18.117  25.477 1.22e-15 ***
## month36        4.000      3.183  33.000   1.257  0.21769    
## month42        7.000      3.183  33.000   2.199  0.03498 *  
## month48        9.000      3.183  33.000   2.828  0.00791 ** 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Correlation of Fixed Effects:
##         (Intr) mnth36 mnth42
## month36 -0.394              
## month42 -0.394  0.500       
## month48 -0.394  0.500  0.500
```

```r
# analysis of variance
anova(model.1)
```

```
## Type III Analysis of Variance Table with Satterthwaite's method
##       Sum Sq Mean Sq NumDF DenDF F value  Pr(>F)  
## month    552     184     3    33  3.0269 0.04322 *
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

```r
# estimated coefficients, slope was fixed, intercept is random
coef(model.1)
```

```
## $id
##    (Intercept) month36 month42 month48
## 1    103.89905       4       7       9
## 2    113.78865       4       7       9
## 3     99.40378       4       7       9
## 4     86.81703       4       7       9
## 5    114.68770       4       7       9
## 6     96.70662       4       7       9
## 7    120.08203       4       7       9
## 8     93.11041       4       7       9
## 9     90.41324       4       7       9
## 10    96.70662       4       7       9
## 11   103.89905       4       7       9
## 12   116.48581       4       7       9
## 
## attr(,"class")
## [1] "coef.mer"
```

```r
# random effects, deviations from the fixed effect of intercept
ranef(model.1)
```

```
## $id
##    (Intercept)
## 1    0.8990539
## 2   10.7886474
## 3   -3.5962158
## 4  -16.1829711
## 5   11.6877013
## 6   -6.2933776
## 7   17.0820250
## 8   -9.8895934
## 9  -12.5867553
## 10  -6.2933776
## 11   0.8990539
## 12  13.4858092
## 
## with conditional variances for "id"
```

```r
# predicted values
predict(model.1)
```

```
##         1         2         3         4         5         6         7         8 
## 103.89905 107.89905 110.89905 112.89905 113.78865 117.78865 120.78865 122.78865 
##         9        10        11        12        13        14        15        16 
##  99.40378 103.40378 106.40378 108.40378  86.81703  90.81703  93.81703  95.81703 
##        17        18        19        20        21        22        23        24 
## 114.68770 118.68770 121.68770 123.68770  96.70662 100.70662 103.70662 105.70662 
##        25        26        27        28        29        30        31        32 
## 120.08203 124.08203 127.08203 129.08203  93.11041  97.11041 100.11041 102.11041 
##        33        34        35        36        37        38        39        40 
##  90.41324  94.41324  97.41324  99.41324  96.70662 100.70662 103.70662 105.70662 
##        41        42        43        44        45        46        47        48 
## 103.89905 107.89905 110.89905 112.89905 116.48581 120.48581 123.48581 125.48581
```


### Random Intercept Only, No effect of time SAME AS THE BASELINE

```r
# Random intercept model only, no time/month, essentially models the mean for each participant
model.2 <- lmer(scores ~ 1 + (1|id), data = long.data)
summary(model.2)
```

```
## Linear mixed model fit by REML. t-tests use Satterthwaite's method [
## lmerModLmerTest]
## Formula: scores ~ 1 + (1 | id)
##    Data: long.data
## 
## REML criterion at convergence: 361.1
## 
## Scaled residuals: 
##     Min      1Q  Median      3Q     Max 
## -1.8488 -0.6912  0.1359  0.6088  1.7441 
## 
## Random effects:
##  Groups   Name        Variance Std.Dev.
##  id       (Intercept) 132.78   11.523  
##  Residual              71.06    8.429  
## Number of obs: 48, groups:  id, 12
## 
## Fixed effects:
##             Estimate Std. Error      df t value Pr(>|t|)    
## (Intercept)  108.000      3.542  11.000   30.49 5.59e-12 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

```r
anova(model.2, model.1)
```

```
## refitting model(s) with ML (instead of REML)
```

```
## Data: long.data
## Models:
## model.2: scores ~ 1 + (1 | id)
## model.1: scores ~ month + (1 | id)
##         npar    AIC    BIC  logLik deviance Chisq Df Pr(>Chisq)  
## model.2    3 371.47 377.08 -182.73   365.47                      
## model.1    6 368.71 379.94 -178.36   356.71 8.751  3    0.03279 *
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

```r
# Specifies a random intercept
# lmer( y ~ x + (1 | random_group), data = my_data)

# Specifies a random slope
# lmer(y ~ x + (random_slope | random_group),	data = my_data
```



```r
# fixed slopes model
fixed_slopes <- lmer(scores ~ month + (1|id), data = long.data)

# random slopes model
#random_slopes <- lmer(scores ~ month + (month|id), data = long.data)

# visualize the models
#visualize(fixed_slopes, plot = "model")

#compare.fits(scores ~ month | id, data = data.long, baseline, fixed_slopes)
```

### Treat Time quantitatively

```r
model.3 <- lmer(scores ~ month + (1 + as.numeric(month)|id), data = long.data)
summary(model.3)
```

```
## Linear mixed model fit by REML. t-tests use Satterthwaite's method [
## lmerModLmerTest]
## Formula: scores ~ month + (1 + as.numeric(month) | id)
##    Data: long.data
## 
## REML criterion at convergence: 336.4
## 
## Scaled residuals: 
##      Min       1Q   Median       3Q      Max 
## -1.81430 -0.47912 -0.05762  0.57396  1.53583 
## 
## Random effects:
##  Groups   Name              Variance Std.Dev. Corr 
##  id       (Intercept)       220.94   14.864        
##           as.numeric(month)  14.01    3.743   -0.60
##  Residual                    37.44    6.119        
## Number of obs: 48, groups:  id, 12
## 
## Fixed effects:
##             Estimate Std. Error      df t value Pr(>|t|)    
## (Intercept)  103.000      4.138  12.288  24.893 6.89e-12 ***
## month36        4.000      2.722  31.803   1.470   0.1515    
## month42        7.000      3.303  22.434   2.119   0.0454 *  
## month48        9.000      4.092  11.859   2.199   0.0485 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Correlation of Fixed Effects:
##         (Intr) mnth36 mnth42
## month36 -0.421              
## month42 -0.465  0.607       
## month48 -0.471  0.595  0.749
```


### Model the data

```r
# Explicit form of modeling is y ~ 1 + (1|random effect). Executed code is implicit/shortcut form.
# id is selected as the random effect
model <- lmer(scores ~ month + (1|id), data = long.data)
summary(model)
```

```
## Linear mixed model fit by REML. t-tests use Satterthwaite's method [
## lmerModLmerTest]
## Formula: scores ~ month + (1 | id)
##    Data: long.data
## 
## REML criterion at convergence: 340.8
## 
## Scaled residuals: 
##      Min       1Q   Median       3Q      Max 
## -1.88627 -0.44709 -0.09063  0.53924  1.70501 
## 
## Random effects:
##  Groups   Name        Variance Std.Dev.
##  id       (Intercept) 135.35   11.634  
##  Residual              60.79    7.797  
## Number of obs: 48, groups:  id, 12
## 
## Fixed effects:
##             Estimate Std. Error      df t value Pr(>|t|)    
## (Intercept)  103.000      4.043  18.117  25.477 1.22e-15 ***
## month36        4.000      3.183  33.000   1.257  0.21769    
## month42        7.000      3.183  33.000   2.199  0.03498 *  
## month48        9.000      3.183  33.000   2.828  0.00791 ** 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Correlation of Fixed Effects:
##         (Intr) mnth36 mnth42
## month36 -0.394              
## month42 -0.394  0.500       
## month48 -0.394  0.500  0.500
```

```r
anova(model)
```

```
## Type III Analysis of Variance Table with Satterthwaite's method
##       Sum Sq Mean Sq NumDF DenDF F value  Pr(>F)  
## month    552     184     3    33  3.0269 0.04322 *
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

```r
#plot(model)
```
### Effects table

```r
#fixef(model)
#ranef(model)
#confint(model)
kable(tidy(model, conf.int = TRUE))
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> effect </th>
   <th style="text-align:left;"> group </th>
   <th style="text-align:left;"> term </th>
   <th style="text-align:right;"> estimate </th>
   <th style="text-align:right;"> std.error </th>
   <th style="text-align:right;"> statistic </th>
   <th style="text-align:right;"> df </th>
   <th style="text-align:right;"> p.value </th>
   <th style="text-align:right;"> conf.low </th>
   <th style="text-align:right;"> conf.high </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> fixed </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> (Intercept) </td>
   <td style="text-align:right;"> 103.000000 </td>
   <td style="text-align:right;"> 4.042858 </td>
   <td style="text-align:right;"> 25.477029 </td>
   <td style="text-align:right;"> 18.1174 </td>
   <td style="text-align:right;"> 0.0000000 </td>
   <td style="text-align:right;"> 94.5102150 </td>
   <td style="text-align:right;"> 111.48978 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fixed </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> month36 </td>
   <td style="text-align:right;"> 4.000000 </td>
   <td style="text-align:right;"> 3.182972 </td>
   <td style="text-align:right;"> 1.256687 </td>
   <td style="text-align:right;"> 33.0000 </td>
   <td style="text-align:right;"> 0.2176899 </td>
   <td style="text-align:right;"> -2.4758059 </td>
   <td style="text-align:right;"> 10.47581 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fixed </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> month42 </td>
   <td style="text-align:right;"> 7.000000 </td>
   <td style="text-align:right;"> 3.182972 </td>
   <td style="text-align:right;"> 2.199202 </td>
   <td style="text-align:right;"> 33.0000 </td>
   <td style="text-align:right;"> 0.0349787 </td>
   <td style="text-align:right;"> 0.5241941 </td>
   <td style="text-align:right;"> 13.47581 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fixed </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> month48 </td>
   <td style="text-align:right;"> 9.000000 </td>
   <td style="text-align:right;"> 3.182972 </td>
   <td style="text-align:right;"> 2.827546 </td>
   <td style="text-align:right;"> 33.0000 </td>
   <td style="text-align:right;"> 0.0079113 </td>
   <td style="text-align:right;"> 2.5241941 </td>
   <td style="text-align:right;"> 15.47581 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ran_pars </td>
   <td style="text-align:left;"> id </td>
   <td style="text-align:left;"> sd__(Intercept) </td>
   <td style="text-align:right;"> 11.633937 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ran_pars </td>
   <td style="text-align:left;"> Residual </td>
   <td style="text-align:left;"> sd__Observation </td>
   <td style="text-align:right;"> 7.796658 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
  </tr>
</tbody>
</table>

### Plot the parameter estimates

```r
coef_estimates <- tidy(model, conf.int = TRUE) %>%
  filter(effect == "fixed")

ggplot(coef_estimates, 
       aes(x = term, y = estimate, 
           ymin = conf.low, ymax = conf.high)) +
  geom_hline( yintercept = 0, color = "#1565c0" ) +
  geom_linerange(color = "#1565c0") + 
  geom_point(color = "#1565c0") + 
  coord_flip() + 
  theme_minimal() +
  theme(axis.line = element_line(color = "grey70"))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-15-1.png" width="672" />




<!-- --------------------------------------------- -->

```r
library(car)
library(nlme)
```

```
## 
## Attaching package: 'nlme'
```

```
## The following object is masked from 'package:lme4':
## 
##     lmList
```

```
## The following object is masked from 'package:dplyr':
## 
##     collapse
```

```r
##Load the data file into R. This is a tab-delimited file hence use of read.delim
surgeryData = read.delim("Cosmetic Surgery.dat",  header = TRUE)

#Run an ANOVA
surgeryANOVA<-aov(Post_QoL~Surgery, data = surgeryData)
summary(surgeryANOVA)
```

```
##              Df Sum Sq Mean Sq F value Pr(>F)
## Surgery       1     29   28.62    0.33  0.566
## Residuals   274  23748   86.67
```

```r
# run the same model but using the linear models command
surgeryLinearModel<-lm(Post_QoL~Surgery, data = surgeryData)
summary(surgeryLinearModel)
```

```
## 
## Call:
## lm(formula = Post_QoL ~ Surgery, data = surgeryData)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -19.916  -7.271  -1.271   7.084  28.284 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  59.9159     0.7731  77.498   <2e-16 ***
## Surgery      -0.6449     1.1222  -0.575    0.566    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 9.31 on 274 degrees of freedom
## Multiple R-squared:  0.001204,	Adjusted R-squared:  -0.002442 
## F-statistic: 0.3302 on 1 and 274 DF,  p-value: 0.566
```

```r
#Run an ANCOVA
surgeryANCOVA<-aov(Post_QoL~Base_QoL + Surgery, data = surgeryData)
summary(surgeryANCOVA)
```

```
##              Df Sum Sq Mean Sq F value Pr(>F)    
## Base_QoL      1  10291   10291 211.432 <2e-16 ***
## Surgery       1    197     197   4.043 0.0453 *  
## Residuals   273  13288      49                   
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

```r
Anova(surgeryANCOVA, type = "III")
```

```
## Anova Table (Type III tests)
## 
## Response: Post_QoL
##              Sum Sq  Df  F value    Pr(>F)    
## (Intercept)  1896.0   1  38.9513 1.653e-09 ***
## Base_QoL    10459.6   1 214.8876 < 2.2e-16 ***
## Surgery       196.8   1   4.0435   0.04533 *  
## Residuals   13288.3 273                       
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

```r
# run the same model but using the linear models command
surgeryLinearModel<-lm(Post_QoL~Surgery + Base_QoL, data = surgeryData)
summary(surgeryLinearModel)
```

```
## 
## Call:
## lm(formula = Post_QoL ~ Surgery + Base_QoL, data = surgeryData)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -13.4142  -5.1326  -0.6495   4.0540  23.5005 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 18.14702    2.90767   6.241 1.65e-09 ***
## Surgery     -1.69723    0.84404  -2.011   0.0453 *  
## Base_QoL     0.66504    0.04537  14.659  < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 6.977 on 273 degrees of freedom
## Multiple R-squared:  0.4411,	Adjusted R-squared:  0.437 
## F-statistic: 107.7 on 2 and 273 DF,  p-value: < 2.2e-16
```

```r
##Fit baseline models
#Fit model with intercept only
interceptOnly <-gls(Post_QoL~1, data = surgeryData, method = "ML")
summary(interceptOnly)
```

```
## Generalized least squares fit by maximum likelihood
##   Model: Post_QoL ~ 1 
##   Data: surgeryData 
##        AIC      BIC    logLik
##   2017.124 2024.365 -1006.562
## 
## Coefficients:
##                Value Std.Error  t-value p-value
## (Intercept) 59.60978 0.5596972 106.5036       0
## 
## Standardized residuals:
##        Min         Q1        Med         Q3        Max 
## -2.1127754 -0.7875625 -0.1734394  0.7962286  3.0803354 
## 
## Residual standard error: 9.281527 
## Degrees of freedom: 276 total; 275 residual
```

```r
# Random Intercepts Models with lmer -------
# The random effects are encased in parentheses and have a pipe. A (1|clinic), designates
# that the intercept is going to be allowed to vary by clinic. This is equivalent to the mean
# for the outcome variable will be different within each clinic. In other words, we think the magnitude of the effect is the same, but they start out at different places.
# Use REML = FALSE to specify Maximum Likelihood as the method

#Fit model allowing intercepts to vary by clinic
randomInterceptOnly <- lmer(Post_QoL ~ 1 + (1|Clinic), data = surgeryData, REML = FALSE)
summary(randomInterceptOnly)
```

```
## Linear mixed model fit by maximum likelihood . t-tests use Satterthwaite's
##   method [lmerModLmerTest]
## Formula: Post_QoL ~ 1 + (1 | Clinic)
##    Data: surgeryData
## 
##      AIC      BIC   logLik deviance df.resid 
##   1911.5   1922.3   -952.7   1905.5      273 
## 
## Scaled residuals: 
##     Min      1Q  Median      3Q     Max 
## -1.8828 -0.7607 -0.1379  0.7075  2.8608 
## 
## Random effects:
##  Groups   Name        Variance Std.Dev.
##  Clinic   (Intercept) 34.92    5.910   
##  Residual             52.40    7.239   
## Number of obs: 276, groups:  Clinic, 10
## 
## Fixed effects:
##             Estimate Std. Error     df t value Pr(>|t|)    
## (Intercept)   60.084      1.920  9.964    31.3 2.78e-11 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

```r
logLik(randomInterceptOnly)*-2
```

```
## 'log Lik.' 1905.473 (df=3)
```

```r
#Add surgery as a predictor
randomInterceptSurgery <-lmer(Post_QoL ~ Surgery + (1|Clinic), data = surgeryData, REML = FALSE)
summary(randomInterceptSurgery)
```

```
## Linear mixed model fit by maximum likelihood . t-tests use Satterthwaite's
##   method [lmerModLmerTest]
## Formula: Post_QoL ~ Surgery + (1 | Clinic)
##    Data: surgeryData
## 
##      AIC      BIC   logLik deviance df.resid 
##   1910.1   1924.6   -951.1   1902.1      272 
## 
## Scaled residuals: 
##     Min      1Q  Median      3Q     Max 
## -1.8904 -0.7191 -0.1421  0.7178  2.8645 
## 
## Random effects:
##  Groups   Name        Variance Std.Dev.
##  Clinic   (Intercept) 37.20    6.100   
##  Residual             51.63    7.185   
## Number of obs: 276, groups:  Clinic, 10
## 
## Fixed effects:
##             Estimate Std. Error       df t value Pr(>|t|)    
## (Intercept)  59.3052     2.0226  10.8391  29.321 1.13e-11 ***
## Surgery       1.6658     0.9058 268.5426   1.839    0.067 .  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Correlation of Fixed Effects:
##         (Intr)
## Surgery -0.210
```

```r
##Fit effect of surgery and baseline QoL- random intercepts across clinics
randomInterceptSurgeryQoL <-lmer(Post_QoL ~ Surgery + Base_QoL + (1|Clinic), data = surgeryData, REML = FALSE)
summary(randomInterceptSurgeryQoL)
```

```
## Linear mixed model fit by maximum likelihood . t-tests use Satterthwaite's
##   method [lmerModLmerTest]
## Formula: Post_QoL ~ Surgery + Base_QoL + (1 | Clinic)
##    Data: surgeryData
## 
##      AIC      BIC   logLik deviance df.resid 
##   1847.5   1865.6   -918.7   1837.5      271 
## 
## Scaled residuals: 
##     Min      1Q  Median      3Q     Max 
## -1.8873 -0.7538 -0.0955  0.5657  3.0021 
## 
## Random effects:
##  Groups   Name        Variance Std.Dev.
##  Clinic   (Intercept)  9.237   3.039   
##  Residual             42.497   6.519   
## Number of obs: 276, groups:  Clinic, 10
## 
## Fixed effects:
##              Estimate Std. Error        df t value Pr(>|t|)    
## (Intercept)  29.56360    3.45296 163.87849   8.562 7.71e-15 ***
## Surgery      -0.31300    0.83855 275.63110  -0.373    0.709    
## Base_QoL      0.47863    0.05249 245.01977   9.119  < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Correlation of Fixed Effects:
##          (Intr) Surgry
## Surgery   0.102       
## Base_QoL -0.947 -0.222
```

```r
# Compare the previous three lmers
anova(randomInterceptOnly, randomInterceptSurgery, randomInterceptSurgeryQoL)
```

```
## Data: surgeryData
## Models:
## randomInterceptOnly: Post_QoL ~ 1 + (1 | Clinic)
## randomInterceptSurgery: Post_QoL ~ Surgery + (1 | Clinic)
## randomInterceptSurgeryQoL: Post_QoL ~ Surgery + Base_QoL + (1 | Clinic)
##                           npar    AIC    BIC  logLik deviance   Chisq Df
## randomInterceptOnly          3 1911.5 1922.3 -952.74   1905.5           
## randomInterceptSurgery       4 1910.1 1924.6 -951.07   1902.1  3.3356  1
## randomInterceptSurgeryQoL    5 1847.5 1865.6 -918.74   1837.5 64.6472  1
##                           Pr(>Chisq)    
## randomInterceptOnly                     
## randomInterceptSurgery       0.06779 .  
## randomInterceptSurgeryQoL  8.958e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

```r
##Fit effect of surgery and baseline QoL- random slopes and intercepts across clinics
addRandomSlope <- lmer(Post_QoL ~ Surgery + Base_QoL + (Surgery|Clinic), 
                       data = surgeryData, 
                       REML = FALSE)
summary(addRandomSlope)
```

```
## Linear mixed model fit by maximum likelihood . t-tests use Satterthwaite's
##   method [lmerModLmerTest]
## Formula: Post_QoL ~ Surgery + Base_QoL + (Surgery | Clinic)
##    Data: surgeryData
## 
##      AIC      BIC   logLik deviance df.resid 
##   1812.6   1838.0   -899.3   1798.6      269 
## 
## Scaled residuals: 
##     Min      1Q  Median      3Q     Max 
## -2.4115 -0.6629 -0.1138  0.6833  2.8335 
## 
## Random effects:
##  Groups   Name        Variance Std.Dev. Corr 
##  Clinic   (Intercept) 37.61    6.133         
##           Surgery     38.41    6.198    -0.97
##  Residual             34.96    5.912         
## Number of obs: 276, groups:  Clinic, 10
## 
## Fixed effects:
##              Estimate Std. Error        df t value Pr(>|t|)    
## (Intercept)  40.10258    3.87174  84.95154  10.358  < 2e-16 ***
## Surgery      -0.65452    2.09943   9.51752  -0.312    0.762    
## Base_QoL      0.31022    0.05321 265.93267   5.830 1.61e-08 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Correlation of Fixed Effects:
##          (Intr) Surgry
## Surgery  -0.430       
## Base_QoL -0.855 -0.063
```

```r
anova(randomInterceptSurgeryQoL,addRandomSlope)
```

```
## Data: surgeryData
## Models:
## randomInterceptSurgeryQoL: Post_QoL ~ Surgery + Base_QoL + (1 | Clinic)
## addRandomSlope: Post_QoL ~ Surgery + Base_QoL + (Surgery | Clinic)
##                           npar    AIC    BIC  logLik deviance  Chisq Df
## randomInterceptSurgeryQoL    5 1847.5 1865.6 -918.74   1837.5          
## addRandomSlope               7 1812.6 1838.0 -899.31   1798.6 38.866  2
##                           Pr(>Chisq)    
## randomInterceptSurgeryQoL               
## addRandomSlope             3.633e-09 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

```r
##Fit effect of surgery and baseline QoL, Reason and Reason*Surgery Interaction- random slopes and intercepts across clinics
addReason<-lmer(Post_QoL ~ Surgery + Base_QoL + Reason + (Surgery|Clinic), data = surgeryData, REML = FALSE)
addReason<-update(addRandomSlope, .~. + Reason)
summary(addReason)
```

```
## Linear mixed model fit by maximum likelihood . t-tests use Satterthwaite's
##   method [lmerModLmerTest]
## Formula: Post_QoL ~ Surgery + Base_QoL + (Surgery | Clinic) + Reason
##    Data: surgeryData
## 
##      AIC      BIC   logLik deviance df.resid 
##   1810.8   1839.8   -897.4   1794.8      268 
## 
## Scaled residuals: 
##     Min      1Q  Median      3Q     Max 
## -2.2006 -0.6831 -0.1257  0.6730  2.9818 
## 
## Random effects:
##  Groups   Name        Variance Std.Dev. Corr 
##  Clinic   (Intercept) 34.08    5.838         
##           Surgery     38.09    6.172    -0.97
##  Residual             34.66    5.887         
## Number of obs: 276, groups:  Clinic, 10
## 
## Fixed effects:
##              Estimate Std. Error        df t value Pr(>|t|)    
## (Intercept)  41.43184    3.90133  98.86325  10.620  < 2e-16 ***
## Surgery      -0.56816    2.09134   9.52201  -0.272   0.7917    
## Base_QoL      0.30535    0.05303 263.87502   5.758 2.36e-08 ***
## Reason       -1.69136    0.84820 220.28439  -1.994   0.0474 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Correlation of Fixed Effects:
##          (Intr) Surgry Bas_QL
## Surgery  -0.400              
## Base_QoL -0.862 -0.064       
## Reason   -0.231 -0.042  0.118
```

```r
finalModel<-lmer(Post_QoL~Surgery + Base_QoL + Reason + Reason:Surgery + (Surgery|Clinic), REML = FALSE, data = surgeryData, )
summary(finalModel)
```

```
## Linear mixed model fit by maximum likelihood . t-tests use Satterthwaite's
##   method [lmerModLmerTest]
## Formula: Post_QoL ~ Surgery + Base_QoL + Reason + Reason:Surgery + (Surgery |  
##     Clinic)
##    Data: surgeryData
## 
##      AIC      BIC   logLik deviance df.resid 
##   1807.0   1839.6   -894.5   1789.0      267 
## 
## Scaled residuals: 
##     Min      1Q  Median      3Q     Max 
## -2.2331 -0.6972 -0.1541  0.6326  3.1642 
## 
## Random effects:
##  Groups   Name        Variance Std.Dev. Corr 
##  Clinic   (Intercept) 30.06    5.482         
##           Surgery     29.35    5.417    -0.95
##  Residual             33.86    5.819         
## Number of obs: 276, groups:  Clinic, 10
## 
## Fixed effects:
##                 Estimate Std. Error        df t value Pr(>|t|)    
## (Intercept)     42.51780    3.84005 108.85403  11.072  < 2e-16 ***
## Surgery         -3.18768    2.16548  15.86367  -1.472  0.16057    
## Base_QoL         0.30536    0.05264 268.91951   5.801 1.85e-08 ***
## Reason          -3.51515    1.13055 259.89415  -3.109  0.00208 ** 
## Surgery:Reason   4.22129    1.68480 217.08708   2.506  0.01296 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Correlation of Fixed Effects:
##             (Intr) Surgry Bas_QL Reason
## Surgery     -0.356                     
## Base_QoL    -0.865 -0.078              
## Reason      -0.233  0.306  0.065       
## Surgery:Rsn  0.096 -0.505  0.024 -0.661
```

```r
#intervals(finalModel, 0.95) #intervals() doesn't work on lmerMod objects

anova(addRandomSlope, addReason, finalModel)
```

```
## Data: surgeryData
## Models:
## addRandomSlope: Post_QoL ~ Surgery + Base_QoL + (Surgery | Clinic)
## addReason: Post_QoL ~ Surgery + Base_QoL + (Surgery | Clinic) + Reason
## finalModel: Post_QoL ~ Surgery + Base_QoL + Reason + Reason:Surgery + (Surgery | Clinic)
##                npar    AIC    BIC  logLik deviance  Chisq Df Pr(>Chisq)  
## addRandomSlope    7 1812.6 1838.0 -899.31   1798.6                       
## addReason         8 1810.8 1839.8 -897.41   1794.8 3.7990  1    0.05128 .
## finalModel        9 1807.0 1839.6 -894.52   1789.0 5.7796  1    0.01621 *
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```








<!-- NOTES:
intraclass correlations: The ICC is a measure of the proportion of variance that is attributable to a randome factor. It's also used as an effect size measure.

fixed effect: In some situations, we are only interested in specific levels of a factor. 

random effect: In other situations we may be interested in generalizing from the levels of a factor studied to other possible levels. For example, if we want to study the effects of treatment on a disorder we have to consider the therapist as any change in mental health is partly attributable to the therapist. However, we can't study all therapists, we have to select a sample of therapists, but would want to generalize beyond those. Because these therapists represent a random sample of the therapists available, they are termed random effects.

Random factor: factors that have randomly selected factors. 

Mixed model: contains a mixture of fixed and random effects

Random effects - 
Fixed effects - 

ML - Maximum Likelihood, one of the ways that the parameters of the model can be estimated. Produces more accurate estimates of fixed regression parameters. This needs to be chosen to compare models.
REML - Restricted Maximum Likelihood, produces more accurate estimates of random variances.



-->











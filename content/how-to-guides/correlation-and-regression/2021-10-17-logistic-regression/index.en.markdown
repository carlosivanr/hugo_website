---
title: Logistic Regression (work in progress)
author: Carlos Rodriguez
date: '2021-10-17'
slug: logistic-regression
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-10-17T18:49:28-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 75
draft: true
---


Logistic regression is used in cases where you have a binary outcome or response variable. 

Did someone die or not, did they survive the hospital stay or not. 

```r
library(car)
library(kableExtra)
#library(mlogit)
```

### Load Data

```r
#load data
eelData <- read.delim("eel.dat", header = TRUE)
```

### Inspect Data

```r
#look at first 6 cases of data
kable(head(eelData))
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> Cured </th>
   <th style="text-align:left;"> Intervention </th>
   <th style="text-align:right;"> Duration </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Not Cured </td>
   <td style="text-align:left;"> No Treatment </td>
   <td style="text-align:right;"> 7 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Not Cured </td>
   <td style="text-align:left;"> No Treatment </td>
   <td style="text-align:right;"> 7 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Not Cured </td>
   <td style="text-align:left;"> No Treatment </td>
   <td style="text-align:right;"> 6 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Cured </td>
   <td style="text-align:left;"> No Treatment </td>
   <td style="text-align:right;"> 8 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Cured </td>
   <td style="text-align:left;"> Intervention </td>
   <td style="text-align:right;"> 7 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Cured </td>
   <td style="text-align:left;"> No Treatment </td>
   <td style="text-align:right;"> 6 </td>
  </tr>
</tbody>
</table>

### Prepare data
Before building our logistic regression model, we will need to convert Cured and Intervention to factors. Additionally, we will need to order the levels so that Not Cured and No Treatment are the baseline categories and set as 0 rather than 1.

```r
eelData$Cured <- factor(eelData$Cured, levels = c("Not Cured", "Cured"))
eelData$Intervention <- factor(eelData$Intervention, levels = c("No Treatment", "Intervention"))
```

### Model 1
Logistic regression models fall under the generalized linear model (GLM) family, which is why we will use the `glm()` function. In our first model, we will try to predict whether or not a patient was cured or not, based on the type of intervention they received. The GLM family includes several types of model that include logistic, poisson, negative binomial, gamma, and ordered logistic models. For logistic regression, we will need to set family = binomial() to specify the random component of our model.

```r
eel_model.1 <- glm(Cured ~ Intervention, data = eelData, family = binomial())

summary(eel_model.1)
```

```
## 
## Call:
## glm(formula = Cured ~ Intervention, family = binomial(), data = eelData)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -1.5940  -1.0579   0.8118   0.8118   1.3018  
## 
## Coefficients:
##                          Estimate Std. Error z value Pr(>|z|)   
## (Intercept)               -0.2877     0.2700  -1.065  0.28671   
## InterventionIntervention   1.2287     0.3998   3.074  0.00212 **
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 154.08  on 112  degrees of freedom
## Residual deviance: 144.16  on 111  degrees of freedom
## AIC: 148.16
## 
## Number of Fisher Scoring iterations: 4
```



### Chi square model on deviance

```r
chi_model <- eel_model.1$null.deviance - eel_model.1$deviance
chidf <- eel_model.1$df.null - eel_model.1$df.residual
chisq.prob <- 1 - pchisq(chi_model, chidf)
chi_model; chidf; chisq.prob
```

```
## [1] 9.926201
```

```
## [1] 1
```

```
## [1] 0.001629425
```

```r
R2.hl <- chi_model/eel_model.1$null.deviance
R.cs <- 1 - exp ((eel_model.1$deviance - eel_model.1$null.deviance)/113)
R.n <- R.cs /( 1- ( exp (-(eel_model.1$null.deviance/ 113))))
```


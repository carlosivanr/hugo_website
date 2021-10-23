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
---


```r
library(car)
```

```
## Loading required package: carData
```

```r
#library(mlogit)
```


```r
#load data
eelData<-read.delim("eel.dat", header = TRUE)


#look at first 6 cases of data
head(eelData)
```

```
##       Cured Intervention Duration
## 1 Not Cured No Treatment        7
## 2 Not Cured No Treatment        7
## 3 Not Cured No Treatment        6
## 4     Cured No Treatment        8
## 5     Cured Intervention        7
## 6     Cured No Treatment        6
```

```r
#Alternatively Re-orders the levels of the factyor so that Not Cured and No Treatment are the baseline categories
eelData$Cured<-factor(eelData$Cured, levels = c("Not Cured", "Cured"))
eelData$Intervention<-factor(eelData$Intervention, levels = c("No Treatment", "Intervention"))
```

### Model 1

```r
eelModel.1 <- glm(Cured ~ Intervention, data = eelData, family = binomial())

summary(eelModel.1)
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

```r
#Just to prove what the null deviance is
eelModel.0 <- glm(Cured ~ 1, data = eelData, family = binomial())
summary(eelModel.0)
```

```
## 
## Call:
## glm(formula = Cured ~ 1, family = binomial(), data = eelData)
## 
## Deviance Residuals: 
##    Min      1Q  Median      3Q     Max  
## -1.309  -1.309   1.052   1.052   1.052  
## 
## Coefficients:
##             Estimate Std. Error z value Pr(>|z|)
## (Intercept)   0.3032     0.1903   1.593    0.111
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 154.08  on 112  degrees of freedom
## Residual deviance: 154.08  on 112  degrees of freedom
## AIC: 156.08
## 
## Number of Fisher Scoring iterations: 4
```

```r
modelChi <- eelModel.1$null.deviance - eelModel.1$deviance
chidf <- eelModel.1$df.null - eelModel.1$df.residual
chisq.prob <- 1 - pchisq(modelChi, chidf)
modelChi; chidf; chisq.prob
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
R2.hl<-modelChi/eelModel.1$null.deviance
R.cs <- 1 - exp ((eelModel.1$deviance - eelModel.1$null.deviance)/113)
R.n <- R.cs /( 1- ( exp (-(eelModel.1$null.deviance/ 113))))
```

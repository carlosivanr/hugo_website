---
title: Logistic Regression Pt.2 - Two Predictor Variables & Casewise Diagnostics
author: Carlos Rodriguez
date: '2021-11-01'
slug: logistic-regression-2
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-11-01T19:48:10-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 80
draft: false
---

In this guide we continue with logistic regression by adding a second predictor variable to our model, compare the model with one predictor to the model with two predictors, and finally inspect the model diagnostics. 






### Model data with two predictors
In our second model, we will include an additional variable, "Duration," which measures the amount of time each participant had spent dealing with the illness before seeking medical treatment. Notice that the residual deviance for the second model is the exact same as the residual deviance for the first model. The residual deviance is one way assess model fit thus including the "Duration" variable does not change the residual deviance. 

An additional piece of information to consider is the Akaike Information Criterion (AIC). The AIC is another way of assessing model fit that takes into consideration the number of predictors in the model. The AIC for the model.2 is larger than the AIC for model.1 which suggests that model.1 is the better model as lower values are better. After considering the deviance statistic and increased AIC, we can then conclude that adding the "Duration" variable does not improve the fit of model.


```r
eel_model.2 <- glm(Cured ~ Intervention + Duration, family = binomial(), data = eelData)

summary(eel_model.2)
```

```
## 
## Call:
## glm(formula = Cured ~ Intervention + Duration, family = binomial(), 
##     data = eelData)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -1.6025  -1.0572   0.8107   0.8161   1.3095  
## 
## Coefficients:
##                           Estimate Std. Error z value Pr(>|z|)   
## (Intercept)              -0.234660   1.220563  -0.192  0.84754   
## InterventionIntervention  1.233532   0.414565   2.975  0.00293 **
## Duration                 -0.007835   0.175913  -0.045  0.96447   
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 154.08  on 112  degrees of freedom
## Residual deviance: 144.16  on 110  degrees of freedom
## AIC: 150.16
## 
## Number of Fisher Scoring iterations: 4
```

### Compare Models
A more objective way to compare logistic regression models is perform a chi-squared test, since the differences in the deviance statistics follow a chi-square distribution. To accomplish this, we simply subtract the model.2 deviance from model.1, subtract the degrees of freedom from model.2 from the degrees of freedom from model.1, and then utilize the resulting values with the pchisq() function. The result of these calculations indicate that the p-value is 0.964 and thus model.2 does not represent a significant improvement when compared to model.1.


```r
modelChi <- eel_model.1$deviance - eel_model.2$deviance
chidf <- eel_model.1$df.residual - eel_model.2$df.residual
chisq.prob <- 1 - pchisq(modelChi, chidf)
modelChi; chidf; chisq.prob
```

```
## [1] 0.001983528
```

```
## [1] 1
```

```
## [1] 0.9644765
```

A second way of comparing models is to use the `anova()` function passing the model in the order of which they were constructed. The results with respect to the residual deviance, degrees of freedom, and the difference between the deviance statistics are identical. The only difference is that this approach does not provide a p-value.

```r
anova(eel_model.1, eel_model.2)
```

```
## Analysis of Deviance Table
## 
## Model 1: Cured ~ Intervention
## Model 2: Cured ~ Intervention + Duration
##   Resid. Df Resid. Dev Df  Deviance
## 1       111     144.16             
## 2       110     144.16  1 0.0019835
```

### Diagnostics
The final step in this guide will be to perform diagnostics. Since model.2 was not an improvement from model.1, we will focus on examining the predicted probabilities, residuals, degrees of freedom for our coefficients, and leverage values from model.1.

```r
eelData$predicted.probabilities <- fitted(eel_model.1)
eelData$standardized.residuals <- rstandard(eel_model.1)
eelData$studentized.residuals <- rstudent(eel_model.1)
eelData$dfbeta <- dfbeta(eel_model.1)
eelData$dffit <- dffits(eel_model.1)
eelData$leverage <- hatvalues(eel_model.1)
```

### Predicted probabilities
The output of the table lists the first several cases of predicted probabilities. Recall that intervention was the only significant predictor in this model. Intervention can only take on a value of 0 or 1, either No Treatment or Intervention. The predicted probability of getting cured without treatment is 0.43 which means that about 43% of these patients will be cured. However, the predicted probability for getting the intervention is .72 which means that about 72% of patients receiving the intervention will be cured.

```r
kable(head(eelData %>%
  select(Cured, Intervention, Duration, predicted.probabilities)))
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> Cured </th>
   <th style="text-align:left;"> Intervention </th>
   <th style="text-align:right;"> Duration </th>
   <th style="text-align:right;"> predicted.probabilities </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Not Cured </td>
   <td style="text-align:left;"> No Treatment </td>
   <td style="text-align:right;"> 7 </td>
   <td style="text-align:right;"> 0.4285714 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Not Cured </td>
   <td style="text-align:left;"> No Treatment </td>
   <td style="text-align:right;"> 7 </td>
   <td style="text-align:right;"> 0.4285714 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Not Cured </td>
   <td style="text-align:left;"> No Treatment </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> 0.4285714 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Cured </td>
   <td style="text-align:left;"> No Treatment </td>
   <td style="text-align:right;"> 8 </td>
   <td style="text-align:right;"> 0.4285714 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Cured </td>
   <td style="text-align:left;"> Intervention </td>
   <td style="text-align:right;"> 7 </td>
   <td style="text-align:right;"> 0.7192982 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Cured </td>
   <td style="text-align:left;"> No Treatment </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> 0.4285714 </td>
  </tr>
</tbody>
</table>

### Residuals
As with linear regression, we want to inspect the residuals to identify cases in which the model fits poorly and identify cases that may have undue influence in our model. We use the same code used in linear regression to determine that there are no cases in which the residual is excessively large. We expect that only about 5% of cases should lie outside +/-1.96 and about 1% of cases should like outside +/-2.58. We should also expect leverage values to near the expected value which is determined by (k+1)/N where k is the number of predictors and n is the sample size. We can also inspect the dfbetas as these should all be less than 1.

```r
# Create a boolean vector of large residuals; greater than 2 or less than -2 
eelData$large.residual <- eelData$standardized.residuals > 2 | eelData$standardized.residuals < -2

# Sum of large standardized residuals
sum(eelData$large.residual)
```

```
## [1] 0
```

```r
# Expected leverage value k+1/n, where k+1 is the number of predictors and n is the sample size
length(eel_model.1$coefficients)/length(eel_model.1$residuals)
```

```
## [1] 0.01769912
```

```r
# Display leverage, standardized residuals and dfbeta
kable(head(eelData %>%
  select(Cured, leverage, standardized.residuals, dfbeta)))
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> Cured </th>
   <th style="text-align:right;"> leverage </th>
   <th style="text-align:right;"> standardized.residuals </th>
   <th style="text-align:right;"> dfbeta </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Not Cured </td>
   <td style="text-align:right;"> 0.0178571 </td>
   <td style="text-align:right;"> -1.0675117 </td>
   <td style="text-align:right;"> -3.886912e-02 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Not Cured </td>
   <td style="text-align:right;"> 0.0178571 </td>
   <td style="text-align:right;"> -1.0675117 </td>
   <td style="text-align:right;"> -3.886912e-02 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Not Cured </td>
   <td style="text-align:right;"> 0.0178571 </td>
   <td style="text-align:right;"> -1.0675117 </td>
   <td style="text-align:right;"> -3.886912e-02 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Cured </td>
   <td style="text-align:right;"> 0.0178571 </td>
   <td style="text-align:right;"> 1.3135473 </td>
   <td style="text-align:right;"> 4.782751e-02 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Cured </td>
   <td style="text-align:right;"> 0.0175439 </td>
   <td style="text-align:right;"> 0.8189783 </td>
   <td style="text-align:right;"> 2.283164e-18 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Cured </td>
   <td style="text-align:right;"> 0.0178571 </td>
   <td style="text-align:right;"> 1.3135473 </td>
   <td style="text-align:right;"> 4.782751e-02 </td>
  </tr>
</tbody>
</table>


### References 

<div id="refs" class="references">

<div id="ref-DSUR">

Field, Andy, Jeremy Miles, and Zoe Field. n.d. *Discovering Statistics Using R*. Sage.

</div>

<div id="ref-R-car">

Fox, John, Sanford Weisberg, and Brad Price. 2020. *Car: Companion to Applied Regression*. <https://CRAN.R-project.org/package=car>.

</div>

<div id="ref-R-tidyverse">

Wickham, Hadley. 2021. *Tidyverse: Easily Install and Load the Tidyverse*. <https://CRAN.R-project.org/package=tidyverse>.

</div>

</div>

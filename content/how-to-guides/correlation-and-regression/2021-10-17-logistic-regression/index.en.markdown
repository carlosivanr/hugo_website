---
title: Logistic Regression Pt.1 - Binary Logistic Regression (one dichotomous predictor)
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
draft: false
---


Logistic regression is a statistical technique to understand the relationship between categorical outcome variables and categorical or continuous predictor variables. Binary logistic regression is used in circumstances to understand binary outcomes such as whether or not someone survived a hostpital stay or whether or not a medical intervention was successful or not. For this guide, we will work with example data in which eels are used an an experimental treatment to cure a medical condition. Our outcome will fall into one of two possibilities, Cured or Not Cured, and our predictor is whether someone received the Intervention or not. Thus we have a situation in which a binary outcome will be predicted by a dichotomous predictor variable.


```r
library(car)
library(kableExtra)
library(tidyverse)
```

### Load data

```r
#load data
eelData <- read.delim("eel.dat", header = TRUE)
```

### Inspect data

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
Before building our logistic regression model, we will need to convert Cured and Intervention to factors. Additionally, we will need to order the levels so that Not Cured and No Treatment are the baseline categories and are set as 0 rather than 1.

```r
eelData$Cured <- factor(eelData$Cured, levels = c("Not Cured", "Cured"))
eelData$Intervention <- factor(eelData$Intervention, levels = c("No Treatment", "Intervention"))
```

### Visualize data
The data consist of a dichotomous outcome variable, Cured or Not Cured, and a dichotomous predictor variable, Intervention or No Treatment. One way to visualize these data is through a stacked bar chart where we can count each possible combination of Cured and Intervention variables to get an idea of the value counts.

```r
colors <- c( "#440154FF","#1565c0")
ggplot(eelData, aes(x = Cured, color = Intervention, fill = Intervention)) +
  stat_count(alpha = 0.7) +
  scale_color_manual(values = colors) +
  scale_fill_manual(values = colors) +
  theme_minimal() +
  theme(axis.line = element_line(color = "grey70"))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-5-1.png" width="672" />




### Model the data
For our first logistic regression model, we will attempt to predict Cured from Intervention. Instead of using the `lm()` function as in the linear regression, we will use the `glm()` function. For logistic regression, we will need to set the  `family = binomial()` argument, and then specify the data. Lastly, we will use the `summary()` to display the output.

```r
eel_model.1 <- glm(Cured ~ Intervention, family = binomial(), data = eelData)

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

### Walkthrough of R's logistic regression output
The output of the `summary()` function can be divided into four sections. 
  * The first section is the "**Call:**," which is simply a reproduction of the `glm()` function used to create the model. 
  * The next section, "**Deviance Residuals:**," contains information about the differences between the model predictions the and the actual values. 
  * In the third, "**Coefficients:**," section we find information regarding the model parameters. The same section will contain a standard error, z-value, and p-value.
  * Finally, the last section displays information about the **overall fit** of the model. In logistic regression, the primary values to inspect are the Null and Residual deviance statistics.

### Deviance statistics
The "Null deviance" value provides information on a model that does not contain any predictors other than the constant. In contrast, the "Residual deviance" value provides information regarding fit in the model that does contain our predictors. If the model fits the data well, then we would expect lower residuals deviance values compared to the null deviance values. The difference between the residual and null deviance values follows a chi-square distribution and can tested for statistical significance. This is accomplished with the `pchisq()` function which calculates the area under the curve for of a chi-distribution either to the left or right of a given value and degrees of freedom. The result of this command is a p-value of 0.002 (rounded) which would indicate that including our predictor "Intervention" significantly improves the fit of the model.


```r
#difference between model deviance statistics
chi_model <- eel_model.1$null.deviance - eel_model.1$deviance

#difference between model degrees of freedom
chidf <- eel_model.1$df.null - eel_model.1$df.residual

#one minus probability of chi_model given df
chisq.prob <- 1 - pchisq(chi_model, chidf)

#produce an output table
Intervention.Model <- c("Chi-square statistic", "Degrees of freedom", "p-value")
Value <- c(chi_model, chidf, chisq.prob)
kable(data.frame(Intervention.Model, Value))
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> Intervention.Model </th>
   <th style="text-align:right;"> Value </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Chi-square statistic </td>
   <td style="text-align:right;"> 9.9262012 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Degrees of freedom </td>
   <td style="text-align:right;"> 1.0000000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> p-value </td>
   <td style="text-align:right;"> 0.0016294 </td>
  </tr>
</tbody>
</table>


<!-- ### Akaike Informatin Criterion (AIC) -->
<!-- The AIC for our model is measure of fit that takes into consideration the number of predictors. The higher the number of the predictors, the more the fit is penalized. The AIC is 148.16 and can be used to compare other models in which additional predictors are included. -->

### Coefficients
The estimate or b-value for the model is 1.23. In linear regression, the coefficients reprsent as the change in the outcome variable that can be expected for a unit increase in a predictor variable. In logistic regression, the coefficients represent the change in the logit of the outcome variable that can be expected for a unit change in the predictor variable. The logit of the coutome variable corresponds to the natural logarithm  of the odds of the outcome variable occurring. The z-values are normally distributed and can be tested for significance. In our example, Intervention is statistically significant which means that the predictor is making a significant contribution to the prediction of the outcome variable.

### Odds ratio
In the case of where the predictor variables are dichotomous, the odds ratio, defined as the exponential (`exp()` function) of the coefficients, has a straightforward interpretation. A value greater than one indicates that as the predictor increases, the odds, which is not to be confused with probability, of the outcome occurring increases. A value less than one indicates that as the predictor increases, the odds of the outcome occurring decreases. The odds ratio with our example is 3.417 and is greater than 1. This means that receiving an intervention increases the odds of cured outcome.

```r
# Odds ratio
kable(exp(eel_model.1$coefficients))
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> x </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> (Intercept) </td>
   <td style="text-align:right;"> 0.750000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> InterventionIntervention </td>
   <td style="text-align:right;"> 3.416667 </td>
  </tr>
</tbody>
</table>


### Confidence intervals of the odds ratio
To obtain confidence intervals for the odds ratio of the model, simply pass the model to the `confint()` function within the `exp()` function. Our lower and upper bounds of the confidence interval does not contain 1. In fact, both of these values are greater than 1, which indicate that it is likely that the directon of receiving the experimental treatment results in increased odds of getting cured.

```r
kable(exp(confint(eel_model.1)))
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> 2.5 % </th>
   <th style="text-align:right;"> 97.5 % </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> (Intercept) </td>
   <td style="text-align:right;"> 0.4374531 </td>
   <td style="text-align:right;"> 1.268674 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> InterventionIntervention </td>
   <td style="text-align:right;"> 1.5820127 </td>
   <td style="text-align:right;"> 7.625545 </td>
  </tr>
</tbody>
</table>

<!-- Notes
Log-likelihood (LL): Predicted values are probabilities between 0 and 1. Log-likelihood is used assess fit of the model which is the sum of the predicted and actual outcomes. Analogous to residual sum of squares. Large values indicate a poor fit.
Deviance (-2LL): essentially a measure of model fit, calculated as -2LL
R: Partial correlation between the outcome variable and each of the predictor variables. Dependent on the Wald Statistic which can be inaccurate in some cases and should be interpreted with caution.
Pseudo R-squareds: Provide additional ways of assessing model fit, but have to be calculated by hand. There are several each with slightly different interpretations
  - Hosmer & Lemeshow: The proportional reduction in the absolute value of the log-likelihood measure. How much does the fit improve as a result of including a predictor?
  - Cox & Snell: Different mueasure, but does not reach it's theoretical maximum
  - Nagelkerke's: Modifies the R.cs value to address a limitation.
  - All of these values provide an idea of the significance of the model
AIC: Another way of assessing model fit that takes into consideration the number of predictors in the model.
Coefficients: Tell us the change in the logit of the outcome variable that can be expected for a one-unit increase in the predictor variable. Individual contributions of predictors. The z-statistic tells us if the coefficient is significantly different than zero. But when the coefficient is large, the standard error can become inflated, which underestimates the z-statistic and increases the likelihood of making a type II error (Which is a false negative, telling a pregnant woman she is not pregnant)
Odds ratio:  Calculated as the exponential of B. This indicates the change in odds resulting from a unit change in the predictor. But if the predictor is a categorical variable, . The odds value is NOT the same as probability, bc odds is calculated as P(event)/P(no event). The odds ratio is interpreted at a cutoff of 1. Greater than 1 means that as the predictor increases the odds of the outcome occurring increases. A value less than one indicates that as the predictor increases the odds of the outcome occurring decreases. 

In the second part of this series, we will create a model with an additional variable, duration, that will quantify the number of days that a participant presented with a problem prior to treatment.

-->


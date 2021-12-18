---
title: Multinomial Logistic Regression
author: Carlos Rodriguez
date: '2021-12-18'
slug: multinomial-regression
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-12-18T10:41:37-07:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 90
draft: true
---

Multinomial regression is an extension of logistic regression. In logistic regression, the dependent or outcome variable can take on one of two categorical values such as whether or not someone survived a stay at a hospital. In cases where the dependent variable falls into more than two categorical variables, then multinomial regression is one possible analysis to test the relationship between your dependent variable and predictor variables. For this guide, we will use multinomial regression using the `nnet` package. We will also use a sample data set in which the researchers determined the effect of pickup lines on whether or not that person got a phone number, went home with the person, or walked away.


```r
library(tidyverse)
library(nnet)
library(car)
library(kableExtra)
```

### Load Data

```r
chatData<-read.delim("Chat-Up Lines.dat", header = TRUE)
#chatData$Gender<-relevel(chatData$Gender, ref = 2)
```

### Inspect Data

```r
kable(head(chatData))
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> Success </th>
   <th style="text-align:right;"> Funny </th>
   <th style="text-align:right;"> Sex </th>
   <th style="text-align:right;"> Good_Mate </th>
   <th style="text-align:left;"> Gender </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Get Phone Number </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 7 </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:left;"> Male </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Go Home with Person </td>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:right;"> 7 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:left;"> Male </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Get Phone Number </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:left;"> Male </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Go Home with Person </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 7 </td>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:left;"> Male </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Get Phone Number </td>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:left;"> Male </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Get Phone Number </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 7 </td>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:left;"> Male </td>
  </tr>
</tbody>
</table>

```r
is.factor(chatData$Success)
```

```
## [1] FALSE
```

```r
is.factor(chatData$Gender)
```

```
## [1] FALSE
```

### Prepare Data

```r
chatData$Gender <- as.factor(chatData$Gender)
chatData$Success <- as.factor(chatData$Success)

chatData$Success <- relevel(chatData$Success, "No response/Walk Off") # Set Reference level to no response...
chatData$Gender <- relevel(chatData$Gender, "Male") # Set Male to reference
```


### Model the data

```r
chatModel2 <- multinom(Success ~ Good_Mate + Funny + Gender + Sex + Gender:Sex +  Funny:Gender, data = chatData)
```

```
## # weights:  24 (14 variable)
## initial  value 1120.584534 
## iter  10 value 953.525508
## iter  20 value 868.736917
## final  value 868.736497 
## converged
```

```r
summary(chatModel2)
```

```
## Call:
## multinom(formula = Success ~ Good_Mate + Funny + Gender + Sex + 
##     Gender:Sex + Funny:Gender, data = chatData)
## 
## Coefficients:
##                     (Intercept) Good_Mate     Funny GenderFemale       Sex
## Get Phone Number      -1.783070 0.1318371 0.1393846    -1.646198 0.2762074
## Go Home with Person   -4.286323 0.1300003 0.3184571    -5.626258 0.4172888
##                     GenderFemale:Sex Funny:GenderFemale
## Get Phone Number          -0.3483306          0.4924484
## Go Home with Person       -0.4766576          1.1724111
## 
## Std. Errors:
##                     (Intercept)  Good_Mate     Funny GenderFemale        Sex
## Get Phone Number      0.6697696 0.05372607 0.1101253    0.7962448 0.08919698
## Go Home with Person   0.9413983 0.08352059 0.1253016    1.3285828 0.12208319
##                     GenderFemale:Sex Funny:GenderFemale
## Get Phone Number           0.1058751          0.1399918
## Go Home with Person        0.1634337          0.1992393
## 
## Residual Deviance: 1737.473 
## AIC: 1765.473
```

### Test for multicolinearity

```r
chatModel <- glm(Success ~ Funny + Good_Mate + Sex + Gender, data = chatData, family = binomial())
vif(chatModel)
```

```
##     Funny Good_Mate       Sex    Gender 
##  1.580214  1.018981  1.006221  1.558624
```

```r
1/vif(chatModel)
```

```
##     Funny Good_Mate       Sex    Gender 
## 0.6328256 0.9813729 0.9938173 0.6415915
```

**Correlations**

```r
kable(cor(chatData[, c("Funny", "Good_Mate", "Sex")]))
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> Funny </th>
   <th style="text-align:right;"> Good_Mate </th>
   <th style="text-align:right;"> Sex </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Funny </td>
   <td style="text-align:right;"> 1.0000000 </td>
   <td style="text-align:right;"> 0.1632098 </td>
   <td style="text-align:right;"> 0.1156084 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Good_Mate </td>
   <td style="text-align:right;"> 0.1632098 </td>
   <td style="text-align:right;"> 1.0000000 </td>
   <td style="text-align:right;"> 0.0379461 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Sex </td>
   <td style="text-align:right;"> 0.1156084 </td>
   <td style="text-align:right;"> 0.0379461 </td>
   <td style="text-align:right;"> 1.0000000 </td>
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

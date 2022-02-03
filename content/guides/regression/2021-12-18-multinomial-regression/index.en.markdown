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
draft: false
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
Before building our multinomial logistic regression model, we will need to check that our predictor variables are factored. Simply pass each column to the `is.factor()` function to check.

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
Since both of our predictor variables are not factored, use the `as.factor()` function to convert them and save into the chatData data frame. Next, we will want to relevel our predictor varibales. The `relevel()` function takes two inputs, the column of the factor that needs to be releveled and the reference variable. For this example we want to set "No response/Walk Off" as the reference for Success, and "Male" as the reference for Gender. One way to think about multinomial regression is view it as a series of binary logistic regression models where one variable is compared to all others. It is the reference level that is compared with all other levels of that variable in multinomial regression.  

```r
chatData$Gender <- as.factor(chatData$Gender)
chatData$Success <- as.factor(chatData$Success)

chatData$Success <- relevel(chatData$Success, "No response/Walk Off") # Set Reference level to no response...
chatData$Gender <- relevel(chatData$Gender, "Male") # Set Male to reference
```

### Model the data
Once out data is prepared, we can move onto building a multinomial regression model. For this example, we want to know if the variables Good_Mate, Funny, Gender, Sex, the interaction between Gender and Sex, and the interaction between Funny and Gender predict the outcome, Success.

```r
chatModel2 <- multinom(Success ~ Good_Mate + Funny + Gender + Sex + Gender:Sex + Funny:Gender, data = chatData)
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

### Walkthrough of `multinom()` and `summary()` output
  * The first chunk of output from the `multinom()` function displays a series of values from iterations of the model. The piece of information to focus on here is to ensure that the model converged. If your model fails to converge, consider variables that have been entered into the model.Perhaps ther are a small number of certain combinations of categorical predictor variables. Some predictors may need to be omitted for the model to converge. 

  * The interpretation of the output from the `summay()` of the multinom model is similar to that of that logistic regression. We see the "**Call:**" which is reproduction of the `multinom()` function used to create the model. 
  
  * Next, we see the "**Coefficients:**" wich quantify the relationship between the predictor variables and the outcome variables. Notice that there are only two outcome variables displayed, "Get Phone Number" and "Go Home with Person." This is because we had set "No Response/Walk Off" as the reference level. Thus as an example, the coefficient for Good_Mate and Get Phone Number is 0.1318 which indicates that for each unit increase in Good_Mate, we can expect as 0.1318 increase in the logit of the outcome variable when the outcomes are "Get Phone Number" and "No response/Walk Off." Similarly, the coefficient for Good_Mate and Go Home with Person is 0.13, which indicates that for each unit increase in Good_Mat, we can expect a 0.13 increase in the logit of the outcome variable.

  * The "**Std. Errors:**" section displays the standard errors of the coefficients. These are useful in calculating z scores and p-values since we can divide the coefficients by the standard errors to obtain z scores.

  * Finally, the output displays the "**Residual Deviance:**" and the "AIC:." The residual deviance provides information regarding fit in the model that does not contain predictors (estimating the intercept only). The AIC, or the Akaike Information Criterion, is a way to assess model fit that penalized the model according to the number of predictor variables used. These two values are helpful for comparing different multinomial regression models.

### Exponentiated coefficients
For each coefficient other than the intercept, the value represents the log odds of the outcome variables increasing for each unit increase in the predictor variable. Because it is not intuitive to interpret these values as log odds, they can be exponentiated. The exponentiated coefficients have a much more straightforward interpretation. The exponentiated coefficients represent the odds ratio which can be interpreted as either greater than or less than one. Values greater than one indicate that as the predictor increases, the odds of the outcome occuring increases. A values less than one indicates that as the predictor increases, the odds of the outcome occuring decreases. To drive this point home, the exponentiated coefficient for Get Phone Number and Good_Mate is 1.14. This values is greater than one which indicates that as the Good_Mate scores increase, the odds of getting a phone number increase relative to "No response/Walk Off."


```r
kable(exp(coef(chatModel2)))
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> (Intercept) </th>
   <th style="text-align:right;"> Good_Mate </th>
   <th style="text-align:right;"> Funny </th>
   <th style="text-align:right;"> GenderFemale </th>
   <th style="text-align:right;"> Sex </th>
   <th style="text-align:right;"> GenderFemale:Sex </th>
   <th style="text-align:right;"> Funny:GenderFemale </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Get Phone Number </td>
   <td style="text-align:right;"> 0.1681212 </td>
   <td style="text-align:right;"> 1.140922 </td>
   <td style="text-align:right;"> 1.149566 </td>
   <td style="text-align:right;"> 0.1927815 </td>
   <td style="text-align:right;"> 1.318121 </td>
   <td style="text-align:right;"> 0.7058655 </td>
   <td style="text-align:right;"> 1.636318 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Go Home with Person </td>
   <td style="text-align:right;"> 0.0137554 </td>
   <td style="text-align:right;"> 1.138829 </td>
   <td style="text-align:right;"> 1.375005 </td>
   <td style="text-align:right;"> 0.0036020 </td>
   <td style="text-align:right;"> 1.517841 </td>
   <td style="text-align:right;"> 0.6208551 </td>
   <td style="text-align:right;"> 3.229770 </td>
  </tr>
</tbody>
</table>

### Z-scores
To obtain z-scores and p-values, we will create a new data frame, z, which will take each coefficient and divide it by the standard error. Next we create a new data frame, p, which will calculate the p-value ofr each z score.


```r
z <- summary(chatModel2)$coefficients/summary(chatModel2)$standard.error
kable(z)
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> (Intercept) </th>
   <th style="text-align:right;"> Good_Mate </th>
   <th style="text-align:right;"> Funny </th>
   <th style="text-align:right;"> GenderFemale </th>
   <th style="text-align:right;"> Sex </th>
   <th style="text-align:right;"> GenderFemale:Sex </th>
   <th style="text-align:right;"> Funny:GenderFemale </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Get Phone Number </td>
   <td style="text-align:right;"> -2.662214 </td>
   <td style="text-align:right;"> 2.453875 </td>
   <td style="text-align:right;"> 1.265691 </td>
   <td style="text-align:right;"> -2.067452 </td>
   <td style="text-align:right;"> 3.096600 </td>
   <td style="text-align:right;"> -3.290013 </td>
   <td style="text-align:right;"> 3.517694 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Go Home with Person </td>
   <td style="text-align:right;"> -4.553144 </td>
   <td style="text-align:right;"> 1.556507 </td>
   <td style="text-align:right;"> 2.541525 </td>
   <td style="text-align:right;"> -4.234782 </td>
   <td style="text-align:right;"> 3.418069 </td>
   <td style="text-align:right;"> -2.916519 </td>
   <td style="text-align:right;"> 5.884435 </td>
  </tr>
</tbody>
</table>

### P-values

```r
# two-tailed z test
p <- (1 - pnorm(abs(z), 0, 1)) *2
kable(p)
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> (Intercept) </th>
   <th style="text-align:right;"> Good_Mate </th>
   <th style="text-align:right;"> Funny </th>
   <th style="text-align:right;"> GenderFemale </th>
   <th style="text-align:right;"> Sex </th>
   <th style="text-align:right;"> GenderFemale:Sex </th>
   <th style="text-align:right;"> Funny:GenderFemale </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Get Phone Number </td>
   <td style="text-align:right;"> 0.0077628 </td>
   <td style="text-align:right;"> 0.0141326 </td>
   <td style="text-align:right;"> 0.2056239 </td>
   <td style="text-align:right;"> 0.0386916 </td>
   <td style="text-align:right;"> 0.0019575 </td>
   <td style="text-align:right;"> 0.0010018 </td>
   <td style="text-align:right;"> 0.0004353 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Go Home with Person </td>
   <td style="text-align:right;"> 0.0000053 </td>
   <td style="text-align:right;"> 0.1195877 </td>
   <td style="text-align:right;"> 0.0110370 </td>
   <td style="text-align:right;"> 0.0000229 </td>
   <td style="text-align:right;"> 0.0006307 </td>
   <td style="text-align:right;"> 0.0035396 </td>
   <td style="text-align:right;"> 0.0000000 </td>
  </tr>
</tbody>
</table>

### Confidence intervals

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



<!-- # Interpretation --------------------------------------------------------------- -->
<!-- # Linear regression -->
<!-- # Used to determine the relationship between a set of predictor variables and a -->
<!-- # continuous variable. -->
<!-- #  -->
<!-- # Logistic regression -->
<!-- # Used to determine if there is a relationship between a set of predictor variables -->
<!-- # and one of two categorical variables. Such as did someone survive a hospital stay -->
<!-- # or not. -->
<!-- #  -->
<!-- # Based on the logistic function, s shaped line, that quantifies -->
<!-- # the probability of the outcome variable falling into the first or second category  -->
<!-- #  -->
<!-- # Wald test used to see if the variable is significant at predicting the outcome var -->
<!-- #  -->
<!-- # Relies on maximum likelihood to find the s shaped line -->
<!-- #  -->
<!-- # Probability is on the y axis, but we can take the log of the odds. The odd is the -->
<!-- # probability of something happening over the probability of something NOT happening -->
<!-- #  -->
<!-- # The log makes a new axis possible that ranges between inf and -inf rather than 0 -->
<!-- # and 1, and results in a straight line like linear regression. The coefficients  -->
<!-- # represent the log odds. -->
<!-- #  -->
<!-- # Coefficients: -->
<!-- # y intercept when the x is zero.  -->
<!-- # standard error is measure of variability as always -->
<!-- # Z value, coefficient/standard error, equal to Wald's test -->
<!-- # For each coefficient other than the intercept, the value represents the log odds of -->
<!-- # the outcome variables increases for each unit increase in the predictor variable  -->
<!-- #    -->
<!-- # Exponentiated Coefficients -->
<!-- # Exponentiated coefficients results in odds ratio. Odds ratios can be preferred  -->
<!-- # because the interpretation is no longer in log form. The interpretation of odds  -->
<!-- # ratio is that if the odds ratio is  -->
<!-- # greater than one, then for each unit increase in x, we can expect the odds -->
<!-- # of the outcome to increase. If the odds ratio is less than one, then we expect the odds -->
<!-- # of the outcome to decrease. If the odds ratio is 0.038, we can say that that if -->
<!-- # the predictor variable increases by 1 unit, the odds is 3.8% compared to the status -->
<!-- # when the x predictor did no increase by 1 unit. -->


<!-- # Multinomial regression -->
<!-- # Used to determine the relationship between a set of predictor variables and  -->
<!-- # three or more categorical variables. -->
<!-- # Interpretation is similar to logistic regression -->

<!-- # Notes: -->
<!-- # Results from mlogit and nnet on sample data are within rounding error of -->
<!-- # eachother when testing with the DSUR data, suggesting both functions provide  -->
<!-- # reasonably overlapping results. -->


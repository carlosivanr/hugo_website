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

Multinomial regression can be thought of as an extension of logistic regression. In logistic regression, the dependent or outcome variable can take on one of two categorical values such as whether or not someone survived a stay at a hospital. In cases where the dependent variable falls into more than two categorical variables, multinomial regression is one possible analysis option. For this guide, we will use multinomial regression using the `nnet` package. We will also use a sample data set in which researchers determined the effect of pickup lines on whether or not that person 1) got a phone number, 2) went home with the person, or 3) walked away.


```r
library(tidyverse)
library(nnet)
library(car)
library(kableExtra)
library(broom)
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
Since both of our predictor variables are not factored, we use the `as.factor()` function to convert them and save into the chatData data frame. Next, we will want to relevel our predictor variables to arrange them and set a reference level. The reference level is what is used as the main comparison variable. The `relevel()` function takes two inputs, the column of the factor that needs to be releveled and the reference level. For this example we want to set "No response/Walk Off" as the reference for Success, and "Male" as the reference for Gender. One way to think about multinomial regression is view it as a series of binary logistic regression models where one level (the reference) is compared to all others. In multinomial regression, it is the reference level that is compared with all other levels of the variable.  

```r
chatData$Gender <- as.factor(chatData$Gender)
chatData$Success <- as.factor(chatData$Success)

chatData$Success <- relevel(chatData$Success, "No response/Walk Off") # Set Reference level to no response...
chatData$Gender <- relevel(chatData$Gender, "Male") # Set Male to reference
```

### Model the data
Once our data is prepared, we can move onto building a multinomial regression model. For this example, we want to know if the variables Good_Mate, Funny, Gender, Sex, the interaction between Gender and Sex, and the interaction between Funny and Gender predict the outcome, Success.


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

The first chunk of output from the `multinom()` function displays a series of values from iterations of the model. The piece of information to focus on here is to ensure that the model converged. If your model fails to converge, consider the variables that have been entered into the model. Perhaps ther are a small number of certain combinations of categorical predictor variables. Some predictors may need to be omitted for the model to converge. 


### Model summary
Passing our newly created model to the `summary()` function produces the output to examine the model coefficients, their standard errors, and the overall fit of the model.

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

<!-- ### Walkthrough of `multinom()` and `summary()` output -->

  * The interpretation of the output from the `summay()` of the multinom model is similar to that of that logistic regression. We see the "**Call:**" which is a reproduction of the `multinom()` function used to create the model. 
  
  * Next, we see the "**Coefficients:**" which quantify the relationship between the predictor variables and the outcome variables. Notice that there are only two outcome variables displayed, "Get Phone Number" and "Go Home with Person." This is because we had set "No Response/Walk Off" as the reference level. Thus as an example, the coefficient for Good_Mate and Get Phone Number is 0.1318 which indicates that for each unit increase in Good_Mate, we can expect as 0.1318 increase in the logit of the outcome variable when the outcomes are "Get Phone Number" and "No response/Walk Off." Similarly, the coefficient for Good_Mate and Go Home with Person is 0.13, which indicates that for each unit increase in Good_Mate, we can expect a 0.13 increase in the logit (i.e. the log of the odds of an event occurring) of the outcome variable.

  * The "**Std. Errors:**" section displays the standard errors of the coefficients. These are useful in calculating z scores and p-values since we can divide the coefficients by the standard errors to obtain z scores.

  * Finally, the output displays the "**Residual Deviance:**" and the "AIC:." The residual deviance provides information regarding fit in the model that does not contain predictors (estimating the intercept only). The AIC, or the Akaike Information Criterion, is a way to assess model fit that penalizes the model according to the number of predictor variables used. These two values are helpful for comparing different multinomial regression models.


### A better way to present the output
The model summary output may look clunky on your screen and it lacks information, specifically z statistics and p values, that could help us interpret the model. The missing information can be filled in with the `tidy()` function in the broom package. Using the tidy() function on our model results in a tibble that now contains a z statistic and a p value. 


<!-- We can build off of this tibble to include additional information to help us interpret the model. -->

```r
tidymodel <- tidy(chatModel2)

tidymodel$expB <- exp(tidymodel$estimate)

#tidymodel$`2.5%` <- 
#test <- data.frame(exp(confint(chatModel2)))
kable(tidymodel)
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> y.level </th>
   <th style="text-align:left;"> term </th>
   <th style="text-align:right;"> estimate </th>
   <th style="text-align:right;"> std.error </th>
   <th style="text-align:right;"> statistic </th>
   <th style="text-align:right;"> p.value </th>
   <th style="text-align:right;"> expB </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Get Phone Number </td>
   <td style="text-align:left;"> (Intercept) </td>
   <td style="text-align:right;"> -1.7830702 </td>
   <td style="text-align:right;"> 0.6697696 </td>
   <td style="text-align:right;"> -2.662214 </td>
   <td style="text-align:right;"> 0.0077628 </td>
   <td style="text-align:right;"> 0.1681212 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Get Phone Number </td>
   <td style="text-align:left;"> Good_Mate </td>
   <td style="text-align:right;"> 0.1318371 </td>
   <td style="text-align:right;"> 0.0537261 </td>
   <td style="text-align:right;"> 2.453875 </td>
   <td style="text-align:right;"> 0.0141326 </td>
   <td style="text-align:right;"> 1.1409224 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Get Phone Number </td>
   <td style="text-align:left;"> Funny </td>
   <td style="text-align:right;"> 0.1393846 </td>
   <td style="text-align:right;"> 0.1101253 </td>
   <td style="text-align:right;"> 1.265691 </td>
   <td style="text-align:right;"> 0.2056239 </td>
   <td style="text-align:right;"> 1.1495661 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Get Phone Number </td>
   <td style="text-align:left;"> GenderFemale </td>
   <td style="text-align:right;"> -1.6461979 </td>
   <td style="text-align:right;"> 0.7962448 </td>
   <td style="text-align:right;"> -2.067452 </td>
   <td style="text-align:right;"> 0.0386916 </td>
   <td style="text-align:right;"> 0.1927815 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Get Phone Number </td>
   <td style="text-align:left;"> Sex </td>
   <td style="text-align:right;"> 0.2762074 </td>
   <td style="text-align:right;"> 0.0891970 </td>
   <td style="text-align:right;"> 3.096600 </td>
   <td style="text-align:right;"> 0.0019575 </td>
   <td style="text-align:right;"> 1.3181212 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Get Phone Number </td>
   <td style="text-align:left;"> GenderFemale:Sex </td>
   <td style="text-align:right;"> -0.3483306 </td>
   <td style="text-align:right;"> 0.1058751 </td>
   <td style="text-align:right;"> -3.290013 </td>
   <td style="text-align:right;"> 0.0010018 </td>
   <td style="text-align:right;"> 0.7058655 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Get Phone Number </td>
   <td style="text-align:left;"> Funny:GenderFemale </td>
   <td style="text-align:right;"> 0.4924484 </td>
   <td style="text-align:right;"> 0.1399918 </td>
   <td style="text-align:right;"> 3.517694 </td>
   <td style="text-align:right;"> 0.0004353 </td>
   <td style="text-align:right;"> 1.6363176 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Go Home with Person </td>
   <td style="text-align:left;"> (Intercept) </td>
   <td style="text-align:right;"> -4.2863226 </td>
   <td style="text-align:right;"> 0.9413983 </td>
   <td style="text-align:right;"> -4.553144 </td>
   <td style="text-align:right;"> 0.0000053 </td>
   <td style="text-align:right;"> 0.0137554 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Go Home with Person </td>
   <td style="text-align:left;"> Good_Mate </td>
   <td style="text-align:right;"> 0.1300003 </td>
   <td style="text-align:right;"> 0.0835206 </td>
   <td style="text-align:right;"> 1.556507 </td>
   <td style="text-align:right;"> 0.1195877 </td>
   <td style="text-align:right;"> 1.1388288 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Go Home with Person </td>
   <td style="text-align:left;"> Funny </td>
   <td style="text-align:right;"> 0.3184571 </td>
   <td style="text-align:right;"> 0.1253016 </td>
   <td style="text-align:right;"> 2.541525 </td>
   <td style="text-align:right;"> 0.0110370 </td>
   <td style="text-align:right;"> 1.3750046 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Go Home with Person </td>
   <td style="text-align:left;"> GenderFemale </td>
   <td style="text-align:right;"> -5.6262579 </td>
   <td style="text-align:right;"> 1.3285828 </td>
   <td style="text-align:right;"> -4.234782 </td>
   <td style="text-align:right;"> 0.0000229 </td>
   <td style="text-align:right;"> 0.0036020 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Go Home with Person </td>
   <td style="text-align:left;"> Sex </td>
   <td style="text-align:right;"> 0.4172888 </td>
   <td style="text-align:right;"> 0.1220832 </td>
   <td style="text-align:right;"> 3.418069 </td>
   <td style="text-align:right;"> 0.0006307 </td>
   <td style="text-align:right;"> 1.5178408 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Go Home with Person </td>
   <td style="text-align:left;"> GenderFemale:Sex </td>
   <td style="text-align:right;"> -0.4766576 </td>
   <td style="text-align:right;"> 0.1634337 </td>
   <td style="text-align:right;"> -2.916519 </td>
   <td style="text-align:right;"> 0.0035396 </td>
   <td style="text-align:right;"> 0.6208551 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Go Home with Person </td>
   <td style="text-align:left;"> Funny:GenderFemale </td>
   <td style="text-align:right;"> 1.1724111 </td>
   <td style="text-align:right;"> 0.1992393 </td>
   <td style="text-align:right;"> 5.884435 </td>
   <td style="text-align:right;"> 0.0000000 </td>
   <td style="text-align:right;"> 3.2297704 </td>
  </tr>
</tbody>
</table>

  
  * For each coefficient other than the intercept, the values represents the log odds of the outcome variables increasing for each unit increase in the predictor variable. Because it is not intuitive to interpret the log odds, they can be exponentiated. The exponentiated coefficients have a much more straightforward interpretation. The exponentiated coefficients represent the odds ratio which can be interpreted as either greater than or less than one. Values greater than one indicate that as the predictor increases, the odds of the outcome occurring increases. A value less than one indicates that as the predictor increases, the odds of the outcome occurring decreases. To drive this point home, the exponentiated coefficient for Get Phone Number and Good_Mate is 1.14. This values is greater than one which indicates that as the Good_Mate scores increase, the odds of getting a phone number increases relative to "No response/Walk Off." To be more specific, the odds of getting a phone number increases by 14% for each unit increase in the Good_Mate score.


<!-- Notes: These chunks here were removed. The idea was to use Andy Fields data set but explore how to do multinomial regression with a different package other than what was in his text. I also wanted to incorporate newer functions such as those from the broom package. The following chunks were from a previous version in which you had to get Z-scores and pvalues manually, whereas the new broom package automatically does that for multinomial regression. -->

<!-- ```{r} -->
<!-- kable(exp(coef(chatModel2))) -->
<!-- ``` -->

<!-- ### Z-scores -->
<!-- To obtain z-scores and p-values, we will create a new data frame, z, which will take each coefficient and divide it by the standard error. Next we create a new data frame, p, which will calculate the p-value of each z score. -->

<!-- ```{r} -->
<!-- z <- summary(chatModel2)$coefficients/summary(chatModel2)$standard.error -->
<!-- kable(z) -->
<!-- ``` -->

<!-- ### P-values -->
<!-- ```{r} -->
<!-- # two-tailed z test -->
<!-- p <- (1 - pnorm(abs(z), 0, 1)) *2 -->
<!-- kable(p) -->
<!-- ``` -->

### Confidence intervals

```r
kable(exp(confint(chatModel2)))
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> 2.5 %.Get Phone Number </th>
   <th style="text-align:right;"> 97.5 %.Get Phone Number </th>
   <th style="text-align:right;"> 2.5 %.Go Home with Person </th>
   <th style="text-align:right;"> 97.5 %.Go Home with Person </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> (Intercept) </td>
   <td style="text-align:right;"> 0.0452391 </td>
   <td style="text-align:right;"> 0.6247861 </td>
   <td style="text-align:right;"> 0.0021735 </td>
   <td style="text-align:right;"> 0.0870550 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Good_Mate </td>
   <td style="text-align:right;"> 1.0268911 </td>
   <td style="text-align:right;"> 1.2676163 </td>
   <td style="text-align:right;"> 0.9668644 </td>
   <td style="text-align:right;"> 1.3413783 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Funny </td>
   <td style="text-align:right;"> 0.9263927 </td>
   <td style="text-align:right;"> 1.4265035 </td>
   <td style="text-align:right;"> 1.0755912 </td>
   <td style="text-align:right;"> 1.7577659 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> GenderFemale </td>
   <td style="text-align:right;"> 0.0404856 </td>
   <td style="text-align:right;"> 0.9179735 </td>
   <td style="text-align:right;"> 0.0002665 </td>
   <td style="text-align:right;"> 0.0486899 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Sex </td>
   <td style="text-align:right;"> 1.1067021 </td>
   <td style="text-align:right;"> 1.5699287 </td>
   <td style="text-align:right;"> 1.1948375 </td>
   <td style="text-align:right;"> 1.9281625 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> GenderFemale:Sex </td>
   <td style="text-align:right;"> 0.5735891 </td>
   <td style="text-align:right;"> 0.8686465 </td>
   <td style="text-align:right;"> 0.4506872 </td>
   <td style="text-align:right;"> 0.8552740 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Funny:GenderFemale </td>
   <td style="text-align:right;"> 1.2436734 </td>
   <td style="text-align:right;"> 2.1529247 </td>
   <td style="text-align:right;"> 2.1856409 </td>
   <td style="text-align:right;"> 4.7727039 </td>
  </tr>
</tbody>
</table>

### Test for multicolinearity
In a similar vein to linear regression, one of the assumptions of multinomial logistic regression is that the predictor variables are uncorrelated. One way to test this is to build a generalized linear model with the `glm()` function, and then using the `vif()` function from the car package. The `vif()` function determines the variance inflation factor for all of the predictor variables. VIF values of 10 or more are problematic and indicate that we have correlated predictor variables. The output of the VIF values all indicate that our variables are not highly correlated. In addition, we can inspect the tolerance statistics by calculating 1 divided by the vif. Anything less than 0.2 is a potential problem and anything less than 0.1 is a serious problem. Based off of our output, we have no reason to believe that we have encountered multicolinearity.



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
An additional way of testing for multicolinearity is to calculate the Pearson pairwise correlations for the predictor variables. Again, we notice small correlation values between 0.03 and 0.16 which are not of concern here.

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


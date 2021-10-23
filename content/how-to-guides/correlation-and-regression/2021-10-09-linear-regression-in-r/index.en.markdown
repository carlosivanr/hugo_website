---
title: Linear Regression Pt. 1 - Simple Linear Regression
author: Carlos Rodriguez
date: '2021-10-09'
slug: linear-regression-in-r
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-10-09T20:24:33-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
draft: false
type: book
weight: 15
---

This guide is the first part in a series on regression. I will walk through how to build a simple linear regression model with one outcome variable and one predictor variable. The dataset for this guide comes from Field, Miles, and Field's ["Discovering Statistics Using R"](https://www.discoveringstatistics.com/books/discovering-statistics-using-r/). The Supermodels dataset contains salary per working day, amount of experience in years, age, and a measure of beauty as determined by a panel of judges. Our job is to help determine what are the factors that best predict salary. We will start out with simple linear regression model before moving on to more complex models in subsequent guides.

### Load packages

```r
library(tidyverse)  # for data importing and visualization
library(broom)      # for linear model
library(kableExtra) # for displaying tables
```

### Load data

```r
data <- read.table(file = "Supermodel.dat", header = TRUE)
kable(
  head(data)
  )
```

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> SALARY </th>
   <th style="text-align:right;"> AGE </th>
   <th style="text-align:right;"> YEARS </th>
   <th style="text-align:right;"> BEAUTY </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 0.3703971 </td>
   <td style="text-align:right;"> 16.66700 </td>
   <td style="text-align:right;"> 3.148455 </td>
   <td style="text-align:right;"> 78.25149 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 53.7247893 </td>
   <td style="text-align:right;"> 20.34707 </td>
   <td style="text-align:right;"> 5.506886 </td>
   <td style="text-align:right;"> 68.56999 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1.4601591 </td>
   <td style="text-align:right;"> 18.20307 </td>
   <td style="text-align:right;"> 5.330748 </td>
   <td style="text-align:right;"> 75.04376 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 0.0243340 </td>
   <td style="text-align:right;"> 15.35626 </td>
   <td style="text-align:right;"> 3.840088 </td>
   <td style="text-align:right;"> 65.14253 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 95.3380701 </td>
   <td style="text-align:right;"> 24.17183 </td>
   <td style="text-align:right;"> 8.532050 </td>
   <td style="text-align:right;"> 71.77039 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 14.6354779 </td>
   <td style="text-align:right;"> 18.26022 </td>
   <td style="text-align:right;"> 4.393159 </td>
   <td style="text-align:right;"> 78.05224 </td>
  </tr>
</tbody>
</table>

### Visualize data
Let's begin by visualizing the data with one predictor varialbe, AGE. We'll turn to a ggplot call to plot SALARY as a function of AGE. We can see that there is moderate and positive relationship between age and salary. Salary tends to increase with age.


```r
ggplot(data, aes(x = AGE, y = SALARY)) +
  geom_point(alpha = 0.7, color = "#1565c0") +
  geom_smooth(method = "lm", se = TRUE, color = "#1565c0") +
  theme_minimal() +
  theme(axis.line = element_line(color = "grey70"))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-3-1.png" width="672" />


### Model the data
For our first model we will try to predict salary from age. To build a regression model in R, we will use the `lm()` function. The `lm()` function needs two main arguments. First, we enter a formula that is in the form of outcome ~ predictor (read outcome by predictor)[^1]. Then, we specify the dataframe to use. Finally, we can obtain output from our model with the `summary()` function.

```r
model <- lm(SALARY ~ AGE, data = data)
summary(model)
```

```
## 
## Call:
## lm(formula = SALARY ~ AGE, data = data)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -23.551  -8.291  -3.895   4.936  67.946 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) -36.1820     7.3145  -4.947 1.46e-06 ***
## AGE           2.6301     0.4013   6.555 3.65e-10 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 14.74 on 229 degrees of freedom
## Multiple R-squared:  0.158,	Adjusted R-squared:  0.1543 
## F-statistic: 42.96 on 1 and 229 DF,  p-value: 3.653e-10
```

### Walkthrough of the output
The output of the `summary()` can be thought of 4 chunks of related information. 
  * Beginning with the top, the first section is the "**Call:**," which is simply a reproduction of the `lm()` function used to create the model. 
  * The next section, "**Residuals:**" contains information about the differences between the model predictions along the model and the actual values. 
  * In the third, "**Coefficients:**," section we find information regarding the model parameters which can also be referred to as beta coefficients. Each model will have an intercept, which isn't really a predictor variable, but can be interpreted as the predicted outcome when the predictor = 0. In our case, the predicted salary is -36.18 when age = 0. This doesn't make a ton of sense since we are unlikely to encounter Supermodels at age 0. Again, the data are fabricated and are simply analyzed here for demonstrative purposes. The AGE coefficient is interpreted as what the model predicts as an increase in the outcome variable for every unit increase in age. In other words, we would predict salary to increase by 2.63 units every year. In the same section we find a standard error, t-value, and p-value. The t-values are the results of a t-test that the coeffient is not zero and the Pr(>|t|) column displays the p-value of this test statistic. This section is concluded with a key of the significance markers. 
  * Finally, the last section displays information about the **overall fit** of the model. In this example, the Multiple R-squared value is interpreted as 15.8% of the variance in SALARY can be accounted for by AGE. The last line of this section displays the results of an analysis of variance and can be interpreted as the regression model resulting in a significantly better prediction of SALARY than using the mean salary.

### Tidy models
With the `tidy()` function in the broom package. We can "clean up" the output of the `summary()` functions by displaying it without all of the information.

```r
kable(
  tidy(model)
)
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> term </th>
   <th style="text-align:right;"> estimate </th>
   <th style="text-align:right;"> std.error </th>
   <th style="text-align:right;"> statistic </th>
   <th style="text-align:right;"> p.value </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> (Intercept) </td>
   <td style="text-align:right;"> -36.181959 </td>
   <td style="text-align:right;"> 7.3145048 </td>
   <td style="text-align:right;"> -4.946604 </td>
   <td style="text-align:right;"> 1.5e-06 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> AGE </td>
   <td style="text-align:right;"> 2.630101 </td>
   <td style="text-align:right;"> 0.4012607 </td>
   <td style="text-align:right;"> 6.554593 </td>
   <td style="text-align:right;"> 0.0e+00 </td>
  </tr>
</tbody>
</table>

### Make predictions with the model
Since our model is better than using the mean and age is a significant predictor of salary, we can then use the model to predict outcomes from a given predictor. We will create a new dataframe with at least one value, change its column name to match that of the dataframe that was used to generate the model, and then use the `predict()` function to get the predicted value. In this case we would expect 24.31 units of salary for a 23-year old model. I simply used a single value to illustrate the use of the `predict()` function, but an entire column vector of ages could just as easily be use to predict salary.

```r
# Predict salary at age of 23
new_data <- as.data.frame(c(23))  # Create a new data frame with a prediction
names(new_data)[1] <- "AGE"       # Change the column name to match the lm() predictor column name
predict(model, new_data)          # Predict new values 
```

```
##        1 
## 24.31036
```
### References
<div id="refs" class="references">

<div id="ref-DSUR">

Field, Andy, Jeremy Miles, and Zoe Field. 2012. *Discovering Statistics Using R*. Sage.

</div>

<div id="ref-R-tidyverse">

Wickham, Hadley. 2019. *Tidyverse: Easily Install and Load the ’Tidyverse’*. <https://CRAN.R-project.org/package=tidyverse>.

</div>

</div>

### Footnotes
[^1]: The terms outcome and predictor variables correspond to the terms response and explanatory variables respectively.


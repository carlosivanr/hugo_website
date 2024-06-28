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
# bibliography: [../../../../packages.bib]
# nocite: |
#   @R-broom
---

This guide is the first part in a series on linear regression. I will walk through how to build a simple linear regression model with one outcome variable and one predictor variable. The dataset for this guide comes from Field, Miles, and Field's ["Discovering Statistics Using R"](https://www.discoveringstatistics.com/books/discovering-statistics-using-r/). The Supermodels dataset contains salary per working day, amount of experience in years, age, and a measure of beauty as determined by a panel of judges. Our task is to help determine what are the factors that best predict salary. We will begin with a simple linear regression model consisting of one outcome (dependent variable) and one numerical predictor (variable) before moving on to more complex models with more predictor variables and of different types like categorical predictors in subsequent guides.

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



|     SALARY|      AGE|    YEARS|   BEAUTY|
|----------:|--------:|--------:|--------:|
|  0.3703971| 16.66700| 3.148455| 78.25149|
| 53.7247893| 20.34707| 5.506886| 68.56999|
|  1.4601591| 18.20307| 5.330748| 75.04376|
|  0.0243340| 15.35626| 3.840088| 65.14253|
| 95.3380701| 24.17183| 8.532050| 71.77039|
| 14.6354779| 18.26022| 4.393159| 78.05224|

### Visualize data
Let's begin by visualizing the data with one predictor variable, AGE. We'll turn to a ggplot call to plot SALARY as a function of AGE. We can see that there is a moderate and positive relationship between age and salary. Salary tends to increase with age. In the call below, we first pass the data frame as the first argument. Then we set our x and y variables to AGE and SALARY, respectively in the `aes()` command. We then add (+) a scatterplot layer with the `geom_point()` call, and a regression line with the `geom_smooth()` call. The remaining options are simply to change some of the visual aspects of the plot.  


```r
ggplot(data, aes(x = AGE, y = SALARY)) +
  geom_point(alpha = 0.7, color = "#1565c0") +
  geom_smooth(method = "lm", se = F, color = "#1565c0") +
  theme_minimal() +
  theme(axis.line = element_line(color = "grey70"))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-3-1.png" width="672" />



### Model the data
To build a regression model in R, we will use the `lm()` function which stands for linear model. The `lm()` function needs two main arguments. First, we enter a formula that is in the form of outcome ~ predictor (which I read as outcome predicted by predictor)[^1]. Then, we specify the data frame to use. Finally, we can obtain output from our model with the `summary()` function.

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
The output of the `summary()` is organized into 4 main sections of related information. 

1. Beginning with the top, the first section is the "**Call:**," which is simply a reproduction of the `lm()` command used to create the model. 

2. The next section, "**Residuals:**" Displays the minimum value of the residuals, the interquartile range of the residuals, and the maximum value of the residuals. The residuals are the differences between our observed values in the dependent variable and what the model predictions. One of the assumptions of linear models is that the residuals are normally distributed. Therefore we expect that the Median of the residuals is close to zero. In addition, we would expect the absolute values of the 1st and 3rd quartiles to be close which would indicate equal spread above and below the mean.

3. The "**Coefficients:**" displays information regarding the model coefficients, their standard errors, t-values, and p-values. Each model will have an intercept, which isn't really a predictor variable. In this case, it can be interpreted as the predicted outcome  when the predictor equals 0. The predicted salary is -36.18 when age equals 0. This doesn't make a ton of sense since we are unlikely to encounter someone with an age of 0 and the salary is negative and is simply a by product of the linear model. In other cases the intercept may represent the mean of the dependent variable for observations belonging to a certain group. The AGE coefficient is interpreted as what the model predicts as an increase in the outcome variable for every unit increase in AGE. In other words, we would predict salary to increase by 2.63 units for every 1 year increase in AGE. In the same section we find the standard error, t-value, and p-value for each coefficient. The t-values are the result of a t-test that the coefficient is not zero and the Pr(>|t|) column displays the p-value of this test statistic. This section is concluded with a key of the significance markers where an asterisk indicates the p-values is <= 0.05. 

4. Finally, the last section displays information about the **overall fit** of the model. The residual standard error (RSE) is the standard error of the residuals. A smaller RSE indicates that the predicted values are close to the observed values and can be thought of as an indicator fit. The Multiple R-squared value is the proportion of the variance in the dependent variable that can be attributed to the predictor variable. In this case, 15.8% of the variance in SALARY can be attributed to AGE. The last line of this section displays the results of an analysis of variance that compares a model where SALARY is predicted by AGE to a model where the mean and only the mean of salary is used as a predictor. Predicting salary from age is significantly better than using the mean of salary alone.

### Tidy models
The `summary()` function was designed to read output, but it may be helpful to obtain the output in a data frame where the values can be manipulated. The `tidy()` function in the broom package displays a portion of the output as a data frame that will have some beneficial features as we move forward.

```r
kable(
  tidy(model)
)
```



|term        |   estimate| std.error| statistic| p.value|
|:-----------|----------:|---------:|---------:|-------:|
|(Intercept) | -36.181959| 7.3145048| -4.946604| 1.5e-06|
|AGE         |   2.630101| 0.4012607|  6.554593| 0.0e+00|

### Make predictions with the model
Since our model is better than using the mean as a predictor of salary and age is a significant predictor of salary, we can then use the model to predict outcomes for data that we don't have. For example, the maximum age in the data set is 25.29. Suppose we wanted to predict the salary of someone who was 30.  To accomplish this, we first create a tibble (a type of dataframe) with at least one value and name the column match that of the predictor variable used to generate the model. Next, use the `predict()` function to get the predicted value for the new AGE value. In this case, we would expect 42.72 units of salary for a 30-year old. I simply used a single value to illustrate the use of the `predict()` function, but an entire data frame new AGE values could just as easily be used to predict multiple salaries.

```r
# Predict salary at age of 30
new_data <- tibble(AGE = 30)
predict(model, new_data)          # Predict new values 
```

```
##        1 
## 42.72106
```


### Interpretation of summary output for categorical variables
Linear models can and will often include categorical predictor variables, often in combination with other numerical predictor variables. To illustrate the output of `lm()` with categorical predictors we will create a new variable named GENDER that will take one of two values, "Male" if the salary is below the median and "Female" otherwise. 

```r
data  <- data %>% mutate(GENDER = ifelse(SALARY <= median(SALARY), "Male", "Female"))
```

We can proceed to visualize our data, except this time we will use box plots since our predictor variable is now a categorical one.

```r
ggplot(data, aes(x = GENDER, y = SALARY)) +
  geom_boxplot(color = "#1565c0") +
  theme_minimal() +
  theme(axis.line = element_line(color = "grey70"))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-9-1.png" width="672" />
We can also group our data by GENDER and then calculate the mean.

```r
data %>% 
  group_by(GENDER) %>%
  summarise(mean_SALARY = mean(SALARY))
```

```
## # A tibble: 2 × 2
##   GENDER mean_SALARY
##   <chr>        <dbl>
## 1 Female       21.2 
## 2 Male          1.53
```

Next, we can build a model where SALARY is predicted by GENDER and display the output.

```r
gender_model <- lm(SALARY ~ GENDER, data = data)

summary(gender_model)
```

```
## 
## Call:
## lm(formula = SALARY ~ GENDER, data = data)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -16.261  -5.043  -1.092   1.579  74.102 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   21.236      1.180   18.00   <2e-16 ***
## GENDERMale   -19.709      1.665  -11.84   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 12.65 on 229 degrees of freedom
## Multiple R-squared:  0.3797,	Adjusted R-squared:  0.377 
## F-statistic: 140.2 on 1 and 229 DF,  p-value: < 2.2e-16
```
When the predictor variable is categorical, the Estimate for the Intercept reflects the mean of the reference group. In this case, "Female" is the reference group because Female appears before Male in alphabetical order. This is the default behavior, but we can easily set MALE or any other variable as the reference group. The Estimate for GENDERMale is -19.71 which reflects that the mean salary of Males is 19.71 units below the mean of Females.

Here we explicitly set the order of the levels where the reference group is Male. Now the output displays the Estimate for the Intercept as the mean of the Male group and the Estimate for GENDERFemale indicates that the mean of the Female group is 19.71 units above the mean of the Male group.


```r
# Explicitly set the order of levels
data$GENDER <- factor(data$GENDER, levels = c("Male", "Female"))

gender_model <- lm(SALARY ~ GENDER, data = data)

summary(gender_model)
```

```
## 
## Call:
## lm(formula = SALARY ~ GENDER, data = data)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -16.261  -5.043  -1.092   1.579  74.102 
## 
## Coefficients:
##              Estimate Std. Error t value Pr(>|t|)    
## (Intercept)     1.527      1.174    1.30    0.195    
## GENDERFemale   19.709      1.665   11.84   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 12.65 on 229 degrees of freedom
## Multiple R-squared:  0.3797,	Adjusted R-squared:  0.377 
## F-statistic: 140.2 on 1 and 229 DF,  p-value: < 2.2e-16
```

### Relationship between two sample independent t-test and lm with a two-level categorical predictor
A linear model where a continuous and numerical variable is predicted by a two-level categorical variable is equivalent to a two sample independent t-test. Notice that the output of the `t.test()` function results in the same t-value and p-value that are shown in the model outputs for the GENDERMale and GENDERFemale models. The var.equal = TRUE argument sets the t-test to use the pooled variance which is akin to the average variance of the two groups and what is used underneath the hood of the `lm()` command.

```r
t.test(SALARY ~ GENDER, var.equal = TRUE, data = data)
```

```
## 
## 	Two Sample t-test
## 
## data:  SALARY by GENDER
## t = -11.84, df = 229, p-value < 2.2e-16
## alternative hypothesis: true difference in means between group Male and group Female is not equal to 0
## 95 percent confidence interval:
##  -22.98894 -16.42929
## sample estimates:
##   mean in group Male mean in group Female 
##             1.526557            21.235668
```
### Final note on categorical predictors
The same relationship between the two sample t-test and a linear model with one two-level categorical predictor is extended to ANOVA when the categorical predictor has three or more levels. To briefly illustrate this, we can create a categorical variable to divide the sample into 3 equal groups based on the salary. This variable will take on three values "Low", "Medium", and "High".


```r
data <- 
  data %>%
  mutate(TERT = ntile(SALARY, 3)) %>%
  mutate(TERT = if_else(TERT == 1, 'Low', if_else(TERT == 2, 'Medium', 'High'))) %>%
  mutate(TERT = factor(TERT, levels = c("Low", "Medium", "High")))
```


Once again, we can display our data as a set of box plots.

```r
ggplot(data, aes(x = TERT, y = SALARY)) +
  geom_boxplot(color = "#1565c0") +
  theme_minimal() +
  theme(axis.line = element_line(color = "grey70"))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-15-1.png" width="672" />
Next we can create a linear model, where SALARY is predicted by TERT.

```r
tert_model <- lm(SALARY ~ TERT, data = data)

summary(tert_model)
```

```
## 
## Call:
## lm(formula = SALARY ~ TERT, data = data)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -16.409  -2.466  -0.429   0.888  67.249 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   0.6105     1.2126   0.503  0.61513    
## TERTMedium    4.7052     1.7149   2.744  0.00656 ** 
## TERTHigh     27.4786     1.7149  16.024  < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 10.64 on 228 degrees of freedom
## Multiple R-squared:  0.563,	Adjusted R-squared:  0.5592 
## F-statistic: 146.9 on 2 and 228 DF,  p-value: < 2.2e-16
```
In the output of the `lm()` command the F-statistic, degrees of freedom and p-value would correspond to the omnibus test for the effect of TERT in a one-way ANOVA. 

```r
summary(car::Anova(tert_model,type = 2))
```

```
##      Sum Sq            Df           F value          Pr(>F) 
##  Min.   :25815   Min.   :  2.0   Min.   :146.9   Min.   :0  
##  1st Qu.:27676   1st Qu.: 58.5   1st Qu.:146.9   1st Qu.:0  
##  Median :29537   Median :115.0   Median :146.9   Median :0  
##  Mean   :29537   Mean   :115.0   Mean   :146.9   Mean   :0  
##  3rd Qu.:31399   3rd Qu.:171.5   3rd Qu.:146.9   3rd Qu.:0  
##  Max.   :33260   Max.   :228.0   Max.   :146.9   Max.   :0  
##                                  NA's   :1       NA's   :1
```

The multiple R-squared value is equivalent to the measure of effect partial eta squared which is often used in ANOVA. Partial eta squared is defined as SSeffect / (SSeffect + SSerror) where SS refers to Sum of Squares and is provided in the output above.


```r
33260/(33260 +25815)
```

```
## [1] 0.5630131
```

The results of the pairwise t-tests also coincide with the values provided for the TERTMedium and TERTHigh values. In practicem these would likely need to be corrected for multiple comparisons and are shown here to illustrate some of the similarities between a linear model specified with `lm()` and an ANOVA.

```r
pairwise.t.test(data$SALARY, data$TERT, p.adj = "none", pool.sd = TRUE)
```

```
## 
## 	Pairwise comparisons using t tests with pooled SD 
## 
## data:  data$SALARY and data$TERT 
## 
##        Low    Medium
## Medium 0.0066 -     
## High   <2e-16 <2e-16
## 
## P value adjustment method: none
```


```r
data %>% rstatix::pairwise_t_test(SALARY ~ TERT, 
                  p.adjust.method = "none") 
```


```r
rstatix::tukey_hsd(data, SALARY ~ TERT) 
```


### References
<div id="refs" class="references">

<div id="ref-DSUR">

Field, Andy, Jeremy Miles, and Zoe Field. 2012. *Discovering Statistics Using R*. Sage.

</div>

<div id="ref-R-tidyverse">

Wickham, Hadley. 2019. *Tidyverse: Easily Install and Load the ’Tidyverse’*. <https://CRAN.R-project.org/package=tidyverse>.

</div>

<div id="ref-R-broom">

Robinson, David, Alex Hayes, and Simon Couch. 2021. *Broom: Convert Statistical Objects into Tidy Tibbles*. <https://CRAN.R-project.org/package=broom>.

</div>

</div>

[^1]: The terms outcome and predictor variables correspond to the terms response and explanatory variables respectively.


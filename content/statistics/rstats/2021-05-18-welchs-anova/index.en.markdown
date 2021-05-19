---
title: Welch's one-way ANOVA
author: Carlos Rodriguez
date: '2021-05-18'
slug: welchs-anova
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-05-18T21:14:42-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
draft: true
type: book
weight: 15
---

ANOVA is a commonly used statistical technique to compare means among two or more groups. The primary assumptions of ANOVA are independence between groups, normally distributed data, and homogeneity of variance. When the homogeneity of variance assumption is violated, a Welch's ANOVA can be conducted instead. The omnibus test of a Welch's ANOVA can then be followed by Games-Howell post-hoc tests. One limitation for the Welch's ANOVA is that it is restricted to data with only one explanatory factor (i.e. one-way designs). This guide covers how to conduct a Welch's ANOVA and the appropriate post-hoc tests with the jmv and rstatix packages. All four demos will use the same example data set as the Fisher's one-way ANOVA.


### The data set
For this module we will use the data from the Chapter 3, Exercise 9 in the AMCP package. In this exercise, a psychologist assigned 12 subjects to one of 4 different psychological treatments. These treatments consisted of rational-emotive, psychoanalytic, client-centered, and behavioral therapies. The 4 different treatments were used to investigate which therapy is more effective at reducing phobia scores.

For these data, Group represents the type of therapy the participant was randomly assigned to. Scores represent the score from a post-therapy fear scale where higher numbers indicate higher levels of phobia. Finally, each of the 12 rows represent each subject.

```r
library(AMCP)

# Load the data
data(C3E9)

# Display part of the data
head(C3E9)
```

```
##   Group Scores
## 1     1      2
## 2     1      4
## 3     1      6
## 4     2     10
## 5     2     12
## 6     2     14
```

<!-- -----------------------TABS---------------------------------- -->
{{< tabs tabTotal="2" tabID="1" tabName1="Welch's jmv" tabName2="Welch's rstatix"  >}}

<!-- -----------------------Tab 1---------------------------------- -->
{{< tab tabNum="1" >}}
The following code chunk demonstrates how to code a Welch's ANOVA in R with the jmv package. The jmv approach uses the exact same function as the Fisher's approach in one-way ANOVA and can take many of the same options to produce plots and conduct tests of normality and equality of variance (homogeneity of variance or homoscedasticity) with the `norm = TRUE` and `eqv = TRUE` options. Games-Howell post-hoc tests are produced by setting `phMethod = 'gamesHowell'`.

```r
library(jmv)
# Conduct Welch's ANOVA
anovaOneW(formula = Scores ~ Group,
          data = C3E9,
          welchs = TRUE,
          norm = TRUE,
          eqv = TRUE,
          phMethod = 'gamesHowell'
          )
```

```
## 
##  ONE-WAY ANOVA
## 
##  One-Way ANOVA (Welch's)                                
##  ────────────────────────────────────────────────────── 
##              F           df1    df2         p           
##  ────────────────────────────────────────────────────── 
##    Scores    7.692308      3    4.444444    0.0319410   
##  ────────────────────────────────────────────────────── 
## 
## 
##  ASSUMPTION CHECKS
## 
##  Normality Test (Shapiro-Wilk)        
##  ──────────────────────────────────── 
##              W            p           
##  ──────────────────────────────────── 
##    Scores    0.8107880    0.0124591   
##  ──────────────────────────────────── 
##    Note. A low p-value suggests a
##    violation of the assumption of
##    normality
## 
## 
##  Homogeneity of Variances Test (Levene's)              
##  ───────────────────────────────────────────────────── 
##              F               df1    df2    p           
##  ───────────────────────────────────────────────────── 
##    Scores    6.933348e-33      3      8    1.0000000   
##  ───────────────────────────────────────────────────── 
## 
## 
##  POST HOC TESTS
## 
##  Games-Howell Post-Hoc Test – Scores                                          
##  ──────────────────────────────────────────────────────────────────────────── 
##                            1            2            3            4           
##  ──────────────────────────────────────────────────────────────────────────── 
##    1    Mean difference            —    -8.000000    -2.000000    -6.000000   
##         p-value                    —    0.0270378    0.6456622    0.0689900   
##                                                                               
##    2    Mean difference                         —     6.000000     2.000000   
##         p-value                                 —    0.0689900    0.6456622   
##                                                                               
##    3    Mean difference                                      —    -4.000000   
##         p-value                                              —    0.2086312   
##                                                                               
##    4    Mean difference                                                   —   
##         p-value                                                           —   
##  ────────────────────────────────────────────────────────────────────────────
```
{{< /tab >}}

<!-- -----------------------Tab 2---------------------------------- -->
{{< tab tabNum="2" >}}
In rstatix, the `anova_test()` function can analyse several types of between and within subjects ANOVA designs. However, to conduct a Welch's ANOVA , the `welch_anova_test()` function is required. The syntax to conduct the actual test is rather simple. In the following code chunk, I've chosen to explicitly declare `formula = Scores ~ Group` and `data = C3E9`.  However, one could just as easily specify the dataframe and formula as long as data is the first entered variable. Similarly, data and formula do not need to be explicitly specified for the `games_howell_test()` function, but hare specified in the example for consistency. 

To produce the Shapiro-Wilk's test of normality as in the jmv output, create an analysis of variance (aov) model with the base R `aov()` function. The rstatix package does contain its own `shapiro_test()` function, but it will not test normality of the residuals, only the actual values. In the guide, the `aov()` model is nested within the `residuals()` function, which is nested within the `shapiro.test()` function. This will conduct Shapiro-Wilk's test on the residuals. Finally, for the homogeneity of variance test, the rstatix `levene_test()` function specifying a formula and a dataframe does the job. Base R also can also plot the Residuals vs Fitted values and generate Q-Q (quantile-quantile) plots from an `aov()` object to examine homogeneity of variance and normality respectively.

#### Check Normality

```r
# Convert group to factor
C3E9$Group <- as.factor(C3E9$Group)

# Produce Q-Q plots
plot(aov(formula = Scores ~ Group, data = C3E9), 2)
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/norm-1.png" width="672" />

```r
# Base R Shapiro-Wilk test on residuals of the aov object
shapiro.test(residuals(aov(formula = Scores ~ Group, data = C3E9)))
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  residuals(aov(formula = Scores ~ Group, data = C3E9))
## W = 0.81079, p-value = 0.01246
```

#### Check Homogeneity of Variance

```r
library(rstatix)

# Residuals vs Fitted values plot
plot(aov(formula = Scores ~ Group, data = C3E9), 1)
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/hov-1.png" width="672" />

```r
# rstatix Levene's test for homogeneity of variance
levene_test(formula = Scores ~ Group,
            data = C3E9)
```

```
## # A tibble: 1 x 4
##     df1   df2 statistic     p
##   <int> <int>     <dbl> <dbl>
## 1     3     8  6.93e-33     1
```

#### Welch's ANOVA

```r
# Conduct Welch's ANOVA
welch_anova_test(formula = Scores ~ Group,
                 data = C3E9)
```

```
## # A tibble: 1 x 7
##   .y.        n statistic   DFn   DFd     p method     
## * <chr>  <int>     <dbl> <dbl> <dbl> <dbl> <chr>      
## 1 Scores    12      7.69     3  4.44 0.032 Welch ANOVA
```

#### Post-hoc Tests


```r
# Games-Howell post-hoc tests
games_howell_test(formula = Scores ~ Group,
                  data = C3E9, 
                  conf.level = 0.95, 
                  detailed = FALSE)
```

```
## # A tibble: 6 x 8
##   .y.    group1 group2 estimate conf.low conf.high p.adj p.adj.signif
## * <chr>  <chr>  <chr>     <dbl>    <dbl>     <dbl> <dbl> <chr>       
## 1 Scores 1      2             8    1.35     14.6   0.027 *           
## 2 Scores 1      3             2   -4.65      8.65  0.646 ns          
## 3 Scores 1      4             6   -0.648    12.6   0.069 ns          
## 4 Scores 2      3            -6  -12.6       0.648 0.069 ns          
## 5 Scores 2      4            -2   -8.65      4.65  0.646 ns          
## 6 Scores 3      4             4   -2.65     10.6   0.209 ns
```


{{< /tab >}}
{{< /tabs >}}

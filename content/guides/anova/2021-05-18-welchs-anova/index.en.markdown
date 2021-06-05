---
title: Welch's one-way ANOVA
author: Carlos Rodriguez
date: '2021-05-18'
slug: welchs-anova
categories: []
tags: []
subtitle: ''
summary: 'Designs with one between-subjects factor that fail to meet the homogeneity of variance assumption.'
authors: []
lastmod: '2021-05-18T21:14:42-06:00'
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

<!-- Prevent the jmv output from wrapping. Make it scrollable horizontally -->
<!-- <style> -->
<!-- pre code, pre, code { -->
<!--   white-space: pre !important; -->
<!--   overflow-x: scroll !important; -->
<!--   word-break: keep-all !important; -->
<!--   word-wrap: initial !important; -->
<!-- } -->
<!-- </style> -->

<!-- Limit the vertical height of output and source -->
<style type="text/css">
pre {
  max-height: 310px;
  overflow-y: auto;
}

pre[class] {
  max-height: 100px;
}
</style>

When the homogeneity of variance assumption is violated for a one-way ANOVA, a Welch's ANOVA can be conducted instead. One limitation for the Welch's ANOVA is that it is restricted to data with only one explanatory factor (i.e. one-way designs). This guide covers how to test for normality, homogeneity of variance and how to conduct a Welch's ANOVA followed by the appropriate post-hoc tests with the jmv and rstatix packages. The two approaches use the same example data set as the Fisher's [one-way ANOVA](/guides/anova/one-way-anova).

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

### Perform ANOVA tests {#tests}
<!-- -----------------------TABS---------------------------------- -->
{{< tabs tabTotal="2" tabID="1" tabName1="jmv" tabName2="rstatix"  >}}

<!-- -----------------------Tab 1---------------------------------- -->
{{< tab tabNum="1" >}}
The following code chunk demonstrates how to code a Welch's ANOVA in R with the jmv package. The jmv approach uses the exact same function as the Fisher's approach in one-way ANOVA and can take many of the same options to produce plots and conduct tests of normality and equality of variance (homogeneity of variance or homoscedasticity) with the `norm = TRUE` and `eqv = TRUE` options. Games-Howell post hoc tests are produced by setting `phMethod = 'gamesHowell'`.

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
In rstatix, the `anova_test()` function can analyse several types of between and within subjects ANOVA designs. However, to conduct a Welch's ANOVA , the `welch_anova_test()` function is required. The syntax to conduct the actual test is rather simple. In the following code chunk, I've chosen to explicitly declare `formula = Scores ~ Group` and `data = C3E9`. However, one could just as easily specify the dataframe and formula as long as data is the first entered variable. Similarly, data and formula do not need to be explicitly specified for the `games_howell_test()` function, but hare specified in the example for consistency. 

To produce the Shapiro-Wilk's test of normality as in the jmv output, we will need to create an analysis of variance (aov) model with the base R `aov()` function. The rstatix package does contain its own `shapiro_test()` function, but it will not test normality of the residuals, only the actual values. In the code snippet below, the `aov()` model is nested within the `residuals()` function, which is nested within the `shapiro.test()` function. This will conduct Shapiro-Wilk's test on the residuals of the aov object. Finally, for the homogeneity of variance test, the rstatix `levene_test()` function specifying a formula and a dataframe does the job. Base R also can also plot the Residuals vs Fitted values and generate Q-Q (quantile-quantile) plots from an `aov()` object to examine homogeneity of variance and normality respectively.

#### Check normality

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

#### Check homogeneity of variance

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

```r
# Alternatively
# welch_anova_test(C3E9, Scores ~ Group)
```

#### Post hoc tests


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

[Back to tabs](#tests)

### Interpretation
Both approaches produce the same results --- a significant effect of the omnibus test [F(3, 4.44) = 7.69, p < 0.05]. Furthermore, Games-Howell post-hoc tests reveal a significant difference between the scores of group 1 (rational-emotive therapy) and group 2 (psychoanalytic therapy) with group 1 demonstrating lower mean phobia scores.

### Wrap up
Similar to the one-way ANOVA guide, the `anovaOneW()` function from the jmv package is a convenient way to perform Welch's one-way ANOVA in R. You can get a significant amount of output including normality, homogeneity of variance, and post-hoc tests by adding a few extra arguments to the call. To produce the same output with the rstatix package a little more work is necessary, but the results will be same.

### References
<div id="refs" class="references">

<div id="ref-R-ggpubr">

Kassambara, Alboukadel. 2020a. *Ggpubr: ’Ggplot2’ Based Publication Ready Plots*. <https://CRAN.R-project.org/package=ggpubr>.

</div>

<div id="ref-R-rstatix">

———. 2020b. *Rstatix: Pipe-Friendly Framework for Basic Statistical Tests*. <https://CRAN.R-project.org/package=rstatix>.

</div>

<div id="ref-R-AMCP">

Maxwell, Scott, Harold Delaney, and Ken Kelley. 2020. *AMCP: A Model Comparison Perspective*. <https://CRAN.R-project.org/package=AMCP>.

</div>

<div id="ref-AMCP">

Maxwell, Scott E, Harold D Delaney, and Ken Kelley. 2017. *Designing Experiments and Analyzing Data: A Model Comparison Perspective*. Routledge.

</div>

<div id="ref-R-jmv">

Selker, Ravi, Jonathon Love, and Damian Dropmann. 2020. *Jmv: The ’Jamovi’ Analyses*. <https://CRAN.R-project.org/package=jmv>.

</div>

<div id="ref-R-tidyverse">

Wickham, Hadley. 2019. *Tidyverse: Easily Install and Load the ’Tidyverse’*. <https://CRAN.R-project.org/package=tidyverse>.

</div>

</div>

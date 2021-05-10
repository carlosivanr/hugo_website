---
title: Two-way ANOVA in R
author: Carlos Rodriguez
date: '2021-05-04'
slug: two-way-anova
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-05-04T19:37:58-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---


In this post, I will cover three different ways of conducting two-way ANOVAs. I will be using the jmv, rstatix, and base R functions. For this demo, we will utilize an example with the chapter_7_table_5 data from the AMCP package. In this particular dataset, we have hypothetical experiment that tests the presence or absence of biofeedback in combination with three different drugs on measures of hypertension. The Feedback group is coded as a 1 or a 2 where 1 indicates the participants received biofeedback and 2 indicates participants did not receive biofeedback. Drug is coded as 1, 2, or 3, and specifies one of three hypothetical drugs that purportedly reduce blood pressure. Finally, Scores refer to a measure of blood pressure where lower values are better.



```r
library(AMCP)

# Load data
data("chapter_7_table_5")

# Inspect data
head(chapter_7_table_5)
```

```
##   Score Feedback Drug
## 1   170        1    1
## 2   175        1    1
## 3   165        1    1
## 4   180        1    1
## 5   160        1    1
## 6   186        1    2
```


<!-- -----------------------TABS---------------------------------- -->
{{< tabs tabTotal="3" tabID="1" tabName1="jmv" tabID="2" tabName2="rstatix" tabID="3" tabName3="base R" >}}
<!-- -----------------------Tab 1---------------------------------- -->
{{< tab tabNum="1" >}}  
###
I've posted about the jmv package before and I think its good start for beginners who want to perform statistical analyses in R. The syntax is streamlined, the output tables look great, its functions automatically convert numeric data to factor, and they can produce  plots to boot! So if you're coming into the R universe from statistical software like SPSS, jmv eases that transition. Let's start by loading the packages, loading the data, and then inspecting it.

```r
library(AMCP)
library(jmv)

# Load data
data("chapter_7_table_5")
```

Next, we'll perform our two-way ANOVA. The following code will set Score as the dependent variable and Feedback and Drug as independent variables. The settings are set to use type III sums of squares, the effect size will be reported as partial eta squared, post-hoc tests will be performed with Feedback and Drug factors, and the plots of the means of Feedback and Drug will be generated.

```r
# ANOVA test with jmv
ANOVA(formula = Score ~ Feedback * Drug,
      data = chapter_7_table_5,
      ss = "3",
      effectSize = 'partEta',
      postHoc = c('Feedback', 'Drug'),
      postHocCorr = 'none',
      emMeans = ~ Feedback:Drug,
      emmPlots = TRUE)
```

```
## 
##  ANOVA
## 
##  ANOVA - Score                                                                                   
##  ─────────────────────────────────────────────────────────────────────────────────────────────── 
##                     Sum of Squares    df    Mean Square    F            p            η²p         
##  ─────────────────────────────────────────────────────────────────────────────────────────────── 
##    Feedback              1080.0000     1      1080.0000     6.934189    0.0145635    0.2241594   
##    Drug                  3420.0000     2      1710.0000    10.979133    0.0004113    0.4777871   
##    Feedback:Drug          780.0000     2       390.0000     2.504013    0.1028767    0.1726428   
##    Residuals             3738.0000    24       155.7500                                          
##  ─────────────────────────────────────────────────────────────────────────────────────────────── 
## 
## 
##  POST HOC TESTS
## 
##  Post Hoc Comparisons - Feedback                                                                    
##  ────────────────────────────────────────────────────────────────────────────────────────────────── 
##    Feedback         Feedback    Mean Difference    SE          df          t            p           
##  ────────────────────────────────────────────────────────────────────────────────────────────────── 
##    1           -    2                 -12.00000    4.557046    24.00000    -2.633285    0.0145635   
##  ────────────────────────────────────────────────────────────────────────────────────────────────── 
## 
## 
##  Post Hoc Comparisons - Drug                                                                 
##  ─────────────────────────────────────────────────────────────────────────────────────────── 
##    Drug         Drug    Mean Difference    SE          df          t             p           
##  ─────────────────────────────────────────────────────────────────────────────────────────── 
##    1       -    2            -24.000000    5.581219    24.00000    -4.3001362    0.0002462   
##            -    3            -21.000000    5.581219    24.00000    -3.7626192    0.0009578   
##    2       -    3              3.000000    5.581219    24.00000     0.5375170    0.5958599   
##  ───────────────────────────────────────────────────────────────────────────────────────────
```

<div class="figure">
<img src="{{< blogdown/postref >}}index_files/figure-html/plot1-1.png" alt="ANOVA plot of means by drug and feedback." width="672" />
<p class="caption">Figure 1: ANOVA plot of means by drug and feedback.</p>
</div>

#### Comparisons of Cell Means with jmv
Where things get a little more complicated for jmv, is in the comparisons of cell means. These would test all pairwise combinations of Drug within Factor for this example. The following code will produce the results of interest, but it will also produce quite a bit of output and so the code is commented

```r
# # Drug 1 vs Drug 2, Drug 1 vs Drug 3, and Drug 2 vs Drug 3 at Feedback 1
# ttestIS(data = filter(chapter_7_table_5, Feedback == 1, Drug == 1 | Drug == 2), vars = Score, group = Drug)
# ttestIS(data = filter(chapter_7_table_5, Feedback == 1, Drug == 1 | Drug == 3), vars = Score, group = Drug)
# ttestIS(data = filter(chapter_7_table_5, Feedback == 1, Drug == 2 | Drug == 3), vars = Score, group = Drug)
# 
# #Drug 1 vs Drug 2, Drug 1 vs Drug 3, and Drug 2 vs Drug 3 at Feedback 2
# ttestIS(data = filter(chapter_7_table_5, Feedback == 2, Drug == 1 | Drug == 2), vars = Score, group = Drug)
# ttestIS(data = filter(chapter_7_table_5, Feedback == 2, Drug == 1 | Drug == 3), vars = Score, group = Drug)
# ttestIS(data = filter(chapter_7_table_5, Feedback == 2, Drug == 2 | Drug == 3), vars = Score, group = Drug)
```
{{< /tab >}}

<!-- -----------------------Tab 2---------------------------------- -->
{{< tab tabNum="2" >}}  
###
When using the rstatix package, we will want to convert our Feedback and Drug data to factor because it won't convert them automatically like jmv. If we don't do this the `anova_test()` function will think they are continuous variables and our results will not match. The rstatix command is a bit more limited in the sense that it will not automatically create the post hoc tests and it will not automatically generate plots. In order to generate the posthoc tests. When using rstatix, it's useful to also useful to load the tidyverse function so that you can use the pipe operator (`%>%`) and the `group_by()` function. The pipe operator basically takes the output of one function or a data frame and feeds into another function.

```r
library(AMCP)
library(rstatix)
library(tidyverse)

# Load data
data("chapter_7_table_5")

# Convert Drug and Feedback to factor
chapter_7_table_5$Drug <- as.factor(chapter_7_table_5$Drug)
chapter_7_table_5$Feedback <- as.factor(chapter_7_table_5$Feedback)

# ANOVA test rstatix
anova_test(chapter_7_table_5, 
           Score ~ Feedback * Drug, 
           dv = Score, 
           type = 3, 
           effect.size = "pes")
```

```
## ANOVA Table (type III tests)
## 
##          Effect DFn DFd      F        p p<.05   pes
## 1      Feedback   1  24  6.934 0.015000     * 0.224
## 2          Drug   2  24 10.979 0.000411     * 0.478
## 3 Feedback:Drug   2  24  2.504 0.103000       0.173
```

#### Alternative rstatix method for two-way ANOVA
There is an additional way of using the `anova_test()` function. First, we will create a model with the base R `aov()` function. Then we can run the `anova_test()` function on our model. This form will have the added benefit of playing well the `emmeans_test()` function to correctly calculate the degrees of freedom for post-hoc tests. Also in this code block is the command to perform the the comparisons of cell means within each factor.

```r
# Alternative way of using anova_test
# First make an base R anova model
model <- aov(formula = Score ~ Feedback * Drug,
    data = chapter_7_table_5)

# Second, use anova_test on the model to print the output
anova_test(model, effect.size = "pes", type = 3)
```

```
## Coefficient covariances computed by hccm()
```

```
## ANOVA Table (type III tests)
## 
##          Effect DFn DFd      F        p p<.05   pes
## 1      Feedback   1  24  6.934 0.015000     * 0.224
## 2          Drug   2  24 10.979 0.000411     * 0.478
## 3 Feedback:Drug   2  24  2.504 0.103000       0.173
```

#### Post-hoc tests with rstatix

```r
# Post-hoc tests for Feedback collapsed across Drug
# Requires the alternative method for correctly calculating degrees of freedom
chapter_7_table_5 %>% 
  emmeans_test(Score ~ Feedback, 
               p.adjust.method = "none", 
               model = model)
```

```
## NOTE: Results may be misleading due to involvement in interactions
```

```
## # A tibble: 1 x 8
##   .y.   group1 group2    df statistic      p  p.adj p.adj.signif
## * <chr> <chr>  <chr>  <dbl>     <dbl>  <dbl>  <dbl> <chr>       
## 1 Score 1      2         24     -2.63 0.0146 0.0146 *
```

```r
chapter_7_table_5 %>% 
  emmeans_test(Score ~ Drug, 
               p.adjust.method = "none", 
               model = model)
```

```
## NOTE: Results may be misleading due to involvement in interactions
```

```
## # A tibble: 3 x 8
##   .y.   group1 group2    df statistic        p    p.adj p.adj.signif
## * <chr> <chr>  <chr>  <dbl>     <dbl>    <dbl>    <dbl> <chr>       
## 1 Score 1      2         24    -4.30  0.000246 0.000246 ***         
## 2 Score 1      3         24    -3.76  0.000958 0.000958 ***         
## 3 Score 2      3         24     0.538 0.596    0.596    ns
```

#### Comparisons of cell means

```r
# Comparisons of cell means Feedback at each level of Drug
chapter_7_table_5 %>% group_by(Drug) %>%
         pairwise_t_test(Score ~ Feedback, 
                         p.adjust.method = "none", 
                         pool.sd = FALSE, 
                         var.equal = TRUE)
```

```
## # A tibble: 3 x 11
##   Drug  .y.   group1 group2    n1    n2 statistic    df     p p.adj p.adj.signif
## * <fct> <chr> <chr>  <chr>  <int> <int>     <dbl> <dbl> <dbl> <dbl> <chr>       
## 1 1     Score 1      2          5     5    -2.67      8 0.028 0.028 *           
## 2 2     Score 1      2          5     5     0.253     8 0.807 0.807 ns          
## 3 3     Score 1      2          5     5    -2.34      8 0.047 0.047 *
```

```r
# Comparisons of cell means Drug at each level of Feedback
chapter_7_table_5 %>% group_by(Feedback) %>%
         pairwise_t_test(Score ~ Drug,
                         p.adjust.method = "none",
                         pool.sd = FALSE,
                         var.equal = TRUE)
```

```
## # A tibble: 6 x 11
##   Feedback .y.   group1 group2    n1    n2 statistic    df     p p.adj
## * <fct>    <chr> <chr>  <chr>  <int> <int>     <dbl> <dbl> <dbl> <dbl>
## 1 1        Score 1      2          5     5     -4.61     8 0.002 0.002
## 2 1        Score 1      3          5     5     -2.53     8 0.035 0.035
## 3 1        Score 2      3          5     5      1.71     8 0.126 0.126
## 4 2        Score 1      2          5     5     -2.18     8 0.061 0.061
## 5 2        Score 1      3          5     5     -2.80     8 0.023 0.023
## 6 2        Score 2      3          5     5     -1.05     8 0.326 0.326
## # … with 1 more variable: p.adj.signif <chr>
```
{{< /tab >}}

<!-- -----------------------Tab 3---------------------------------- -->
{{< tab tabNum="3" >}}
###
When using the base R function, it's wise to also load the car package. The default sums of squares in the aov() function is type II and in order to get ANOVA results with type III sums of squares, the car package is needed. Similar to the functions with rstatix, we will need to convert the Feedback and Drug data to factor before running the ANOVA. There are ways to get the effect size as in the jmv or rstatix packages, but, an additional package and more coding will be needed, thus it provides a bit less functionality.

```r
library(car)
library(AMCP)

# Load data
data("chapter_7_table_5")

# Convert Feedback and Drug to factor
chapter_7_table_5$Drug <- as.factor(chapter_7_table_5$Drug)
chapter_7_table_5$Feedback <- as.factor(chapter_7_table_5$Feedback)

# ANOVA with car
Anova(aov(formula = Score ~ Feedback * Drug,
    data = chapter_7_table_5),
    type = "3")
```

```
## Anova Table (Type III tests)
## 
## Response: Score
##                Sum Sq Df   F value    Pr(>F)    
## (Intercept)   1117470  1 7174.7673 < 2.2e-16 ***
## Feedback         1080  1    6.9342 0.0145635 *  
## Drug             3420  2   10.9791 0.0004113 ***
## Feedback:Drug     780  2    2.5040 0.1028767    
## Residuals        3738 24                        
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

{{< /tab >}}
{{< /tabs >}}

### Wrap-up
All three packages, jmv, rstatix, and car, will produce the exact same results for the main effects and interaction tests of a two-way ANOVA. The jmv package is a great way to conduct statistical tests if you are beginning with R because it simplifies some of the syntax, generates plots automatically, and it also converts some of your numerical data to categorical. For this type of analysis, it's main disadvantage is in conducting the comparisons of cell means. However, R is such a flexible program that you could conceivably rely on jmv for the interaction and tests of the main effects and then use rstatix for conducting the comparisons of cell means.

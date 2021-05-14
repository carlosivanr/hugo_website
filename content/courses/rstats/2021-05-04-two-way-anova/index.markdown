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
type: book
weight: 20
---


For this module, I will cover different ways of conducting two-way ANOVAs in R. Two-way ANOVAS are for situations when you want to compare means of groups that differ along two categorical variables.

### The data set
For this demo, we will utilize an example with the chapter_7_table_5 data from the AMCP package. In the example dataset, we have a hypothetical experiment that tests the presence or absence of biofeedback in combination with three different drugs on measures of blood pressure. The Feedback group is coded as a 1 or a 2 where 1 indicates the participants received biofeedback and 2 indicates participants did not. Drug is coded as 1, 2, or 3, and specifies one of three hypothetical drugs that purportedly reduce blood pressure. Finally, Scores refer to a measure of blood pressure where lower values are better. This leaves us with a 2x3 between-subjects design and we will assume that the assumptions of independence, equality of variance, and normality are met for the sample dataset.


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

### Perform ANOVA Tests
<!-- -----------------------TABS---------------------------------- -->
{{< tabs tabTotal="2" tabID="1" tabName1="jmv" tabID="2" tabName2="rstatix" tabID="3" tabName3="base R" >}}

<!-- -----------------------Tab 1---------------------------------- -->
{{< tab tabNum="1" >}}  
We've covered the jmv package before and it can be a good starting point for beginners who want to perform statistical analyses in R. The syntax is streamlined, the output tables look great, its functions will convert numeric data to factor, and it can automatically produce plots to boot! So if you're coming into the R universe from statistical software like SPSS, jmv eases that transition. Let's start by loading the packages, loading the data, and then inspecting it.

### Perform two-way ANOVA, post-hoc tests, and generate plots
Next, we'll perform our two-way ANOVA. The following code will set Score as the dependent variable and Feedback and Drug as independent variables. The settings are set to use type III sums of squares, the effect size will be reported as partial eta squared, post-hoc tests will be performed with Feedback and Drug factors, and the plots of the means of Feedback and Drug will be generated.

```r
library(jmv)
```

```
## Warning: package 'jmv' was built under R version 4.0.5
```

```r
# ANOVA test with jmv
ANOVA(formula = Score ~ Feedback * Drug,
      data = chapter_7_table_5,
      ss = "3",
      effectSize = 'partEta',
      postHoc = c('Feedback', 'Drug'),
      postHocCorr = 'none',
      emMeans = ~ Feedback + Drug,
      emmPlots = TRUE,
      emmTables = FALSE,
      ciWidthEmm = 95
      )
```

```
## 
##  ANOVA
## 
##  ANOVA - Score                                                                                   
##  ----------------------------------------------------------------------------------------------- 
##                     Sum of Squares    df    Mean Square    F            p            <U+03B7>²p         
##  ----------------------------------------------------------------------------------------------- 
##    Feedback              1080.0000     1      1080.0000     6.934189    0.0145635    0.2241594   
##    Drug                  3420.0000     2      1710.0000    10.979133    0.0004113    0.4777871   
##    Feedback:Drug          780.0000     2       390.0000     2.504013    0.1028767    0.1726428   
##    Residuals             3738.0000    24       155.7500                                          
##  ----------------------------------------------------------------------------------------------- 
## 
## 
##  POST HOC TESTS
## 
##  Post Hoc Comparisons - Feedback                                                                    
##  -------------------------------------------------------------------------------------------------- 
##    Feedback         Feedback    Mean Difference    SE          df          t            p           
##  -------------------------------------------------------------------------------------------------- 
##    1           -    2                 -12.00000    4.557046    24.00000    -2.633285    0.0145635   
##  -------------------------------------------------------------------------------------------------- 
## 
## 
##  Post Hoc Comparisons - Drug                                                                 
##  ------------------------------------------------------------------------------------------- 
##    Drug         Drug    Mean Difference    SE          df          t             p           
##  ------------------------------------------------------------------------------------------- 
##    1       -    2            -24.000000    5.581219    24.00000    -4.3001362    0.0002462   
##            -    3            -21.000000    5.581219    24.00000    -3.7626192    0.0009578   
##    2       -    3              3.000000    5.581219    24.00000     0.5375170    0.5958599   
##  -------------------------------------------------------------------------------------------
```

<div class="figure">
<img src="{{< blogdown/postref >}}index_files/figure-html/plot1-1.png" alt="Estimated marginal means and confidence intervals." width="672" />
<p class="caption">Figure 1: Estimated marginal means and confidence intervals.</p>
</div><div class="figure">
<img src="{{< blogdown/postref >}}index_files/figure-html/plot1-2.png" alt="Estimated marginal means and confidence intervals." width="672" />
<p class="caption">Figure 2: Estimated marginal means and confidence intervals.</p>
</div>

A walk through of the results and their interpretation is presented in the rstatix tab.

<!-- #### Comparisons of Cell Means with jmv -->
<!-- Where things get a little more complicated for jmv, is in the comparisons of cell means. These would test all pairwise combinations of Drug within Factor for this example. The following code will produce the results of interest, but it will also produce quite a bit of output and so the code is commented -->

{{< /tab >}}

<!-- -----------------------Tab 2---------------------------------- -->
{{< tab tabNum="2" >}}  
When using rstatix, it's useful to also useful to load the tidyverse package so that you can use the pipe operator (`%>%`) and the `group_by()` function. The pipe operator takes the output of one function or a data frame and feeds into another function. Once we load our data, we will first want to convert Feedback and Drug to factor so the values get treated as categorical variable. Next, we will build an ANOVA model with the base R `aov()` function that predicts Score by Feedback and Drug. Finally, we will use the rstatix `anova_test()` function on our aov model to produce the output for the omnibus test.

```r
library(tidyverse)
library(rstatix)
library(ggpubr)

# Convert Drug and Feedback to factor
chapter_7_table_5$Drug <- as.factor(chapter_7_table_5$Drug)
chapter_7_table_5$Feedback <- as.factor(chapter_7_table_5$Feedback)

# Create aov() model
model <- aov(formula = Score ~ Feedback * Drug,
    data = chapter_7_table_5)

# anova_test on  model, with partial eta squared and type III ss
anova_test(model, effect.size = "pes", type = 3)
```

```
## ANOVA Table (type III tests)
## 
##          Effect DFn DFd      F        p p<.05   pes
## 1      Feedback   1  24  6.934 0.015000     * 0.224
## 2          Drug   2  24 10.979 0.000411     * 0.478
## 3 Feedback:Drug   2  24  2.504 0.103000       0.173
```
The results from the omnibus test reveal that there is no significant interaction between Drug and Feedback. On the other hand, there are significant effects for Drug and for Feedback. Thus, at this point, we could proceed to perform tests of marginal means. 

#### Tests of Marginal Means
The rstatix package includes a function, `emmeans_test()`, that can perform tests of estimated marginal means. To perform these tests, we will use the aov model that we created in the previous step and we will conduct two separate tests, one for Feedback and one for Drug. I set the correction method to `"none"`, but this can be easily changed according to your situation.

We will also use the `get_summary_stats()` function here that will illustrate how the tidyverse package meshes with rstatix to produce descriptive statistics to aid in the interpretation of the output. First we will start with the chapter_7_table_5 data and pipe it to the `group_by()` function which will create subsets of data according to combinations of Drug and Feedback, then that output is fed into the `get_summary_stats()` which will calculate several descriptive statistics for each group. Finally, to display only a part of this output, the `select()` function will display only the four columns specified.

To plot the data we will rely on the ggpubr package. The ggpubr and rstatix packages are developed by the same individual and are aimed at simplifying the syntax for conducting statistical tests and generating plots in R. For our purposes we will use the `ggerrorplot()` function.
#### Feedback

```r
# Test of marginal means for Feedback
chapter_7_table_5 %>% 
  emmeans_test(Score ~ Feedback, 
               p.adjust.method = "none", 
               model = model)
```

```
## NOTE: Results may be misleading due to involvement in interactions
```

```r
## # A tibble: 1 x 9
##   term     .y.   group1 group2    df statistic      p  p.adj p.adj.signif
## * <chr>    <chr> <chr>  <chr>  <dbl>     <dbl>  <dbl>  <dbl> <chr>       
## 1 Feedback Score 1      2         24     -2.63 0.0146 0.0146 *
```

```r
# Get Summary Stats
chapter_7_table_5 %>% 
  group_by(Feedback) %>%
  get_summary_stats(Score) %>%
  select(Feedback, mean, sd, se)
```

```
## # A tibble: 2 x 4
##   Feedback  mean    sd    se
##   <fct>    <dbl> <dbl> <dbl>
## 1 1          187  18.0  4.64
## 2 2          199  15.6  4.03
```

```r
# Generate plot
ggerrorplot(chapter_7_table_5, 
       x = "Feedback", 
       y = "Score",
       add = "mean",
       desc_stat = "mean_ci",
       error.plot = "errorbar",
       width = .1,
       )
```

<div class="figure">
<img src="{{< blogdown/postref >}}index_files/figure-html/plot3-1.png" alt="Means and confidence intervals for Feedback collapsed across Drug. n.b. Notice that the confidence intervals between the ggpubr plots and those produced by jmv are different. The reason for this is because of how the plotting function calculates confidence intervals. ggpubr computes a confidence interval based around a group mean using a group specific standard error, while the jmv function plots confidence intervals based on a standard error from an estimated marginal mean framework." width="672" />
<p class="caption">Figure 3: Means and confidence intervals for Feedback collapsed across Drug. n.b. Notice that the confidence intervals between the ggpubr plots and those produced by jmv are different. The reason for this is because of how the plotting function calculates confidence intervals. ggpubr computes a confidence interval based around a group mean using a group specific standard error, while the jmv function plots confidence intervals based on a standard error from an estimated marginal mean framework.</p>
</div>

#### Drug

```r
# Test of marginal means for Drug
chapter_7_table_5 %>% 
  emmeans_test(Score ~ Drug, 
               p.adjust.method = "none", 
               model = model)
```

```
## NOTE: Results may be misleading due to involvement in interactions
```

```r
## # A tibble: 3 x 9
##   term  .y.   group1 group2    df statistic        p    p.adj p.adj.signif
## * <chr> <chr> <chr>  <chr>  <dbl>     <dbl>    <dbl>    <dbl> <chr>       
## 1 Drug  Score 1      2         24    -4.30  0.000246 0.000246 ***         
## 2 Drug  Score 1      3         24    -3.76  0.000958 0.000958 ***         
## 3 Drug  Score 2      3         24     0.538 0.596    0.596    ns
```

```r
# Get Summary Stats
chapter_7_table_5 %>% 
  group_by(Drug) %>%
  get_summary_stats(Score) %>%
  select(Drug, mean, sd, se)
```

```
## # A tibble: 3 x 4
##   Drug   mean    sd    se
##   <fct> <dbl> <dbl> <dbl>
## 1 1       178  12.3  3.89
## 2 2       202  11.8  3.74
## 3 3       199  18.2  5.75
```

```r
# Generate Plot
ggerrorplot(chapter_7_table_5, 
       x = "Drug", 
       y = "Score",
       add = "mean",
       desc_stat = "mean_ci",
       error.plot = "errorbar",
       width = .1,
)
```

<div class="figure">
<img src="{{< blogdown/postref >}}index_files/figure-html/plot4-1.png" alt="Means and confidence intervals for Drug collapsed across Feedback." width="672" />
<p class="caption">Figure 4: Means and confidence intervals for Drug collapsed across Feedback.</p>
</div>

The two tests of marginal means will produce a couple of messages to remind us that the results could be misleading because of interactions. However, as we saw in the omnibus test, there was no significant interaction and can disregard the message. The results of the `emmeans_test()` on Feedback indicate a significant effect and suggests that the participants undergoing biofeedback had lower bloodpressure scores than those without biofeedback. When examining the output of the results of the `emmeans_test()` on Drug, we see that the mean of Drug 1 is significantly lower than that of Drug 2 and Drug 3. However, the mean of Drug 2 is not significantly different than that of Drug 3.

<!-- #### Alternative rstatix method for two-way ANOVA -->
<!-- There is an additional way of using the `anova_test()` function. First, we will create a model with the base R `aov()` function. Then we can run the `anova_test()` function on our model. This form will have the added benefit of playing well the `emmeans_test()` function to correctly calculate the degrees of freedom for post-hoc tests. Also in this code block is the command to perform the the comparisons of cell means within each factor. -->





<!-- #### Comparisons of cell means -->





<!-- [Back to jmv](#jmv) -->
{{< /tab >}}

<!-- -----------------------Tab 3---------------------------------- -->
{{< tab tabNum="3" >}}
###
When using the base R function, it's wise to also load the car package. The default sums of squares in the `aov()` function is type II and in order to get ANOVA results with type III sums of squares, the car package is needed. Similar to the functions with rstatix, we will need to convert the Feedback and Drug data to factor before running the ANOVA.


<!-- As for the post-hoc tests, we can use the built-in `TukeyHSD()` function. These values will match those produced by jmv and rstatix when setting the correction method to "tukey". There are other ways of  -->

{{< /tab >}}
{{< /tabs >}}

### Wrap-up
The jmv and rstatix functions will produce the exact same results for the main effects and interaction tests of a two-way ANOVA. The jmv package is a great place to start with statistical tests if you are beginning with R because it simplifies some of the syntax, generates plots automatically, and it also converts some of your numerical data to categorical. While it's a lot easier to code a 2x3 ANOVA with jmv there are some advantages to using rstatix that I will become apparent in future modules. 

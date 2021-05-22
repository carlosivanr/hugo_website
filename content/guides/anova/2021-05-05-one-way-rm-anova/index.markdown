---
title: One-way Repeated Measures ANOVA
author: Carlos Rodriguez
date: '2021-05-05'
slug: one-way-rm-anova
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-05-05360:40:40-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 30
biblio-style: "apa"
link-citations: true
nocite: |
  @R-rstatix, @R-AMCP, @R-jmv, @R-ggpubr, @R-tidyverse
---

<style>
pre code, pre, code {
  white-space: pre !important;
  overflow-x: scroll !important;
  word-break: keep-all !important;
  word-wrap: initial !important;
}
</style>



In this guide we will cover how to conduct one-way repeated measures ANOVA with the jmv and rstatix packages. Repeated measures ANOVA are used in cases where a particular observation from the same subject is taken multiple times. An example of this may be in an experiment of learning and memory where a rat is timed over several trials to measure how long it takes to find an escape platform in the Morris Water Task. Another example will be seen in our data set where scores for a cognitive assessment are compared at multiple ages.

### The data set
For this demo, we will use the chapter_11_table_5 dataset from the AMCP package. These data are from a hypothetical study that tracked the age-normed general cognitive scores from the McCarthy Scales of Children's Abilities (MSCA) of 12 children at 4 different time points.


```r
library(AMCP)

# Load data
data("chapter_11_table_5")

# Inspect data
head(chapter_11_table_5)
```

```
##   Months30 Months36 Months42 Months48
## 1      108       96      110      122
## 2      103      117      127      133
## 3       96      107      106      107
## 4       84       85       92       99
## 5      118      125      125      116
## 6      110      107       96       91
```

### Perform ANOVA Tests {#tests}
<!-- -----------------------TABS---------------------------------- -->
{{< tabs tabTotal="2" tabID="1" tabName1="jmv" tabID="2" tabName2="rstatix" tabID="3" tabName3="paired-t-test">}}

<!-- -----------------------Tab 1---------------------------------- -->
{{< tab tabNum="1" >}}
With the jmv package, coding repeated measures ANOVA is a bit more cumbersome compared to one- and two-way ANOVAS. We will have to set the rm option which takes a "list of lists". For the one-way repeated measures design, we will need a list of one list that contains a label which will be "Age-in-months", and a character vector of the levels of the age factor (i.e. 30, 60, 42, 48). Next, we will work on the rmCells by making another list of lists. This list will be made up of 4 lists, one for each level, and each list will have a `measure` that corresponds to the name of the column of the dataframe containing the data, and a `cell` that corresponds to each level. Now we can move on to specifying the repeated measure terms in "rmTerms." For our purposes we will set `spherCorr = 'none'`, but in practice you may want to apply a correction for sphericity depending on your data. We will then set `effectSize = 'omega'`, and set `emMeans = TRUE` to produce the marginal means by months so that we can plot the data when `emmPlots = TRUE`. 


```r
library(jmv)

# Repeated measures anova with jmv
anovaRM(
  data = chapter_11_table_5,
  rm = list(
    list(
      label = "Age-in-months", 
      levels = c("30", "36", "42", "48"))),
  rmCells = list(
    list(
      measure = "Months30", 
      cell = "30"),
    list(
      measure = "Months36", 
      cell = "36"),
    list(
      measure = "Months42", 
      cell = "42"),
    list(
      measure = "Months48", 
      cell = "48")),
  rmTerms = list(
    "Age-in-months"),
  spherCorr = 'none',
  effectSize = 'partEta',
  emMeans = ~ `Age-in-months`,
  emmPlots = TRUE,
  postHocCorr = "none",
  postHoc = "Age-in-months",
  depLabel = "Mean MSCA Scores")
```

```
## 
##  REPEATED MEASURES ANOVA
## 
##  Within Subjects Effects                                                                        
##  ────────────────────────────────────────────────────────────────────────────────────────────── 
##                     Sum of Squares    df    Mean Square    F           p            η²-p        
##  ────────────────────────────────────────────────────────────────────────────────────────────── 
##    Age-in-months          552.0000     3      184.00000    3.026919    0.0432186    0.2157936   
##    Residual              2006.0000    33       60.78788                                         
##  ────────────────────────────────────────────────────────────────────────────────────────────── 
##    Note. Type 3 Sums of Squares
## 
## 
##  Between Subjects Effects                                                           
##  ────────────────────────────────────────────────────────────────────────────────── 
##                Sum of Squares    df    Mean Square    F    p            η²-p        
##  ────────────────────────────────────────────────────────────────────────────────── 
##    Residual          6624.000    11       602.1818                                  
##  ────────────────────────────────────────────────────────────────────────────────── 
##    Note. Type 3 Sums of Squares
## 
## 
##  POST HOC TESTS
## 
##  Post Hoc Comparisons - Age-in-months                                                                          
##  ───────────────────────────────────────────────────────────────────────────────────────────────────────────── 
##    Age-in-months         Age-in-months    Mean Difference    SE          df          t             p           
##  ───────────────────────────────────────────────────────────────────────────────────────────────────────────── 
##    30               -    36                     -4.000000    3.182972    33.00000    -1.2566870    0.2176899   
##                     -    42                     -7.000000    3.182972    33.00000    -2.1992022    0.0349787   
##                     -    48                     -9.000000    3.182972    33.00000    -2.8275457    0.0079113   
##    36               -    42                     -3.000000    3.182972    33.00000    -0.9425152    0.3527782   
##                     -    48                     -5.000000    3.182972    33.00000    -1.5708587    0.1257555   
##    42               -    48                     -2.000000    3.182972    33.00000    -0.6283435    0.5341039   
##  ─────────────────────────────────────────────────────────────────────────────────────────────────────────────
```

<div class="figure">
<img src="{{< blogdown/postref >}}index_files/figure-html/plot1-1.png" alt="anovaRM plot of means by group." width="672" />
<p class="caption">Figure 1: anovaRM plot of means by group.</p>
</div>
{{< /tab >}}


<!-- -----------------------Tab 2---------------------------------- -->
{{< tab tabNum="2" >}}
To perform a repeated measures ANOVA with rstatix, we will first need create a new data frame with the subject id coded by a number. In this case, we have 12 subjects so we will code them 1 through 12. Next, we will want to convert our data from wide to long format. For our purposes, we will want to take the rows the subject's MSCA scores and arrange them into a column while preserving the information about the age in another column. This will make it so that we end up with 48 rows of data even though we started with 12.

We will then want to convert the subject id and the age information to factor. From there, we can create the rm_aov object with the `anova_test()` function. This is the same function that was used in the one- and two-way ANOVA guides, but tweaked slightly. The tweaks are found in the options `wid = id` to specify the subjects id factor, `within = age` to specify that our within subjects factor is age, and `effect.size = "pes"` to correspond with the jmv function. Finally, we will run the `get_anova_table()` function on our rm_aov object with `correction = "none"`. In practice, you may want to change the correction setting even if your data do not violate the assumption of sphericity.

```r
library(tidyverse)
library(rstatix)
library(ggpubr)

# Create a new data frame with a subject id
rm_data <- cbind(id = c(1:12), chapter_11_table_5)

# Convert from wide to long format then convert id and age to factor
rm_data <- rm_data %>% 
  gather(., age, score, Months30:Months48) %>%
  convert_as_factor(id, age)

# Inspect data
head(rm_data)
```

```
##   id      age score
## 1  1 Months30   108
## 2  2 Months30   103
## 3  3 Months30    96
## 4  4 Months30    84
## 5  5 Months30   118
## 6  6 Months30   110
```

```r
# Conduct repeated measures ANOVA
rm_aov <- anova_test(data = rm_data, 
                     dv = score, 
                     wid = id, 
                     within = age, 
                     effect.size = "pes", 
                     detailed = TRUE)

# Print anova table
get_anova_table(rm_aov, correction = "none")
```

```
## ANOVA Table (type III tests)
## 
##        Effect DFn DFd    SSn  SSd       F        p p<.05   pes
## 1 (Intercept)   1  11 559872 6624 929.739 5.59e-12     * 0.988
## 2         age   3  33    552 2006   3.027 4.30e-02     * 0.216
```

```r
# pairwise comparisons with pairwise_t_test
pwc <- rm_data %>%
  pairwise_t_test(score ~ age, 
                  paired = TRUE, 
                  p.adjust.method = "none")
pwc
```

```
## # A tibble: 6 x 10
##   .y.   group1   group2      n1    n2 statistic    df     p p.adj p.adj.signif
## * <chr> <chr>    <chr>    <int> <int>     <dbl> <dbl> <dbl> <dbl> <chr>       
## 1 score Months30 Months36    12    12    -1.55     11 0.149 0.149 ns          
## 2 score Months30 Months42    12    12    -2.30     11 0.042 0.042 *           
## 3 score Months30 Months48    12    12    -2.44     11 0.033 0.033 *           
## 4 score Months36 Months42    12    12    -1.09     11 0.3   0.3   ns          
## 5 score Months36 Months48    12    12    -1.16     11 0.271 0.271 ns          
## 6 score Months42 Months48    12    12    -0.896    11 0.39  0.39  ns
```

```r
# pairwise comparisons with emmeans_test
rm.model <- aov(score ~ age + Error(id/age), data = rm_data)
pwc <- rm_data %>%
  emmeans_test(score ~ age,
               p.adjust.method = "none",
               model = rm.model)
pwc
```

```
## # A tibble: 6 x 8
##   .y.   group1   group2      df statistic       p   p.adj p.adj.signif
## * <chr> <chr>    <chr>    <dbl>     <dbl>   <dbl>   <dbl> <chr>       
## 1 score Months30 Months36    33    -1.26  0.218   0.218   ns          
## 2 score Months30 Months42    33    -2.20  0.0350  0.0350  *           
## 3 score Months30 Months48    33    -2.83  0.00791 0.00791 **          
## 4 score Months36 Months42    33    -0.943 0.353   0.353   ns          
## 5 score Months36 Months48    33    -1.57  0.126   0.126   ns          
## 6 score Months42 Months48    33    -0.628 0.534   0.534   ns
```
# n.b.
jmv implements emmeans_test. Field, Miles, & Field, 2012 use pairwise.t.test() which is the function underlying pairwise_t_test(). Maxwell, Delaney, & Kelly exercise caution for pwc w rm designs because of pwcs are much more sensitive to violations of sphericity. Even small departures from sphericity can lead to biased tests. You should use a separate variance estimate approach. Howell, stresses this too especially in cases where sphericity is violated. The correction protects the overall test, but becomes problematic with the pwcs. https://www.uvm.edu/~statdhtx/StatPages/More_Stuff/RepMeasMultComp/RepMeasMultComp.html. Also sounds like SPSS does paired samples t-tests.

https://statistics.laerd.com/spss-tutorials/one-way-anova-repeated-measures-using-spss-statistics-2.php

### Plot the data
To plot the data, we can use the `ggerrorplot()` function. We will use the converted long-form rm_data rather than the wide chapter_11_table_5 dataframe. Then we just need to specify what to plot on the x and y axes, set `add = "mean", desc_stat = "mean_ci", `error.plot = "errorbar", and `width = .1`.

```r
ggerrorplot(rm_data,
            x = "age",
            y = "score",
            add = "mean",
            desc_stat = "mean_ci",
            error.plot = "errorbar",
            width = .1)
```

<div class="figure">
<img src="{{< blogdown/postref >}}index_files/figure-html/plot2-1.png" alt="ggerrorplot plot of means and 95% confidence intervals by group." width="672" />
<p class="caption">Figure 2: ggerrorplot plot of means and 95% confidence intervals by group.</p>
</div>



{{< /tab >}}

<!-- <!-- -----------------------Tab 3---------------------------------- -->
<!-- {{< tab tabNum="3" >}} -->
<!-- #### Paired T test in RM ANOVA Framework -->
<!-- ```{r, include = TRUE} -->
<!-- #### Chapter 11 Table 1, A paired t test can be run as an anovaRM -->
<!-- data("chapter_11_table_1") -->
<!-- head(chapter_11_table_1) -->

<!-- anovaRM( -->
<!--   data = chapter_11_table_1, -->
<!--         rm = list( -->
<!--             list( -->
<!--                 label = 'Condition', -->
<!--                 levels = c('Low', 'High'))), -->
<!--         rmCells = list( -->
<!--             list( -->
<!--                 measure = 'YCondition1', -->
<!--                 cell = 'Low'), -->
<!--             list( -->
<!--                 measure = 'YCondition2', -->
<!--                 cell = 'High')), -->
<!--         rmTerms = list( -->
<!--             'Condition'), -->
<!-- ) -->
<!-- ``` -->
<!-- {{< /tab >}} -->

{{< /tabs >}}

<!-- This works to link back up to tabs -->
[Back to tabs](#tests)

### Interpretation  
For the omnibus test, we obtain a significant effect of Age [F(33,3) = 3.03, p < 0.05] which suggests that the means of the 4 ages are not equal. The omega squared value is 0.04 which suggest the about 4% of the variance in scores is acounted for by age in the ages studied.

### Wrap Up
For one-way repeated measures ANOVA, using the jmv package in R will become tedious as each column will need to specified in the `rmCells = list(...` argument. This will become even more burdensome in two-way repeated measures ANOVA. 

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

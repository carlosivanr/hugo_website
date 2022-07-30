---
title: One-way ANOVA
author: Carlos Rodriguez
date: '2021-04-24'
slug: one-way-anova
categories: []
tags: []
subtitle: ''
summary: 'Designs with one between-subjects factor.'
authors: []
lastmod: "July 30, 2022"
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
draft: false
type: book
weight: 10
---
<script src="{{< blogdown/postref >}}index_files/kePrint/kePrint.js"></script>
<link href="{{< blogdown/postref >}}index_files/lightable/lightable.css" rel="stylesheet" />

<!-- Prevent the jmv output from wrapping. Make it scrollable horizontally
<!-- <style> -->
<!-- pre code, pre, code { -->
<!--   white-space: pre !important; -->
<!--   overflow-x: scroll !important; -->
<!--   word-break: keep-all !important; -->
<!--   word-wrap: initial !important; -->
<!-- } -->
<!-- </style>  -->

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

The one-way ANOVA is a commonly used statistical technique to compare means among two or more groups. This guide covers how to perform a one-way ANOVA in R. The primary assumptions of ANOVA are independence between groups, normally distributed residuals, and homogeneity of variance. When the homogeneity of variance assumption is violated, a [Welch's ANOVA](/guides/anova/welchs-anova) can be conducted instead.


### The data set
This guide relies on toy data from Exercise 9 in Chapter 3 of the AMCP package. In this exercise, a psychologist assigned 12 subjects to one of 4 different therapy treatments. These treatments consisted of rational-emotive, psychoanalytic, client-centered, and behavioral therapies. The 4 different treatments were used to investigate which therapy is more effective at reducing phobia scores.

For these data, Group represents the type of therapy the participant was randomly assigned to. Scores represent the score from a post-therapy fear scale where higher numbers indicate higher levels of phobia. Finally, each of the 12 rows represent each subject.
<div style="border: 1px solid #ddd; padding: 0px; overflow-y: scroll; height:300px; "><table class="table" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;position: sticky; top:0; background-color: #FFFFFF;"> Group </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;position: sticky; top:0; background-color: #FFFFFF;"> Scores </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 2 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 6 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 10 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 12 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 14 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 6 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 8 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 8 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 10 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 12 </td>
  </tr>
</tbody>
</table></div>

### Perform ANOVA tests {#tests}
<!-- -----------------------TABS---------------------------------- -->
{{< tabs tabTotal="2" tabID="1" tabName1="jmv" tabName2="rstatix" tabName3="Welch's jmv" tabName4="Welch's rstatix"  >}}

<!-- -----------------------Tab 1---------------------------------- -->
{{< tab tabNum="1" >}}
<br>
Jmv is a package that comes from the standalone <a href="https://jamovi.org/"> jamovi </a> statistical spreadsheet software. Jamovi was designed as an alternative to costly statistical analysis software packages like SPSS or SAS and runs R underneath the hood. 

With the `ANOVA()` function, we will predict Scores by Group (`Scores ~ Group`), set the data to be analyzed as C3E9 and `ss = "3"` to use the Type III sums of squares. The `effectSize = 'partEta'` will output the partial eta squared effect size for the omnibus test. We will also set the `postHocCorr  = 'bonf'` to conduct Bonferroni corrected post hoc tests (although `tukey` can be used as well), `postHocES = "d"` will compute Cohen's d effect sizes for the post hoc tests. Lastly, we want to set `emmMeans = ~ Group` and `emmPlots = TRUE` to plot estimated marginal means and confidence intervals.


```r
library(jmv)
library(AMCP)
data(C3E9)

# Conduct One-way ANOVA test
ANOVA(formula = Scores ~ Group, 
      data = C3E9,
      ss = "3",
      effectSize = 'partEta',
      postHoc = ~ Group,
      postHocCorr = 'bonf',
      postHocES = "d",
      emMeans = ~ Group,
      emmPlots = TRUE,
      ciWidthEmm = 95)
```

```
## 
##  ANOVA
## 
##  ANOVA - Scores                                                                             
##  ────────────────────────────────────────────────────────────────────────────────────────── 
##                 Sum of Squares    df    Mean Square    F           p            η²p         
##  ────────────────────────────────────────────────────────────────────────────────────────── 
##    Group             120.00000     3      40.000000    10.00000    0.0044074    0.7894737   
##    Residuals          32.00000     8       4.000000                                         
##  ────────────────────────────────────────────────────────────────────────────────────────── 
## 
## 
##  POST HOC TESTS
## 
##  Post Hoc Comparisons - Group                                                                                 
##  ──────────────────────────────────────────────────────────────────────────────────────────────────────────── 
##    Group         Group    Mean Difference    SE          df          t            p-bonferroni    Cohen's d   
##  ──────────────────────────────────────────────────────────────────────────────────────────────────────────── 
##    1        -    2              -8.000000    1.632993    8.000000    -4.898979       0.0071719    -4.000000   
##             -    3              -2.000000    1.632993    8.000000    -1.224745       1.0000000    -1.000000   
##             -    4              -6.000000    1.632993    8.000000    -3.674235       0.0376264    -3.000000   
##    2        -    3               6.000000    1.632993    8.000000     3.674235       0.0376264     3.000000   
##             -    4               2.000000    1.632993    8.000000     1.224745       1.0000000     1.000000   
##    3        -    4              -4.000000    1.632993    8.000000    -2.449490       0.2398111    -2.000000   
##  ──────────────────────────────────────────────────────────────────────────────────────────────────────────── 
##    Note. Comparisons are based on estimated marginal means
```

<div class="figure">
<img src="{{< blogdown/postref >}}index_files/figure-html/plot1-1.png" alt="Plot of mean Scores and 95% confidence intervals by group." width="672" />
<p class="caption">Figure 1: Plot of mean Scores and 95% confidence intervals by group.</p>
</div>


#### Nota Bene 
When using the `postHocES = "d"` option, it may be necessary to update the jmv package remotely. This will depend on the version of jmv installed from cran. One way to get the latest updates for jmv is to run the following commands and then restarting RStudio. I've noticed that without the updates the Cohen's d effect sizes can be off, so you may want to double check effect sizes.

```r
remotes::install_github('jamovi/jmvcore')
remotes::install_github('jamovi/jmv')
```

#### One-way ANOVA with the `anovaOneW()` function
The following code chunk displays how to use the `anovaOneW()` function to analyze the same data and get the same results for the omnibus, although the `anvoaOneW()` function is limited to `tukey` post hoc tests.
<!-- Alternative jmv one way ANOVA -->

```r
anovaOneW(formula = Scores ~ Group,
          data = C3E9,
          fishers = TRUE,
          welchs = FALSE,
          descPlot = TRUE,
          phMethod = 'tukey')
```
{{< /tab >}}

<!-- -----------------------Tab 2---------------------------------- -->
{{< tab tabNum="2" >}}
<br>

The rstatix package is another way of programming statistical tests in R. One of the benefits of the rstatix package is that it meshes well with the pipe (`%>%`) operator from the tidyverse package. The pipe operator takes is useful for chaining functions. An example of this chaining will become apparent in the code chunk below. The same developer of the rstatix package also developed the ggpubr package which simplifies producing ggplot2 figures. In this guide, the ggpubr package is loaded primarily to simplify producing a jmv style plot.

The sample code of conducting the `anova_test()` is not too different than the `ANOVA()` function in jmv. However, for this approach, we will need to change our Group variable to factor, otherwise `anova_test()` will think we will want to predict scores from a numeric variable rather than a categorical one. To determine the type of data you have, you can use the `str()` function on a dataframe which can display the structure of a dataframe and can display if your data are factored or not.

Next, we specify a formula predicting Scores by Group (`Scores ~ Group`). Then we tell the function that we want to analyze the C3E9 data, specify our dependent variable (dv; Scores), set the effect size output to partial eta squared ("pes"), and then set the sum of squares method to type III.


```r
library(rstatix)
library(ggpubr)

# Display structure of data
str(C3E9)
```

```
## 'data.frame':	12 obs. of  2 variables:
##  $ Group : int  1 1 1 2 2 2 3 3 3 4 ...
##  $ Scores: int  2 4 6 10 12 14 4 6 8 8 ...
```

```r
# Convert group to factor
C3E9$Group <- as.factor(C3E9$Group)

# Display structure of data
str(C3E9)
```

```
## 'data.frame':	12 obs. of  2 variables:
##  $ Group : Factor w/ 4 levels "1","2","3","4": 1 1 1 2 2 2 3 3 3 4 ...
##  $ Scores: int  2 4 6 10 12 14 4 6 8 8 ...
```

```r
# Conduct ANOVA test
anova_test(Scores ~ Group, 
           data = C3E9, 
           effect.size = "pes", 
           type = 3)
```

```
## ANOVA Table (type III tests)
## 
##   Effect DFn DFd  F     p p<.05   pes
## 1  Group   3   8 10 0.004     * 0.789
```
### Post hoc tests
To get the output for the post-hoc tests, we will run the `tukey_hsd()` function on the same data with the same formula (`Scores ~ Group`). We can also conduct pair wise comparisons with the `pairwise_t_test()` function and apply a different correction procedure such Bonferroni, Holm, or False Discovery Rate (FDR). In addition, rstatix provides some convenient ways for producing effect sizes, and summary statistics.

**Tukey's Honest Significant Difference (HSD)**

```r
C3E9 %>%
  tukey_hsd(Scores ~ Group) %>% 
  kable(., "html")
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> term </th>
   <th style="text-align:left;"> group1 </th>
   <th style="text-align:left;"> group2 </th>
   <th style="text-align:right;"> null.value </th>
   <th style="text-align:right;"> estimate </th>
   <th style="text-align:right;"> conf.low </th>
   <th style="text-align:right;"> conf.high </th>
   <th style="text-align:right;"> p.adj </th>
   <th style="text-align:left;"> p.adj.signif </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Group </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 8 </td>
   <td style="text-align:right;"> 2.7705811 </td>
   <td style="text-align:right;"> 13.2294189 </td>
   <td style="text-align:right;"> 0.00523 </td>
   <td style="text-align:left;"> ** </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Group </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> -3.2294189 </td>
   <td style="text-align:right;"> 7.2294189 </td>
   <td style="text-align:right;"> 0.63000 </td>
   <td style="text-align:left;"> ns </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Group </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> 0.7705811 </td>
   <td style="text-align:right;"> 11.2294189 </td>
   <td style="text-align:right;"> 0.02590 </td>
   <td style="text-align:left;"> * </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Group </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> -6 </td>
   <td style="text-align:right;"> -11.2294189 </td>
   <td style="text-align:right;"> -0.7705811 </td>
   <td style="text-align:right;"> 0.02590 </td>
   <td style="text-align:left;"> * </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Group </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> -2 </td>
   <td style="text-align:right;"> -7.2294189 </td>
   <td style="text-align:right;"> 3.2294189 </td>
   <td style="text-align:right;"> 0.63000 </td>
   <td style="text-align:left;"> ns </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Group </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> -1.2294189 </td>
   <td style="text-align:right;"> 9.2294189 </td>
   <td style="text-align:right;"> 0.14400 </td>
   <td style="text-align:left;"> ns </td>
  </tr>
</tbody>
</table>

#

**Bonferroni Corrected Tests**

```r
# Bonferroni corrected post hoc tests
C3E9 %>% 
  pairwise_t_test(Scores ~ Group, p.adjust.method = "bonferroni") %>%
  kable(., "html")
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> .y. </th>
   <th style="text-align:left;"> group1 </th>
   <th style="text-align:left;"> group2 </th>
   <th style="text-align:right;"> n1 </th>
   <th style="text-align:right;"> n2 </th>
   <th style="text-align:right;"> p </th>
   <th style="text-align:left;"> p.signif </th>
   <th style="text-align:right;"> p.adj </th>
   <th style="text-align:left;"> p.adj.signif </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Scores </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 0.00120 </td>
   <td style="text-align:left;"> ** </td>
   <td style="text-align:right;"> 0.00717 </td>
   <td style="text-align:left;"> ** </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Scores </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 0.25600 </td>
   <td style="text-align:left;"> ns </td>
   <td style="text-align:right;"> 1.00000 </td>
   <td style="text-align:left;"> ns </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Scores </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 0.00627 </td>
   <td style="text-align:left;"> ** </td>
   <td style="text-align:right;"> 0.03760 </td>
   <td style="text-align:left;"> * </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Scores </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 0.00627 </td>
   <td style="text-align:left;"> ** </td>
   <td style="text-align:right;"> 0.03760 </td>
   <td style="text-align:left;"> * </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Scores </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 0.25600 </td>
   <td style="text-align:left;"> ns </td>
   <td style="text-align:right;"> 1.00000 </td>
   <td style="text-align:left;"> ns </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Scores </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 0.04000 </td>
   <td style="text-align:left;"> * </td>
   <td style="text-align:right;"> 0.24000 </td>
   <td style="text-align:left;"> ns </td>
  </tr>
</tbody>
</table>
**Effect Sizes**

```r
# Effect sizes
C3E9 %>% 
  cohens_d(Scores ~ Group, var.equal = FALSE) %>%
  kable(., "html")
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> .y. </th>
   <th style="text-align:left;"> group1 </th>
   <th style="text-align:left;"> group2 </th>
   <th style="text-align:right;"> effsize </th>
   <th style="text-align:right;"> n1 </th>
   <th style="text-align:right;"> n2 </th>
   <th style="text-align:left;"> magnitude </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Scores </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> -4 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> large </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Scores </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:right;"> -1 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> large </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Scores </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> -3 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> large </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Scores </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> large </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Scores </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> large </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Scores </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> -2 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> large </td>
  </tr>
</tbody>
</table>

**Summary Statistics**

```r
# Summary statistics
C3E9 %>% 
  group_by(Group) %>%
  get_summary_stats() %>%
  kable(., "html")
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> Group </th>
   <th style="text-align:left;"> variable </th>
   <th style="text-align:right;"> n </th>
   <th style="text-align:right;"> min </th>
   <th style="text-align:right;"> max </th>
   <th style="text-align:right;"> median </th>
   <th style="text-align:right;"> q1 </th>
   <th style="text-align:right;"> q3 </th>
   <th style="text-align:right;"> iqr </th>
   <th style="text-align:right;"> mad </th>
   <th style="text-align:right;"> mean </th>
   <th style="text-align:right;"> sd </th>
   <th style="text-align:right;"> se </th>
   <th style="text-align:right;"> ci </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> Scores </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 2.965 </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 1.155 </td>
   <td style="text-align:right;"> 4.968 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> Scores </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 14 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 13 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 2.965 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 1.155 </td>
   <td style="text-align:right;"> 4.968 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> Scores </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 8 </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:right;"> 7 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 2.965 </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 1.155 </td>
   <td style="text-align:right;"> 4.968 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:left;"> Scores </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 8 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 2.965 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 1.155 </td>
   <td style="text-align:right;"> 4.968 </td>
  </tr>
</tbody>
</table>



<!-- To produce a jmv style plot, things get a little trickier. First, we will need to calculate means and confidence intervals. Luckily, `get_summary_stats()` can do this painlessly because of the pipe operator from the tidyverse package. Essentially, the C3E9 data is fed to `group_by()` which will separate the data by Group, then the output is fed in `get_summary_stats()` which will compute descriptive statistics such as means, medians, confidence intervals, and others. The summary_data is then what we will use to generate a plot with the `ggplot()` function.   -->




<!-- The ggplot code is the trickiest part of using this approach. I'll attempt to explain the basic components here, but don't get discouraged if it doesn't make sense. It can take a while to fully get a hold of ggplot. I know it frustrated me plenty when I started with it. First, summary_data is used as the input, because it contains the means and confidence intervals. Next, we will specify what to plot on the x axis and what to plot on the y axis. Then, we will tell ggplot to add a layer of geometric element in the form of error bars. We can then set the ymin and ymax of the error bars as mean-ci and mean+ci as these values are in the input summary_data. Next, we will want to add some points (geom_point), then change their fill, color, and shape. The ggtitle will add a title to our plot, while ylab changes the label on the y axis. To wrap things up, we will add a theme to the plot to change the background, and then set the title to be centered horizontally. -->



### Plot the data  
One way to produce a plot of the data is to use the ggpubr package. The ggpubr package is a wrapper for ggplot2 and serves to simplify the ggplot2 syntax. The only drawback is that it may not have all of the flexibility of ggplot2. However, if you're new to R, ggpubr is a gentle introduction to making publication quality figures in R. In this next code chunk, I use the `ggpubr()` function on our C3E9 data, specify the x and y variables, set add to `"mean"` to plot the means, set `desc_stat = "mean_ci"` to plot the confidence intervals, set `error.plot = "errorbar"` to draw the error bars, and `width = .1` to specify the length of errorbar tips. The rest of the options are straightforward.



```r
ggerrorplot(C3E9,
       x = "Group",
       y = "Scores",
       add = "mean",
       desc_stat = "mean_ci",
       error.plot = "errorbar",
       width = .1,
       color = azl,
       title = "Mean (95% CI)")
```

<div class="figure">
<img src="{{< blogdown/postref >}}index_files/figure-html/ggpubr-1.png" alt="Plot of means and 95% confidence intervals produced by ggpbur." width="672" />
<p class="caption">Figure 2: Plot of means and 95% confidence intervals produced by ggpbur.</p>
</div>
<!-- The color was picked off of the theme which is in themes/github.com/wowchemy/wowchemy-hugo-modules/wowchemy/data/themese/minimial.toml  -->
{{< /tab >}}
{{< /tabs >}}

[Back to tabs](#tests)

### Interpret the output
For the omnibus test, we obtained a significant effect of Group [F(3,8) = 10, p < 0.01] which suggests that the means of the 4 groups are not equal. In other words, one of the treatments may be significantly different than another. To determine where, if any differences between two groups exist, we conducted post-hoc tests on all possible combinations of pairwise comparisons. These tests revealed a significant difference between groups 1 and 2, between groups 1 and 4, and between groups 2 and 3. The results suggest that phobia scores after rational-emotive therapy were lower when compared to psychoanalytic and behavioral therapies. The results also suggest that phobia scores were on average lower after client-centered therapy compared to psychoanalytic therapy. Finally, there were statistically significant differences between rational-emotive and client-centered therapy, but no differences between psychoanalytic and behavioral therapies.

### Wrap up
One of the benefits of the `ANOVA()` function lies in eliminating the need to write the R code to produce a plot of means, confidence intervals, and effect sizes. What is produced with one option within the `ANOVA()` command, takes additional packages and several lines of code to produce with other packages. The `anovaOneW()` function from the jmv package can also be used to perform a one-way ANOVA. However, the `anovaOneW()` is limited in features and is best suited for the Welch's ANOVA. If you're starting out with R, the jmv package can be a nice place to start conducting one-way ANOVA in R.

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

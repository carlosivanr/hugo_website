---
title: One-way Repeated Measures ANOVA
author: Carlos Rodriguez
date: '2021-05-05'
slug: one-way-rm-anova
categories: []
tags: []
subtitle: ''
summary: 'Designs with one within-subjects factor.'
authors: []
lastmod: "July 29 2022"
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

<!-- Prevent the jmv output from wrapping. Make it scrollable horizontally -->
<!-- <style> -->
<!-- pre code, pre, code { -->
<!--   white-space: pre !important; -->
<!--   overflow-x: scroll !important; -->
<!--   word-break: keep-all !important; -->
<!--   word-wrap: initial !important; -->
<!-- } -->
<!-- </style> -->


This guide covers how to conduct one-way repeated measures ANOVA with the jmv and rstatix packages. Repeated measures ANOVAs are used in cases where an observation from the same subject is taken multiple times. For example, an experimenter may wish to examine the changes in scores from a cognitive assessment gathered from the same participants at multiple ages. 

### The data set
The chapter_11_table_5 dataset from the AMCP package is from a hypothetical study that tracked the age-normed general cognitive scores from the McCarthy Scales of Children's Abilities (MSCA) of 12 children at 4 different time points, .


```r
library(AMCP)
library(kableExtra)
# Load data
data("chapter_11_table_5")

# Inspect data
kable(head(chapter_11_table_5))
```

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> Months30 </th>
   <th style="text-align:right;"> Months36 </th>
   <th style="text-align:right;"> Months42 </th>
   <th style="text-align:right;"> Months48 </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 108 </td>
   <td style="text-align:right;"> 96 </td>
   <td style="text-align:right;"> 110 </td>
   <td style="text-align:right;"> 122 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 103 </td>
   <td style="text-align:right;"> 117 </td>
   <td style="text-align:right;"> 127 </td>
   <td style="text-align:right;"> 133 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 96 </td>
   <td style="text-align:right;"> 107 </td>
   <td style="text-align:right;"> 106 </td>
   <td style="text-align:right;"> 107 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 84 </td>
   <td style="text-align:right;"> 85 </td>
   <td style="text-align:right;"> 92 </td>
   <td style="text-align:right;"> 99 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 118 </td>
   <td style="text-align:right;"> 125 </td>
   <td style="text-align:right;"> 125 </td>
   <td style="text-align:right;"> 116 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 110 </td>
   <td style="text-align:right;"> 107 </td>
   <td style="text-align:right;"> 96 </td>
   <td style="text-align:right;"> 91 </td>
  </tr>
</tbody>
</table>

### Perform ANOVA tests {#tests}
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
##    30               -    36                     -4.000000    2.579053    11.00000    -1.5509568    0.1491893   
##                     -    42                     -7.000000    3.045115    11.00000    -2.2987635    0.0421235   
##                     -    48                     -9.000000    3.692745    11.00000    -2.4372115    0.0329854   
##    36               -    42                     -3.000000    2.757909    11.00000    -1.0877807    0.2999517   
##                     -    48                     -5.000000    4.316985    11.00000    -1.1582156    0.2713044   
##    42               -    48                     -2.000000    2.232677    11.00000    -0.8957855    0.3895495   
##  ─────────────────────────────────────────────────────────────────────────────────────────────────────────────
```

<div class="figure">
<img src="{{< blogdown/postref >}}index_files/figure-html/plot1-1.png" alt="anovaRM plot of means by group." width="672" />
<p class="caption">Figure 1: anovaRM plot of means by group.</p>
</div>
{{< /tab >}}


<!-- -----------------------Tab 2---------------------------------- -->
{{< tab tabNum="2" >}}
To perform a repeated measures ANOVA with rstatix, first create a new data frame with the subject id coded by a number. Next, convert the data from wide to long format. Next, convert the subject id and the age information to factor. Then, create the rm_aov object with the `anova_test()` function. This is the same function that was used in the one- and two-way ANOVA guides, but modified slightly. The modifications are found in the options `wid = id` to specify the subjects id factor, `within = age` to specify that our within subjects factor is age, and `effect.size = "pes"` to correspond with the jmv function. Finally, we will run the `get_anova_table()` function on our rm_aov object with `correction = "none"`. In practice, you may want to change the correction setting even if your data do not violate the assumption of sphericity.

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
kable(head(rm_data))
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> id </th>
   <th style="text-align:left;"> age </th>
   <th style="text-align:right;"> score </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> Months30 </td>
   <td style="text-align:right;"> 108 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> Months30 </td>
   <td style="text-align:right;"> 103 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> Months30 </td>
   <td style="text-align:right;"> 96 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:left;"> Months30 </td>
   <td style="text-align:right;"> 84 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 5 </td>
   <td style="text-align:left;"> Months30 </td>
   <td style="text-align:right;"> 118 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 6 </td>
   <td style="text-align:left;"> Months30 </td>
   <td style="text-align:right;"> 110 </td>
  </tr>
</tbody>
</table>

### Conduct repeated measures ANOVA

```r
rm_aov <- anova_test(data = rm_data, 
                     dv = score, 
                     wid = id, 
                     within = age, 
                     effect.size = "pes", 
                     detailed = TRUE)

# Print anova table
kable(get_anova_table(rm_aov, correction = "none"))
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> Effect </th>
   <th style="text-align:right;"> DFn </th>
   <th style="text-align:right;"> DFd </th>
   <th style="text-align:right;"> SSn </th>
   <th style="text-align:right;"> SSd </th>
   <th style="text-align:right;"> F </th>
   <th style="text-align:right;"> p </th>
   <th style="text-align:left;"> p&lt;.05 </th>
   <th style="text-align:right;"> pes </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> (Intercept) </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 559872 </td>
   <td style="text-align:right;"> 6624 </td>
   <td style="text-align:right;"> 929.739 </td>
   <td style="text-align:right;"> 0.000 </td>
   <td style="text-align:left;"> * </td>
   <td style="text-align:right;"> 0.988 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> age </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 33 </td>
   <td style="text-align:right;"> 552 </td>
   <td style="text-align:right;"> 2006 </td>
   <td style="text-align:right;"> 3.027 </td>
   <td style="text-align:right;"> 0.043 </td>
   <td style="text-align:left;"> * </td>
   <td style="text-align:right;"> 0.216 </td>
  </tr>
</tbody>
</table>

### Pairwise comparisons with pairwise_t_test

```r
pwc <- rm_data %>%
  pairwise_t_test(score ~ age, 
                  paired = TRUE, 
                  p.adjust.method = "none")
kable(pwc)
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> .y. </th>
   <th style="text-align:left;"> group1 </th>
   <th style="text-align:left;"> group2 </th>
   <th style="text-align:right;"> n1 </th>
   <th style="text-align:right;"> n2 </th>
   <th style="text-align:right;"> statistic </th>
   <th style="text-align:right;"> df </th>
   <th style="text-align:right;"> p </th>
   <th style="text-align:right;"> p.adj </th>
   <th style="text-align:left;"> p.adj.signif </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> score </td>
   <td style="text-align:left;"> Months30 </td>
   <td style="text-align:left;"> Months36 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> -1.5509568 </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 0.149 </td>
   <td style="text-align:right;"> 0.149 </td>
   <td style="text-align:left;"> ns </td>
  </tr>
  <tr>
   <td style="text-align:left;"> score </td>
   <td style="text-align:left;"> Months30 </td>
   <td style="text-align:left;"> Months42 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> -2.2987635 </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 0.042 </td>
   <td style="text-align:right;"> 0.042 </td>
   <td style="text-align:left;"> * </td>
  </tr>
  <tr>
   <td style="text-align:left;"> score </td>
   <td style="text-align:left;"> Months30 </td>
   <td style="text-align:left;"> Months48 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> -2.4372115 </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 0.033 </td>
   <td style="text-align:right;"> 0.033 </td>
   <td style="text-align:left;"> * </td>
  </tr>
  <tr>
   <td style="text-align:left;"> score </td>
   <td style="text-align:left;"> Months36 </td>
   <td style="text-align:left;"> Months42 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> -1.0877807 </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 0.300 </td>
   <td style="text-align:right;"> 0.300 </td>
   <td style="text-align:left;"> ns </td>
  </tr>
  <tr>
   <td style="text-align:left;"> score </td>
   <td style="text-align:left;"> Months36 </td>
   <td style="text-align:left;"> Months48 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> -1.1582156 </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 0.271 </td>
   <td style="text-align:right;"> 0.271 </td>
   <td style="text-align:left;"> ns </td>
  </tr>
  <tr>
   <td style="text-align:left;"> score </td>
   <td style="text-align:left;"> Months42 </td>
   <td style="text-align:left;"> Months48 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> -0.8957855 </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 0.390 </td>
   <td style="text-align:right;"> 0.390 </td>
   <td style="text-align:left;"> ns </td>
  </tr>
</tbody>
</table>

<!-- ### Pairwise comparisons with emmeans_test -->
<!-- ```{r} -->
<!-- rm.model <- aov(score ~ age + Error(id/age), data = rm_data) -->
<!-- pwc <- rm_data %>% -->
<!--   emmeans_test(score ~ age, -->
<!--                p.adjust.method = "none", -->
<!--                model = rm.model) -->
<!-- kable(pwc) -->
<!-- ``` -->

<!-- ### Nota Bene -->
<!-- jmv implements emmeans_test. Field, Miles, & Field, 2012 use pairwise.t.test() which is the function underlying pairwise_t_test(). Maxwell, Delaney, & Kelly exercise caution for pair wise comparisons with repeated measures designs designs because the approach is much more sensitive to violations of sphericity. Even small departures from sphericity can lead to biased tests. Therefore they recommend a separate variance estimate approach. Howell, stresses this too especially in cases where sphericity is violated. Although a sphericity correction can protect the overall test, it may become problematic with the pairwise comparisons. https://www.uvm.edu/~statdhtx/StatPages/More_Stuff/RepMeasMultComp/RepMeasMultComp.html.  -->

<!-- This may also be an issue in SPSS since it performs paired samples t-tests. -->
<!-- https://statistics.laerd.com/spss-tutorials/one-way-anova-repeated-measures-using-spss-statistics-2.php -->

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
For the omnibus test, we obtain a significant effect of Age [F(33,3) = 3.03, p < 0.05] which suggests that the means of the 4 ages are not equal. The omega squared value is 0.04 which suggest the about 4% of the variance in scores is accounted for by age in the ages studied.

### Wrap up
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

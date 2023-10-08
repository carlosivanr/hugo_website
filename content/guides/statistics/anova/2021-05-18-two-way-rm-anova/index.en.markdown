---
title: Two-way Repeated Measures ANOVA
author: Carlos Rodriguez
date: '2021-05-18'
slug: two-way-rm-anova
categories: []
tags: []
subtitle: ''
summary: 'Designs with two within-subjects factors.'
authors: []
lastmod: "October 05 2022"
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 80
draft: false
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

In this guide, I cover how to conduct two-way repeated measures ANOVA with two within-subjects factors.

### The data set
For this guide, we will use the data from Chapter 12, Table 1 in the AMCP package. In this hypothetical study, 10 subjects are participating in an experiment that tests how visual information can interfere with recognizing letters. Each subject performs a letter recognition task in two conditions, noise absent or present. Noise refers to visual information that is presented along with either a letter T or I that the subject needs to identify. Within each condition, visual information was either presented at 0&deg; (directly in front of the participant), at 4&deg; (slightly offset to one side), or at 8&deg; (even more offset to the side). Thus, noise and degree represent our repeated measures. Finally, the dependent variable in this study is the latency in milliseconds needed to identify the letter.


```r
library(AMCP)

# Load the data
data(chapter_12_table_1)

# Display part of the data
kableExtra::kable(head(chapter_12_table_1))
```

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> Absent0 </th>
   <th style="text-align:right;"> Absent4 </th>
   <th style="text-align:right;"> Absent8 </th>
   <th style="text-align:right;"> Present0 </th>
   <th style="text-align:right;"> Present4 </th>
   <th style="text-align:right;"> Present8 </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 420 </td>
   <td style="text-align:right;"> 420 </td>
   <td style="text-align:right;"> 480 </td>
   <td style="text-align:right;"> 480 </td>
   <td style="text-align:right;"> 600 </td>
   <td style="text-align:right;"> 780 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 420 </td>
   <td style="text-align:right;"> 480 </td>
   <td style="text-align:right;"> 480 </td>
   <td style="text-align:right;"> 360 </td>
   <td style="text-align:right;"> 480 </td>
   <td style="text-align:right;"> 600 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 480 </td>
   <td style="text-align:right;"> 480 </td>
   <td style="text-align:right;"> 540 </td>
   <td style="text-align:right;"> 660 </td>
   <td style="text-align:right;"> 780 </td>
   <td style="text-align:right;"> 780 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 420 </td>
   <td style="text-align:right;"> 540 </td>
   <td style="text-align:right;"> 540 </td>
   <td style="text-align:right;"> 480 </td>
   <td style="text-align:right;"> 780 </td>
   <td style="text-align:right;"> 900 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 540 </td>
   <td style="text-align:right;"> 660 </td>
   <td style="text-align:right;"> 540 </td>
   <td style="text-align:right;"> 480 </td>
   <td style="text-align:right;"> 660 </td>
   <td style="text-align:right;"> 720 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 360 </td>
   <td style="text-align:right;"> 420 </td>
   <td style="text-align:right;"> 360 </td>
   <td style="text-align:right;"> 360 </td>
   <td style="text-align:right;"> 480 </td>
   <td style="text-align:right;"> 540 </td>
  </tr>
</tbody>
</table>

### Perform ANOVA tests {#tests}
<!-- -----------------------TABS---------------------------------- -->
{{< tabs tabTotal="2" tabID="1" tabName1="rstatix" tabName2="lmer" >}}

<!-- -----------------------Tab 1---------------------------------- -->
{{< tab tabNum="1" >}}
<br>

<!-- ```{r, plot1, fig.cap = 'figure caption.', warning=FALSE} -->
<!-- library(jmv) -->
<!-- # Repeated measures anova with jmv -->
<!-- anovaRM( -->
<!--   data = chapter_12_table_1, -->
<!--   rm = list( -->
<!--     list( -->
<!--       label = "Condition", -->
<!--       levels = c("Absent", "Present")), -->
<!--     list( -->
<!--       label = "Angle", -->
<!--       levels = c("0", "4", "8"))), -->
<!--   rmCells = list( -->
<!--     list( -->
<!--       measure = "Absent0", -->
<!--       cell = c("Absent", "0")), -->
<!--     list( -->
<!--       measure = "Absent4", -->
<!--       cell = c("Absent", "4")), -->
<!--     list( -->
<!--       measure = "Absent8", -->
<!--       cell = c("Absent", "8")), -->
<!--     list( -->
<!--       measure = "Present0", -->
<!--       cell = c("Present", "0")), -->
<!--     list( -->
<!--       measure = "Present4", -->
<!--       cell = c("Present", "4")), -->
<!--     list( -->
<!--       measure = "Present8", -->
<!--       cell = c("Present", "8"))), -->
<!--   rmTerms = ~ Condition + Angle + Condition:Angle, -->
<!--   effectSize = 'partEta', -->
<!--   emMeans = list(c("Angle", "Condition")), -->
<!--   emmPlots = TRUE, -->
<!--   depLabel = "Mean Latency") -->
<!-- ``` -->

As we encountered in the guide for [one-way rm-ANOVA](/guides/anova/one-way-anova), we will want to convert our data set from wide format to long format before conducting the statistical test. This situation is a bit trickier because there are many more columns to deal with so I've written a separate walk through that can be found [here](/guides/r/wide-to-long) in order to focus on the procedures for conducting the statistical tests.


```r
library(tidyverse)
library(rstatix)
library(ggpubr)

# Create a new data frame with a subject id
data <- cbind(id = c(1:10), chapter_12_table_1)

# Convert the data from wide to long
data <-  data %>%
  gather(key = Condition.Angle,
         value = Latency,-id) %>%
  separate(col = Condition.Angle,
           into = c("Condition", "Angle"),
           sep = -1) %>%
  arrange(id,
          Condition,
          Angle) %>%
  convert_as_factor(Condition, Angle)

# Conduct repeated measures ANOVA
rm.mod <- anova_test(data = data,
                     dv = Latency, 
                     wid = id, 
                     within = c(Condition, Angle), 
                     effect.size = "pes")

# Print ANOVA table and  Mauchly's Test for Sphericity 
rm.mod
```

```
## ANOVA Table (type III tests)
## 
## $ANOVA
##            Effect DFn DFd      F        p p<.05   pes
## 1       Condition   1   9 33.766 2.56e-04     * 0.790
## 2           Angle   2  18 40.719 2.09e-07     * 0.819
## 3 Condition:Angle   2  18 45.310 9.42e-08     * 0.834
## 
## $`Mauchly's Test for Sphericity`
##            Effect     W     p p<.05
## 1           Angle 0.960 0.850      
## 2 Condition:Angle 0.894 0.638      
## 
## $`Sphericity Corrections`
##            Effect   GGe      DF[GG]    p[GG] p[GG]<.05   HFe      DF[HF]
## 1           Angle 0.962 1.92, 17.31 3.40e-07         * 1.218 2.44, 21.92
## 2 Condition:Angle 0.904 1.81, 16.27 3.45e-07         * 1.118 2.24, 20.12
##      p[HF] p[HF]<.05
## 1 2.09e-07         *
## 2 9.42e-08         *
```




### Interpretation
The results of Mauchly's test indicate that we have not violated the assumption of sphericity and do not require any corrections on the F-values. In addition, the results suggest a significant interaction between noise and condition. As a result, the next step will be to test for simple effects. In other words, we can decompose the significant interaction into testing the effect of angle at each level of noise and then test the effect of noise at each level of angle. Each of these tests can be thought of as a series of one-way repeated measures tests.

**The effect of angle at each level of condition**

Let's begin by carrying out two one-way repeated measures ANOVAs, one for each level of Condition. To help visualize these analyses, we can plot the data with the `ggline()` function. The results of these analyses reveal significant effects of angle when conducted on on absent and present data separately. These significant effects can be further investigated with pair wise comparisons.

```r
data %>% 
  group_by(Condition) %>% 
  anova_test(dv = Latency, wid = id, within = Angle, effect.size = "pes") %>% 
  get_anova_table() %>%
  adjust_pvalue(method = "bonferroni") %>%
  kableExtra::kable()
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> Condition </th>
   <th style="text-align:left;"> Effect </th>
   <th style="text-align:right;"> DFn </th>
   <th style="text-align:right;"> DFd </th>
   <th style="text-align:right;"> F </th>
   <th style="text-align:right;"> p </th>
   <th style="text-align:left;"> p&lt;.05 </th>
   <th style="text-align:right;"> pes </th>
   <th style="text-align:right;"> p.adj </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Absent </td>
   <td style="text-align:left;"> Angle </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 18 </td>
   <td style="text-align:right;"> 5.046 </td>
   <td style="text-align:right;"> 0.018 </td>
   <td style="text-align:left;"> * </td>
   <td style="text-align:right;"> 0.359 </td>
   <td style="text-align:right;"> 0.036 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Present </td>
   <td style="text-align:left;"> Angle </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 18 </td>
   <td style="text-align:right;"> 77.022 </td>
   <td style="text-align:right;"> 0.000 </td>
   <td style="text-align:left;"> * </td>
   <td style="text-align:right;"> 0.895 </td>
   <td style="text-align:right;"> 0.000 </td>
  </tr>
</tbody>
</table>

```r
# Generate Plot 1
ggline(data,
       "Angle", 
       "Latency",
       color = "Condition",
       add = "mean_se",
       palette = "jama",
       position = position_dodge(.2))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-2-1.png" width="672" />

```r
# Pairwise comparisons at each level of Condition
data %>% 
  group_by(Condition) %>%
  pairwise_t_test(
    Latency ~ Angle,
    paired = TRUE,
    p.adjust.method = "bonferroni") %>%
  kableExtra::kable()
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> Condition </th>
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
   <td style="text-align:left;"> Absent </td>
   <td style="text-align:left;"> Latency </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> -2.4494897 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 3.70e-02 </td>
   <td style="text-align:right;"> 1.10e-01 </td>
   <td style="text-align:left;"> ns </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Absent </td>
   <td style="text-align:left;"> Latency </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> -3.4979930 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 7.00e-03 </td>
   <td style="text-align:right;"> 2.00e-02 </td>
   <td style="text-align:left;"> * </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Absent </td>
   <td style="text-align:left;"> Latency </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> -0.7092994 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 4.96e-01 </td>
   <td style="text-align:right;"> 1.00e+00 </td>
   <td style="text-align:left;"> ns </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Present </td>
   <td style="text-align:left;"> Latency </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> -8.5732141 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 1.27e-05 </td>
   <td style="text-align:right;"> 3.81e-05 </td>
   <td style="text-align:left;"> **** </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Present </td>
   <td style="text-align:left;"> Latency </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> -9.9253974 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 3.80e-06 </td>
   <td style="text-align:right;"> 1.14e-05 </td>
   <td style="text-align:left;"> **** </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Present </td>
   <td style="text-align:left;"> Latency </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> -5.6666667 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 3.07e-04 </td>
   <td style="text-align:right;"> 9.21e-04 </td>
   <td style="text-align:left;"> *** </td>
  </tr>
</tbody>
</table>



**The effect of condition at each level of angle**

Now we move towards carrying out three one-way repeated measures ANOVAs, one for each level of Angle. As before, we can plot the data with the `ggline()` function to help with the interpretation of the data. The results of these analyses reveal significant effects of condition when conducted separately at each level of angle. These significant effects are further investigated with pair wise comparisons.

```r
data %>% 
  group_by(Angle) %>% 
  anova_test(dv = Latency, wid = id, within = Condition, effect.size = "pes") %>% 
  get_anova_table() %>%
  adjust_pvalue(method = "bonferroni") %>%
  kableExtra::kable()
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> Angle </th>
   <th style="text-align:left;"> Effect </th>
   <th style="text-align:right;"> DFn </th>
   <th style="text-align:right;"> DFd </th>
   <th style="text-align:right;"> F </th>
   <th style="text-align:right;"> p </th>
   <th style="text-align:left;"> p&lt;.05 </th>
   <th style="text-align:right;"> pes </th>
   <th style="text-align:right;"> p.adj </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> Condition </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 1.552 </td>
   <td style="text-align:right;"> 2.44e-01 </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:right;"> 0.147 </td>
   <td style="text-align:right;"> 7.32e-01 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:left;"> Condition </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 19.737 </td>
   <td style="text-align:right;"> 2.00e-03 </td>
   <td style="text-align:left;"> * </td>
   <td style="text-align:right;"> 0.687 </td>
   <td style="text-align:right;"> 6.00e-03 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:left;"> Condition </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 125.587 </td>
   <td style="text-align:right;"> 1.40e-06 </td>
   <td style="text-align:left;"> * </td>
   <td style="text-align:right;"> 0.933 </td>
   <td style="text-align:right;"> 4.10e-06 </td>
  </tr>
</tbody>
</table>

```r
# Generate Plot 2
ggline(data,
       "Condition", 
       "Latency",
       color = "Angle",
       add = "mean_se",
       palette = "jama",
       position = position_dodge(.2))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-3-1.png" width="672" />

```r
# Pairwise comparisons at each level of Angle
data %>% 
  group_by(Angle) %>%
  pairwise_t_test(
    Latency ~ Condition,
    paired = TRUE,
    p.adjust.method = "bonferroni") %>%
  kableExtra::kable()
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> Angle </th>
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
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> Latency </td>
   <td style="text-align:left;"> Absent </td>
   <td style="text-align:left;"> Present </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> -1.245682 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 2.44e-01 </td>
   <td style="text-align:right;"> 2.44e-01 </td>
   <td style="text-align:left;"> ns </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:left;"> Latency </td>
   <td style="text-align:left;"> Absent </td>
   <td style="text-align:left;"> Present </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> -4.442617 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 2.00e-03 </td>
   <td style="text-align:right;"> 2.00e-03 </td>
   <td style="text-align:left;"> ** </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:left;"> Latency </td>
   <td style="text-align:left;"> Absent </td>
   <td style="text-align:left;"> Present </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> -11.206568 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 1.40e-06 </td>
   <td style="text-align:right;"> 1.40e-06 </td>
   <td style="text-align:left;"> **** </td>
  </tr>
</tbody>
</table>




{{< /tab >}}

{{< tab tabNum="2" >}}
<br>

A repeated measures ANOVA can also be analyzed under a mixed effects framework using the lme4 package. Mixed effects models contain a mixture of fixed and random effects and are also sometimes referred to as multi-level models or hierarchical linear models. Fixed effects can be thought of as factors in which all possible levels that a researcher is interested in are represented in the data. A random effect on the other hand is a factor for which the levels in the experimental data represent a sample of a larger set. Mixed effects models may be advantageous because they do not require the assumption of independence in ANOVA, the assumption of homogeneity of regression slopes in ANCOVA, and may be better suited for handling unbalanced designs or situations with missing data. While mixed effect models are estimated using slightly different procedures than more traditional models, there are cases in which the results will overlap. The balanced repeated measures ANOVA with two within-subjects factors is one of those cases.

The `lmer()` function from the lme4 package is designed to fit linear mixed-effects regression models via REML or maximum likelihood. Just like the base R `lm()` function, `lmer()` takes a formula specifying the dependent variable predicted by (~) a combination of fixed- and random- effect variables. Predictor variables encased in parentheses specify random effects, while variables without parentheses are specified as fixed effects. 

The first part of the formula in the example below specifies to predict Latency from the interaction between Condition and Angle as fixed effects. Including just the interaction term is a shortcut for "Latency ~ Condition + Angle + Condition * Angle." The term "(1|id)" specifies a random intercept for each subject. This allows each subject to have their own mean (starting point) modeled. The remaining terms with the colons indicate random intercepts for the interactions between subject and Condition (1|Condition:id) and subject and Angle (1|Angle:id). The following results will not only mirror those displayed under the rstatix approach, but will also mirror the results generated by SAS.


```r
library(lme4)
library(lmerTest)
library(broom.mixed)

rm.mod <- lmerTest::lmer(Latency ~ Condition + Angle + Condition:Angle + 
                 (1|id) +
                 (1|Condition:id) + 
                 (1|Angle:id), 
               data=data)    

anova(rm.mod)
```

```
## Type III Analysis of Variance Table with Satterthwaite's method
##                 Sum Sq Mean Sq NumDF DenDF F value    Pr(>F)    
## Condition        39169   39169     1     9  33.766  0.000256 ***
## Angle            94468   47234     2    18  40.719 2.087e-07 ***
## Condition:Angle 105120   52560     2    18  45.310 9.424e-08 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

```r
summary(rm.mod)
```

```
## Linear mixed model fit by REML. t-tests use Satterthwaite's method [
## lmerModLmerTest]
## Formula: Latency ~ Condition + Angle + Condition:Angle + (1 | id) + (1 |  
##     Condition:id) + (1 | Angle:id)
##    Data: data
## 
## REML criterion at convergence: 616.1
## 
## Scaled residuals: 
##      Min       1Q   Median       3Q      Max 
## -1.49491 -0.46023  0.02919  0.50472  1.41413 
## 
## Random effects:
##  Groups       Name        Variance Std.Dev.
##  Angle:id     (Intercept) 1200     34.64   
##  Condition:id (Intercept) 2433     49.33   
##  id           (Intercept) 3600     60.00   
##  Residual                 1160     34.06   
## Number of obs: 60, groups:  Angle:id, 30; Condition:id, 20; id, 10
## 
## Fixed effects:
##                         Estimate Std. Error     df t value Pr(>|t|)    
## (Intercept)               462.00      28.97  19.79  15.947 9.28e-13 ***
## ConditionPresent           30.00      26.81  14.08   1.119  0.28183    
## Angle4                     48.00      21.73  28.60   2.209  0.03532 *  
## Angle8                     66.00      21.73  28.60   3.038  0.00505 ** 
## ConditionPresent:Angle4   120.00      21.54  18.00   5.571 2.75e-05 ***
## ConditionPresent:Angle8   204.00      21.54  18.00   9.470 2.05e-08 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Correlation of Fixed Effects:
##             (Intr) CndtnP Angle4 Angle8 CnP:A4
## CondtnPrsnt -0.463                            
## Angle4      -0.375  0.199                     
## Angle8      -0.375  0.199  0.500              
## CndtnPrs:A4  0.186 -0.402 -0.496 -0.248       
## CndtnPrs:A8  0.186 -0.402 -0.248 -0.496  0.500
```

```r
tidy(rm.mod)
```

```
## # A tibble: 10 × 8
##    effect   group        term            estim…¹ std.e…² stati…³    df   p.value
##    <chr>    <chr>        <chr>             <dbl>   <dbl>   <dbl> <dbl>     <dbl>
##  1 fixed    <NA>         (Intercept)       462.     29.0   15.9   19.8  9.28e-13
##  2 fixed    <NA>         ConditionPrese…    30.0    26.8    1.12  14.1  2.82e- 1
##  3 fixed    <NA>         Angle4             48.0    21.7    2.21  28.6  3.53e- 2
##  4 fixed    <NA>         Angle8             66.0    21.7    3.04  28.6  5.05e- 3
##  5 fixed    <NA>         ConditionPrese…   120.     21.5    5.57  18.0  2.75e- 5
##  6 fixed    <NA>         ConditionPrese…   204.     21.5    9.47  18.0  2.05e- 8
##  7 ran_pars Angle:id     sd__(Intercept)    34.6    NA     NA     NA   NA       
##  8 ran_pars Condition:id sd__(Intercept)    49.3    NA     NA     NA   NA       
##  9 ran_pars id           sd__(Intercept)    60.0    NA     NA     NA   NA       
## 10 ran_pars Residual     sd__Observation    34.1    NA     NA     NA   NA       
## # … with abbreviated variable names ¹​estimate, ²​std.error, ³​statistic
```

For these data, the `lmer()` function produces the exact same results as the `anova_test()` function. The 
<!-- Lmer cheat sheet and specifying two within-subjects factors in lmer() -->
<!-- https://stats.stackexchange.com/questions/13784/repeated-measures-anova-with-lme-lmer-in-r-for-two-within-subject-factors -->
<!-- https://stats.stackexchange.com/questions/13166/rs-lmer-cheat-sheet -->

{{< /tab >}}
{{< /tabs >}}

[Back to tabs](#tests)


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

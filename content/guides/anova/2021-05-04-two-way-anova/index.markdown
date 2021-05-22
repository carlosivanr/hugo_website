---
title: Two-way ANOVA
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

<style>
pre code, pre, code {
  white-space: pre !important;
  overflow-x: scroll !important;
  word-break: keep-all !important;
  word-wrap: initial !important;
}
</style>

<style type="text/css">
pre {
  max-height: 310px;
  overflow-y: auto;
}

pre[class] {
  max-height: 100px;
}
</style>



In this guide, we will cover different ways of conducting two-way ANOVAs in R. Two-way ANOVAS are an extension of one-ways ANOVAS and can be used for situations where the goal is to statistically compare means of two or more groups that differ along two categorical variables.

### The data set
We will utilize an example with the chapter_7_table_5 data from the AMCP package. In the example dataset, we have a hypothetical experiment that tests the presence or absence of biofeedback in combination with three different drugs on measures of blood pressure. The Feedback group is coded as a 1 or a 2 where 1 indicates the participants received biofeedback and 2 indicates participants did not. Drug is coded as 1, 2, or 3, and specifies one of three hypothetical drugs that purportedly reduce blood pressure. Finally, Scores refer to the dependent variable and measure blood pressure where lower values are better. This leaves us with a 2x3 between-subjects design. We will also assume that the assumptions of independence, equality of variance, and normality are met for the sample dataset.


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

### Perform ANOVA Tests {#tests}
<!-- -----------------------TABS---------------------------------- -->
{{< tabs tabTotal="2" tabID="1" tabName1="jmv" tabID="2" tabName2="rstatix" tabID="3" tabName3="base R" >}}

<!-- -----------------------Tab 1---------------------------------- -->
{{< tab tabNum="1" >}}  
We will begin by loading the jmv package and use `ANOVA()` function which can be used for both one-way and two-way designs. The following code will set Score as the dependent variable and Feedback and Drug as independent variables. The settings are set to use type III sums of squares, the effect size will be reported as partial eta squared, post-hoc tests will be performed with Feedback and Drug factors, and the plots of the means of Feedback and Drug will be generated.



```r
library(jmv)

# ANOVA test with jmv
ANOVA(formula = Score ~ Feedback * Drug,
      data = chapter_7_table_5,
      ss = "3",
      effectSize = 'partEta',
      postHoc = c('Feedback', 'Drug'),
      postHocCorr = 'none',
      emMeans = ~ Feedback + Drug,
      emmPlots = TRUE,
      emmTables = TRUE,
      ciWidthEmm = 95
      )
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
## 
## 
##  ESTIMATED MARGINAL MEANS
## 
##  FEEDBACK
## 
##  Estimated Marginal Means - Feedback                          
##  ──────────────────────────────────────────────────────────── 
##    Feedback    Mean        SE          Lower       Upper      
##  ──────────────────────────────────────────────────────────── 
##    1           187.0000    3.222318    180.3495    193.6505   
##    2           199.0000    3.222318    192.3495    205.6505   
##  ──────────────────────────────────────────────────────────── 
## 
## 
##  DRUG
## 
##  Estimated Marginal Means - Drug                          
##  ──────────────────────────────────────────────────────── 
##    Drug    Mean        SE          Lower       Upper      
##  ──────────────────────────────────────────────────────── 
##    1       178.0000    3.946517    169.8548    186.1452   
##    2       202.0000    3.946517    193.8548    210.1452   
##    3       199.0000    3.946517    190.8548    207.1452   
##  ────────────────────────────────────────────────────────
```

<div class="figure">
<img src="{{< blogdown/postref >}}index_files/figure-html/plot1-1.png" alt="Estimated marginal means and confidence intervals." width="672" />
<p class="caption">Figure 1: Estimated marginal means and confidence intervals.</p>
</div><div class="figure">
<img src="{{< blogdown/postref >}}index_files/figure-html/plot1-2.png" alt="Estimated marginal means and confidence intervals." width="672" />
<p class="caption">Figure 2: Estimated marginal means and confidence intervals.</p>
</div>

<!-- #### Comparisons of Cell Means with jmv -->
<!-- Where things get a little more complicated for jmv, is in the comparisons of cell means. These would test all pairwise combinations of Drug within Factor for this example. The following code will produce the results of interest, but it will also produce quite a bit of output and so the code is commented -->

{{< /tab >}}

<!-- -----------------------Tab 2---------------------------------- -->
{{< tab tabNum="2" >}}  
When using rstatix, it's useful to load the tidyverse package as well. This will make it so that we can use the pipe operator (`%>%`) and the `group_by()` function. Once we load our data, we will first want to convert Feedback and Drug to factor so the values get treated as categorical variable. Next, we will build an ANOVA model with the base R `aov()` function that predicts Score by Feedback and Drug. Finally, we will use the rstatix `anova_test()` function on our aov model to produce the output for the omnibus test.

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
The results from the omnibus test reveal that there is no significant interaction between Drug and Feedback. On the other hand, there are significant main effects for Drug and for Feedback. Because the interaction is not significant, may proceed to perform tests of marginal means. Had the interaction been significant, we could have opted to perfrom tests of simple effects of Drug within Feedback, or Feedback within drug. However, these are primarily suggestions and the approach to analyzing the data should be guided by the research question.

#### Tests of Marginal Means
The rstatix package includes a function, `emmeans_test()`, that can perform tests of estimated marginal means. To perform these tests, we will use the aov model that we created in the previous step and we will conduct two separate tests, one for Feedback and one for Drug. The correction method in this example is set to `"none"`, but this can be easily changed according to your situation. The available correction methods can be found by typing `help(anova_test)` in the Console.

We will also use the `get_summary_stats()` function and illustrate how the tidyverse package meshes with rstatix to produce descriptive statistics to aid in the interpretation of the output. First we will start with the chapter_7_table_5 data and pipe it to the `group_by()` function which will create subsets of data according to combinations of Drug and Feedback, then that output is fed into the `get_summary_stats()` which will calculate several descriptive statistics for each group. Finally, to display only a part of this output, the `select()` function will display data by specifying column names.

To plot the data we will rely on the ggpubr package. The ggpubr and rstatix packages are developed by the same individual and are aimed at simplifying the syntax for conducting statistical tests and generating plots in R. For our purposes we will use the `ggerrorplot()` function to plot means and confidence intervals.

#### Feedback

```r
# Test of marginal means for Feedback
pwc <- chapter_7_table_5 %>% 
  emmeans_test(Score ~ Feedback, 
               p.adjust.method = "none", 
               model = model)
```

```
## NOTE: Results may be misleading due to involvement in interactions
```

```r
# Print the results of the pairwise comparisons of Feedback
pwc
```

```
## # A tibble: 1 x 8
##   .y.   group1 group2    df statistic      p  p.adj p.adj.signif
## * <chr> <chr>  <chr>  <dbl>     <dbl>  <dbl>  <dbl> <chr>       
## 1 Score 1      2         24     -2.63 0.0146 0.0146 *
```

```r
# Print the estimated marginal means of Feedback
get_emmeans(pwc)
```

```
## # A tibble: 2 x 7
##   Feedback emmean    se    df conf.low conf.high method      
##   <fct>     <dbl> <dbl> <dbl>    <dbl>     <dbl> <chr>       
## 1 1           187  3.22    24     180.      194. Emmeans test
## 2 2           199  3.22    24     192.      206. Emmeans test
```

```r
# Modify pwc to include x and y positions for plotting signficance markers
pwc <- pwc %>% add_xy_position(x = "Feedback", fun = "mean_ci")

# Generate an error plot uses ggpubr AND ggplot functions to layer the plot
ggerrorplot(get_emmeans(pwc), 
       x = "Feedback", 
       y = "emmean",
       ylab = "Mean BP Scores") +
    geom_errorbar(aes(ymin = conf.low, ymax = conf.high), 
                  width = 0.1) +
    stat_pvalue_manual(pwc, hide.ns = TRUE, tip.length = FALSE)
```

<div class="figure">
<img src="{{< blogdown/postref >}}index_files/figure-html/plot3-1.png" alt="Means and confidence intervals for Feedback collapsed across Drug. *, p&lt;0.05; **, p&lt;0.01; ***, p&lt;0.001; ns, not significant. n.b. The confidence intervals plotted by the ggpubr and jmv packages are based on estimated marginal means. Note that the standard errors are the same for all levels of Feedback. In some situations, you may want to plot the cell means and the corresponding confidence intervals instead. To plot the cell means with ggpubr, see the code chunk below. " width="672" />
<p class="caption">Figure 3: Means and confidence intervals for Feedback collapsed across Drug. *, p<0.05; **, p<0.01; ***, p<0.001; ns, not significant. n.b. The confidence intervals plotted by the ggpubr and jmv packages are based on estimated marginal means. Note that the standard errors are the same for all levels of Feedback. In some situations, you may want to plot the cell means and the corresponding confidence intervals instead. To plot the cell means with ggpubr, see the code chunk below. </p>
</div>

#### Plot cell means instead

```r
#Produce an error plot with cell means and ci instead of estimated marginal means
#The same code chunk can be used to get cell means of Drug by changing the "x = " argument
ggerrorplot(chapter_7_table_5,
       x = "Feedback",
       y = "Score",
       add = "mean",
       desc_stat = "mean_ci",
       error.plot = "errorbar",
       width = .1)

#Print cell mean, standard deviation, and standard error for each level of Feedback
chapter_7_table_5 %>%
  group_by(Feedback) %>%
  get_summary_stats(Score) %>%
  select(Feedback, mean, sd, se)
```


#### Drug

```r
# Test of marginal means for Drug
pwc <- chapter_7_table_5 %>% 
  emmeans_test(Score ~ Drug, 
               p.adjust.method = "none", 
               model = model)
```

```
## NOTE: Results may be misleading due to involvement in interactions
```

```r
# Print the results of the pairwise comparisons of Feedback
pwc
```

```
## # A tibble: 3 x 8
##   .y.   group1 group2    df statistic        p    p.adj p.adj.signif
## * <chr> <chr>  <chr>  <dbl>     <dbl>    <dbl>    <dbl> <chr>       
## 1 Score 1      2         24    -4.30  0.000246 0.000246 ***         
## 2 Score 1      3         24    -3.76  0.000958 0.000958 ***         
## 3 Score 2      3         24     0.538 0.596    0.596    ns
```

```r
# Print the estimated marginal means of Feedback
get_emmeans(pwc)
```

```
## # A tibble: 3 x 7
##   Drug  emmean    se    df conf.low conf.high method      
##   <fct>  <dbl> <dbl> <dbl>    <dbl>     <dbl> <chr>       
## 1 1        178  3.95    24     170.      186. Emmeans test
## 2 2        202  3.95    24     194.      210. Emmeans test
## 3 3        199  3.95    24     191.      207. Emmeans test
```

```r
# Modify pwc to include x and y positions for plotting signficance markers
pwc <- pwc %>% add_xy_position(x = "Feedback", fun = "mean_ci")

# Generate an error plot uses ggpubr AND ggplot functions to layer the plot
ggerrorplot(get_emmeans(pwc), 
       x = "Drug", 
       y = "emmean",
       ylab = "Mean BP Scores") +
    geom_errorbar(aes(ymin = conf.low, ymax = conf.high), 
                  width = 0.1) +
    stat_pvalue_manual(pwc, hide.ns = FALSE, tip.length = FALSE)
```

<div class="figure">
<img src="{{< blogdown/postref >}}index_files/figure-html/plot4-1.png" alt="Means and confidence intervals for Drug collapsed across Feedback. *, p&lt;0.05; **, p&lt;0.01; ***, p&lt;0.001; ns, not significant" width="672" />
<p class="caption">Figure 4: Means and confidence intervals for Drug collapsed across Feedback. *, p<0.05; **, p<0.01; ***, p<0.001; ns, not significant</p>
</div>

The two tests of marginal means will produce a couple of messages to remind us that the results could be misleading because of interactions. However, as we saw in the omnibus test, there was no significant interaction and can disregard the messages. The results of the `emmeans_test()` on Feedback indicate a significant effect and suggests that the participants undergoing biofeedback had lower bloodpressure scores than those without biofeedback. When examining the output of the results of the `emmeans_test()` on Drug, we see that the mean of Drug 1 is significantly lower than that of Drug 2 and Drug 3. However, the mean of Drug 2 is not significantly different than that of Drug 3.

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

[Back to tabs](#tests)

### Wrap-up
The jmv and rstatix functions both produce the same results, as they should because they share many of the underlying statistical functions. The jmv package is a great place to start with statistical tests if you are beginning with R. One reason for this is because it simplifies some of the syntax, generates plots automatically, and it can automatically convert some of your numerical data to categorical. While it's a lot easier to code a 2x3 ANOVA with jmv there are some advantages to using rstatix and ggpubr. One advantage is that the ggpubr functions are designed to take in arguments from rstatix that make plotting significance markers relatively straightforward. In addition, ggpubr plots can be used as a starting point layer ggplot geometric elements like the `geom_errorbar()` element in Figures 3 and 4. 


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

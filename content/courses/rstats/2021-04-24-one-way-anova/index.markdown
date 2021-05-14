---
title: One-way ANOVA in R
author: Carlos Rodriguez
date: '2021-04-24'
slug: one-way-anova
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-04-24T21:53:52-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 10
---

In this module, I will demonstrate how to perform a one-way ANOVA with the jmv and rstatix packages and with the base R functions. 


### The data set
For this demo, we will use the AMCP package. AMCP is the R package for Maxwell, Delaney, and Kelley's 3rd edition of <a href="https://designingexperiments.com/"> "Designing Experiments and Analyzing Data: A model comparison perspective."</a> For this demo we will use the data from Chapter 3, Exercise 9. In this exercise, a psychologist assigned 12 subjects to one of 4 different psychological treatments. These treatments consisted of rational-emotive, psychoanalytic, client-centered, and behavioral therapies. The 4 different treatments were used to investigate which therapy is more effective which can be tested with a one-way ANOVA.

For these data, Group represents the type of therapy the participant was randomly assigned to. Scores represent the score from a post-therapy fear scale where higher numbers indicate higher levels of phobia. Finally, each of the 12 rows represent each subject. The `head()` function in the code will only display data for the first six rows.

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

### Perform ANOVA Tests
<!-- -----------------------TABS---------------------------------- -->
{{< tabs tabTotal="2" tabID="1" tabName1="jmv" tabName2="rstatix" tabName3="base R"  >}}

<!-- -----------------------Tab 1---------------------------------- -->
{{< tab tabNum="1" >}}
Jmv is a package that comes from the standalone jamovi statistical spreadsheet software. <a href="https://jamovi.org/"> Jamovi </a> was designed as an alternative to costly programs like SPSS or SAS and runs R underneath the hood. The developers of jamovi also released an R package with all of the functions of their standalone program. As you'll see, jmv produces well organized output and can automatically generate plots. It serves as a great way run statistical tests in R for beginners.  

With the `anovaOneW()` function we will predict Scores by Group (`Scores ~ Group`), set the data to be analyzed as C3E9, set `fishers = TRUE` and `welchs = FALSE`, otherwise the function will run the default Welch's ANOVA. We will also set the `phMethod = 'tukey'` to conduct posthoc tests, Lastly, we want to set `descPlot = TRUE` to plot means and confidence intervals.


```r
library(jmv)
library(AMCP)

# Load the data
data(C3E9)

# Conduct ANOVA test
anovaOneW(formula = Scores ~ Group, 
          data = C3E9, 
          fishers = TRUE, 
          welchs = FALSE, 
          descPlot = TRUE,
          phMethod = 'tukey'
          )
```

```
## 
##  ONE-WAY ANOVA
## 
##  One-Way ANOVA (Fisher's)                          
##  ------------------------------------------------- 
##              F           df1    df2    p           
##  ------------------------------------------------- 
##    Scores    10.00000      3      8    0.0044074   
##  ------------------------------------------------- 
## 
## 
##  POST HOC TESTS
## 
##  Tukey Post-Hoc Test – Scores                                                 
##  ---------------------------------------------------------------------------- 
##                            1            2            3            4           
##  ---------------------------------------------------------------------------- 
##    1    Mean difference            —    -8.000000    -2.000000    -6.000000   
##         p-value                    —    0.0052339    0.6297636    0.0259193   
##                                                                               
##    2    Mean difference                         —     6.000000     2.000000   
##         p-value                                 —    0.0259193    0.6297636   
##                                                                               
##    3    Mean difference                                      —    -4.000000   
##         p-value                                              —    0.1441838   
##                                                                               
##    4    Mean difference                                                   —   
##         p-value                                                           —   
##  ----------------------------------------------------------------------------
```

<div class="figure">
<img src="{{< blogdown/postref >}}index_files/figure-html/plot1-1.png" alt="anovaOneW plot of means and 95% confidence intervals by group." width="672" />
<p class="caption">Figure 1: anovaOneW plot of means and 95% confidence intervals by group.</p>
</div>


{{< /tab >}}
<!-- -----------------------Tab 2---------------------------------- -->
{{< tab tabNum="2" >}}
The rstatix package is another way of programming statistical tests in R. One of the benefits of the rstatix package is that it meshes well with the pipe (`%>%`) operator from the tidyverse package. This means that you could potentially use the `group_by()` function on your data to facilitate conducting pairwise comparisons, generating summary data, and other statistical computations like calculating effect sizes. We'll see an example of this in the code chunk below. The same developer of the rstatix package also developed the ggpubr package which simplifies producing ggplot2 figures. In this demo, the ggpubr package is loaded primarily to simplify producing a jamovi style plot.

The sample code of conducting the `anova_test()` is not too different than the `anovaOneW()` function in jmv. However, for this approach, we will need to change our Group variable to factor, otherwise `anova_test()` will think we will want to predict scores by a numeric variable rather than a categorical one. Next, we specify a formula predicting Scores by Group (`Scores ~ Group`). Then we tell the function that we want to analyze the C3E9 data, specify our dependent variable (dv; Scores), set the effect size output to partial eta squared ("pes"), and then set the sum of squares method to type III.


```r
library(AMCP)
library(rstatix)
library(ggpubr)

# Load data
data(C3E9)

# Convert group to factor
C3E9$Group <- as.factor(C3E9$Group)

# Conduct ANOVA test
anova_test(Scores ~ Group, 
           data = C3E9, 
           dv = Scores, 
           effect.size = "pes", 
           type = 3
           )
```

```
## ANOVA Table (type III tests)
## 
##   Effect DFn DFd  F     p p<.05   pes
## 1  Group   3   8 10 0.004     * 0.789
```
### Posthoc Tests
To get the output for the post-hoc tests, we will run the `tukey_hsd()` function on the same data with the same formula (`Scores ~ Group`)

```r
# Tukey posthoc tests
C3E9 %>%
  tukey_hsd(Scores ~ Group) %>%
  select(-null.value)
```

```r
## # A tibble: 6 x 8
##   term  group1 group2 estimate conf.low conf.high   p.adj p.adj.signif
##   <chr> <chr>  <chr>     <dbl>    <dbl>     <dbl>   <dbl> <chr>       
## 1 Group 1      2          8.00    2.77     13.2   0.00523 **          
## 2 Group 1      3          2.00   -3.23      7.23  0.63    ns          
## 3 Group 1      4          6.00    0.771    11.2   0.0259  *           
## 4 Group 2      3         -6.00  -11.2      -0.771 0.0259  *           
## 5 Group 2      4         -2.00   -7.23      3.23  0.63    ns          
## 6 Group 3      4          4      -1.23      9.23  0.144   ns
```


```r
# Bonferroni corrected posthoc tests
C3E9 %>% 
  pairwise_t_test(Scores ~ Group, p.adjust.method = "bonferroni")
```

```
## # A tibble: 6 x 9
##   .y.    group1 group2    n1    n2       p p.signif   p.adj p.adj.signif
## * <chr>  <chr>  <chr>  <int> <int>   <dbl> <chr>      <dbl> <chr>       
## 1 Scores 1      2          3     3 0.00120 **       0.00717 **          
## 2 Scores 1      3          3     3 0.256   ns       1       ns          
## 3 Scores 2      3          3     3 0.00627 **       0.0376  *           
## 4 Scores 1      4          3     3 0.00627 **       0.0376  *           
## 5 Scores 2      4          3     3 0.256   ns       1       ns          
## 6 Scores 3      4          3     3 0.04    *        0.24    ns
```

<!-- To produce a jmv style plot, things get a little trickier. First, we will need to calculate means and confidence intervals. Luckily, `get_summary_stats()` can do this painlessly because of the pipe operator from the tidyverse package. Essentially, the C3E9 data is fed to `group_by()` which will separate the data by Group, then the output is fed in `get_summary_stats()` which will compute descriptive statistics such as means, medians, confidence intervals, and others. The summary_data is then what we will use to generate a plot with the `ggplot()` function.   -->




<!-- The ggplot code is the trickiest part of using this approach. I'll attempt to explain the basic components here, but don't get discouraged if it doesn't make sense. It can take a while to fully get a hold of ggplot. I know it frustrated me plenty when I started with it. First, summary_data is used as the input, because it contains the means and confidence intervals. Next, we will specify what to plot on the x axis and what to plot on the y axis. Then, we will tell ggplot to add a layer of geometric element in the form of error bars. We can then set the ymin and ymax of the error bars as mean-ci and mean+ci as these values are in the input summary_data. Next, we will want to add some points (geom_point), then change their fill, color, and shape. The ggtitle will add a title to our plot, while ylab changes the label on the y axis. To wrap things up, we will add a theme to the plot to change the background, and then set the title to be centered horizontally. -->



### Plot the data  

One way to produce a plot of the data is to use the ggpubr package. The ggpubr package is a wrapper for ggplot2 and serves to simplify the ggplot2 syntax. The only drawback is that it may not have all of the flexibility of ggplot2. However, if you're new to R, ggpubr is a gentle introduction to making figure. In this next code chunk, I use the `ggpubr()` function on our C3E9 data, specify the x and y variables, set add to `"mean"` to plot the means, set `desc_stat = "mean_ci"` to plot the confidence intervals, set `error.plot = "errorbar"` to draw the error bars, and `width = .1` to specify the length of errorbar tips. The rest of the options are straightforward.

```r
ggerrorplot(C3E9,
       x = "Group",
       y = "Scores",
       add = "mean",
       desc_stat = "mean_ci",
       error.plot = "errorbar",
       width = .1,
       color = "blue",
       title = "Mean (95% CI)",
)
```

<div class="figure">
<img src="{{< blogdown/postref >}}index_files/figure-html/ggpubr-1.png" alt="Plot of means and 95% confidence intervals produced by ggpbur." width="672" />
<p class="caption">Figure 2: Plot of means and 95% confidence intervals produced by ggpbur.</p>
</div>
{{< /tab >}}

<!-- -----------------------Tab 3---------------------------------- -->
{{< tab tabNum="3" >}}
####    
The same data can be analyzed with the base R functions which produce the same results when using the `aov()` function. Again, we will predict Scores by Group. However, because Group is of numerical class, we will need to convert it to factor for the `aov()` function to work properly. Lastly, we will need to encase our `aov()` function within the `summary()` function to produce the output we are interested in. A plot of the data can be recreated using the code in the rstatix approach.


```r
# Print the summary of the
summary(
  aov(formula = Scores ~ as.factor(Group), 
      data = C3E9)
        )
```

```
##                  Df Sum Sq Mean Sq F value  Pr(>F)   
## as.factor(Group)  3    120      40      10 0.00441 **
## Residuals         8     32       4                   
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

```r
# Tukey post-hoc tests for the aov()
TukeyHSD(
  aov(formula = Scores ~ as.factor(Group), 
            data = C3E9)
         )
```

```
##   Tukey multiple comparisons of means
##     95% family-wise confidence level
## 
## Fit: aov(formula = Scores ~ as.factor(Group), data = C3E9)
## 
## $`as.factor(Group)`
##     diff         lwr        upr     p adj
## 2-1    8   2.7705811 13.2294189 0.0052339
## 3-1    2  -3.2294189  7.2294189 0.6297636
## 4-1    6   0.7705811 11.2294189 0.0259193
## 3-2   -6 -11.2294189 -0.7705811 0.0259193
## 4-2   -2  -7.2294189  3.2294189 0.6297636
## 4-3    4  -1.2294189  9.2294189 0.1441838
```


{{< /tab >}}
{{< /tabs >}}

### Interpret the output
For the omnibus test, we obtain a significant effect of Group [F(3,8) = 10, p < 0.01] which suggests that the means of the 4 groups are not equal. In other words, one of the treatments may be significantly different than another. The post-hoc tests that perform all possible combinations of pairwise comparisons also indicate a significant difference between group 1 and 2, between group 1 and 4, and between group 2 and 3.  

### Wrap Up
One of the benefits of the `anovaOneW()` function lies in eliminating the need to write code to produce a plot of means and confidence intervals. What is produced with one option within the `anovaOneW()` command, takes additional packages and several lines of code to produce with ggplot. It's main disadvantage is that `anovaOneW()` is limited in it post-hoc tests options.  You can select either 'tukey' or none for a traditional one-way ANOVA, but you could get additional correction methods with the `ANOVA()` in jmv. If you're starting out with R, the jmv package will surely give you a head start in terms of analyzing and visualizing simple one-way ANOVA tests.

---
title: Correlation in R
author: Carlos Rodriguez
date: '2021-10-08'
slug: correlation-in-r
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-10-08T05:50:09-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 10
draft: false
---


In this guide, I will walk through how to use the rstatix package to perform Pearson product moment correlations in R. I will use a sample data file from the first edition of ["Discovering Statistics Using R"](https://www.discoveringstatistics.com/books/discovering-statistics-using-r/) by Field, Miles, and Field [^1]. The sample data contain scores from a hypothetical anxiety measure, test scores, and the number of hours spent studying (revising) before the exam.

### Load packages

```r
library(tidyverse)  # for data importing and visualization
library(rstatix)    # for performing statistics
library(kableExtra) # for displaying tables
```

### Load data

```r
data <- read.table(file = "Exam Anxiety.dat", header = TRUE)
kable(head(data))
```

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> Code </th>
   <th style="text-align:right;"> Revise </th>
   <th style="text-align:right;"> Exam </th>
   <th style="text-align:right;"> Anxiety </th>
   <th style="text-align:left;"> Gender </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 40 </td>
   <td style="text-align:right;"> 86.298 </td>
   <td style="text-align:left;"> Male </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 65 </td>
   <td style="text-align:right;"> 88.716 </td>
   <td style="text-align:left;"> Female </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 27 </td>
   <td style="text-align:right;"> 80 </td>
   <td style="text-align:right;"> 70.178 </td>
   <td style="text-align:left;"> Male </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 53 </td>
   <td style="text-align:right;"> 80 </td>
   <td style="text-align:right;"> 61.312 </td>
   <td style="text-align:left;"> Male </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 40 </td>
   <td style="text-align:right;"> 89.522 </td>
   <td style="text-align:left;"> Male </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> 22 </td>
   <td style="text-align:right;"> 70 </td>
   <td style="text-align:right;"> 60.506 </td>
   <td style="text-align:left;"> Female </td>
  </tr>
</tbody>
</table>

### Visualize the data
A quick visualization of the exam and anxiety scores indicates that there is a relationship between these two variables. As pre-test anxiety goes up, exam performance declines.

```r
ggplot(data, aes(x = Anxiety, y = Exam)) +
  geom_point(alpha = 0.7, color = "#1565c0") +
  geom_smooth(method = "lm", se = FALSE, color = "#1565c0") +
  theme_minimal() +
  theme(axis.line = element_line(color = "grey70"))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-3-1.png" width="672" />

When visualizing the anxiety and the number of hour spent studying, we see a more dramatic relationship.

```r
ggplot(data, aes(x = Anxiety, y = Revise)) +
  geom_point(alpha = 0.7, color = "#1565c0") +
  geom_smooth(method = "lm", se = FALSE, color = "#1565c0") +
  theme_minimal() +
  theme(axis.line = element_line(color = "grey70"))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-4-1.png" width="672" />


### Correlations with rstatix
To perform the statistical test of significance for correlations, I like to use the rstatix package and its `cor_test()` function. What I have done here is encapsulated the output from the `cor_test()` function with the `kable()` function to print  a nicer table as compared to the plain output. The cor_test() function can take multiple variables that need to be tested through the vars and vars2 arguments. Simply add additional columns if they are in an analysis. `cor_test()` can also perfrom multiple types of correlation tests including the Pearson product moment correlation, Spearman's rank correlation, and Kendall's tau (non-parametric). The function can also take a [use = "pairwise.complete.obs"] argument to include only data that have complete observations. This is a useful feature in cases where you may have missing data. Finally, although it is not shown, the function can take an argument to specify confidence intervals. The output produces a correlation value, a test statistic, a p value, and confidence intervals.

```r
correlations <- cor_test(data,
                         vars = c("Anxiety"),
                         vars2 = c("Exam", "Revise"),
                         method = "pearson",
                         use = "pairwise.complete.obs")
kable(correlations)
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> var1 </th>
   <th style="text-align:left;"> var2 </th>
   <th style="text-align:right;"> cor </th>
   <th style="text-align:right;"> statistic </th>
   <th style="text-align:right;"> p </th>
   <th style="text-align:right;"> conf.low </th>
   <th style="text-align:right;"> conf.high </th>
   <th style="text-align:left;"> method </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Anxiety </td>
   <td style="text-align:left;"> Exam </td>
   <td style="text-align:right;"> -0.44 </td>
   <td style="text-align:right;"> -4.938026 </td>
   <td style="text-align:right;"> 3.1e-06 </td>
   <td style="text-align:right;"> -0.5846244 </td>
   <td style="text-align:right;"> -0.2705591 </td>
   <td style="text-align:left;"> Pearson </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Anxiety </td>
   <td style="text-align:left;"> Revise </td>
   <td style="text-align:right;"> -0.71 </td>
   <td style="text-align:right;"> -10.111055 </td>
   <td style="text-align:right;"> 0.0e+00 </td>
   <td style="text-align:right;"> -0.7938168 </td>
   <td style="text-align:right;"> -0.5977733 </td>
   <td style="text-align:left;"> Pearson </td>
  </tr>
</tbody>
</table>

### Coefficients of Determination
The coefficient of determination or R squared value is one way to help interpret correlation values. To calculate the coefficient of determination we simply square the correlation values. The coefficient of determination can also be multiplied by 100 to obtain a percentage that assess the amount of variance in one variable that can be accounted by another. In order to do this in R we can use the `mutate()` function from the tidyverse package. This function will display two new columns, cod and percnt, in the correlations data frame with these values.

```r
# Square the R values and convert to percent
kable(correlations %>% mutate(cod = cor^2, percnt = cor^2*100))
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> var1 </th>
   <th style="text-align:left;"> var2 </th>
   <th style="text-align:right;"> cor </th>
   <th style="text-align:right;"> statistic </th>
   <th style="text-align:right;"> p </th>
   <th style="text-align:right;"> conf.low </th>
   <th style="text-align:right;"> conf.high </th>
   <th style="text-align:left;"> method </th>
   <th style="text-align:right;"> cod </th>
   <th style="text-align:right;"> percnt </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Anxiety </td>
   <td style="text-align:left;"> Exam </td>
   <td style="text-align:right;"> -0.44 </td>
   <td style="text-align:right;"> -4.938026 </td>
   <td style="text-align:right;"> 3.1e-06 </td>
   <td style="text-align:right;"> -0.5846244 </td>
   <td style="text-align:right;"> -0.2705591 </td>
   <td style="text-align:left;"> Pearson </td>
   <td style="text-align:right;"> 0.1936 </td>
   <td style="text-align:right;"> 19.36 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Anxiety </td>
   <td style="text-align:left;"> Revise </td>
   <td style="text-align:right;"> -0.71 </td>
   <td style="text-align:right;"> -10.111055 </td>
   <td style="text-align:right;"> 0.0e+00 </td>
   <td style="text-align:right;"> -0.7938168 </td>
   <td style="text-align:right;"> -0.5977733 </td>
   <td style="text-align:left;"> Pearson </td>
   <td style="text-align:right;"> 0.5041 </td>
   <td style="text-align:right;"> 50.41 </td>
  </tr>
</tbody>
</table>

### Interpretation
In this guide, we used sample data to reveal a negative relationship between pre-test anxiety and exam performance. Additionally, we also noticed a negative relationship between pre-test anxiety and the amount of hours spent studying. Both of these relationships are satistically significant, but in this case we would also want to pay attention to the confidence intervals. The confidence intervals in the two correlations do no cross zero, which indicate that the value of the correlations in these two relationships is likely to be negative in the population. Pre-test anxiety accounts for about 19% of the variability exams scores while the number of hours spent studying account for about 50% of the variability in exams scores. In this example, we did not examine the relationship between the number of hours spent studying and exam scores which may help explain some of the variance that is still unaccounted for. We also did not examine how these relationships may differ when separated by gender. However, the main goal of this guide was to showcase the `cor_test()` function from the rstatix package to perform Pearson product moment correlations.

### References
<!-- Need references for Andy Field and kableExtra packages -->

<div id="refs" class="references">

<div id="ref-AMCP">

Field, Andy, Jeremy Miles, and Zoe Field. 2012. *Discovering Statistics Using R*. Sage.

</div>


<div id="ref-R-rstatix">

Kassambara, Alboukadel. 2020. *Rstatix: Pipe-Friendly Framework for Basic Statistical Tests*. <https://CRAN.R-project.org/package=rstatix>.

</div>


<div id="ref-R-tidyverse">

Wickham, Hadley. 2019. *Tidyverse: Easily Install and Load the ’Tidyverse’*. <https://CRAN.R-project.org/package=tidyverse>.

</div>

</div>

### Footnotes
[^1]: DSUR is an excellent introductory resource for learning more about the theory, background, and execution of several statistical analyses including correlation, regression, t-tests, and analysis of variance in R. At the time of this writing, the second edition is slated to be released in 2022 which should have some welcome updates to new R syntax, packages, and functions. I am definitely looking forward to getting a copy for myself when it is released.

<!-- # ```{r, warning=FALSE, message=FALSE} -->
<!-- # # Load Packages #### -->
<!-- # library(tidyverse)  # for data importing and visualization -->
<!-- # library(rstatix)    # for performing statistics -->
<!-- # library(ggpubr)     # for creating graphs -->
<!-- # library(kableExtra) # for displaying tables -->
<!-- #  -->
<!-- # # Load Data -------------------------------------------------------------------- -->
<!-- # # List and load the graph data, will be fed in wide format -->
<!-- # data <- read.csv("Ecommerce Customers") -->
<!-- #  -->
<!-- # kable(head(data, n = 3)) -->
<!-- #  -->
<!-- #  -->
<!-- #  -->
<!-- # # EDA -------------------------------------------------------------------------- -->
<!-- # # Scatter plot -->
<!-- # ggplot(data, aes(x = Length.of.Membership, y = Yearly.Amount.Spent)) + -->
<!-- #   geom_point(alpha = 0.7, color = "#1565c0") + -->
<!-- #   geom_smooth(method = "lm", color = "black") -->
<!-- # ``` -->
<!-- # ```{r} -->
<!-- # kable( -->
<!-- #   cor_test(data,  -->
<!-- #          vars = c("Length.of.Membership"),  -->
<!-- #          vars2 = c("Yearly.Amount.Spent", "Time.on.App"),  -->
<!-- #          method = "pearson",  -->
<!-- #          use = "pairwise.complete.obs") -->
<!-- # ) -->
<!-- # ``` -->

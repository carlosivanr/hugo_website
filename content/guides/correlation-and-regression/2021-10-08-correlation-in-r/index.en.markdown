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
---


In this post I will walk through how to use the rstatix package to perform correlations in R.  


```r
# Load Packages ####
library(tidyverse)  # for data importing and visualization
library(rstatix)    # for performing statistics
library(ggpubr)     # for creating graphs
library(kableExtra) # for displaying tables

# Load Data --------------------------------------------------------------------
# List and load the graph data, will be fed in wide format
data <- read.csv("Ecommerce Customers")

kable(head(data, n = 3))
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> Email </th>
   <th style="text-align:left;"> Address </th>
   <th style="text-align:left;"> Avatar </th>
   <th style="text-align:right;"> Avg..Session.Length </th>
   <th style="text-align:right;"> Time.on.App </th>
   <th style="text-align:right;"> Time.on.Website </th>
   <th style="text-align:right;"> Length.of.Membership </th>
   <th style="text-align:right;"> Yearly.Amount.Spent </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> mstephenson@fernandez.com </td>
   <td style="text-align:left;"> 835 Frank Tunnel
Wrightmouth, MI 82180-9605 </td>
   <td style="text-align:left;"> Violet </td>
   <td style="text-align:right;"> 34.49727 </td>
   <td style="text-align:right;"> 12.65565 </td>
   <td style="text-align:right;"> 39.57767 </td>
   <td style="text-align:right;"> 4.082621 </td>
   <td style="text-align:right;"> 587.9511 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> hduke@hotmail.com </td>
   <td style="text-align:left;"> 4547 Archer Common
Diazchester, CA 06566-8576 </td>
   <td style="text-align:left;"> DarkGreen </td>
   <td style="text-align:right;"> 31.92627 </td>
   <td style="text-align:right;"> 11.10946 </td>
   <td style="text-align:right;"> 37.26896 </td>
   <td style="text-align:right;"> 2.664034 </td>
   <td style="text-align:right;"> 392.2049 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> pallen@yahoo.com </td>
   <td style="text-align:left;"> 24645 Valerie Unions Suite 582
Cobbborough, DC 99414-7564 </td>
   <td style="text-align:left;"> Bisque </td>
   <td style="text-align:right;"> 33.00091 </td>
   <td style="text-align:right;"> 11.33028 </td>
   <td style="text-align:right;"> 37.11060 </td>
   <td style="text-align:right;"> 4.104543 </td>
   <td style="text-align:right;"> 487.5475 </td>
  </tr>
</tbody>
</table>

```r
# EDA --------------------------------------------------------------------------
# Scatter plot
ggplot(data, aes(x = Length.of.Membership, y = Yearly.Amount.Spent)) +
  geom_point(alpha = 0.7, color = "#1565c0") +
  geom_smooth(method = "lm", color = "black")
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-1.png" width="672" />

### Correlations with rstatix
To perform the statistical test of significance for correlations, I like to use the rstatix package and its `cor_test()` function. What I have done here is encapsulated the output from the `cor_test()` function with the `kable()` function to print  a nicer table as compared to the plain output. The cor_test() function can take multiple variables that need to be tested through the vars and vars2 arguments. Simply add additional columns if they are in an analysis. `cor_test()` can also perfrom multiple types of correlation tests including the Pearson product moment correlation, Spearman's rank correlation, and Kendall's tau (non-parametric). The function can also take a [use = "pairwise.complete.obs"] argument to include only data that have complete observations. This is a useful feature in cases where you may have missing data. Finally, although it is not shown, the function can take an argument to specify confidence intervals. The output produces a correlation value, a test statistic, a p value, and confidence intervals.

```r
kable(
  cor_test(data, 
         vars = c("Length.of.Membership"), 
         vars2 = c("Yearly.Amount.Spent", "Time.on.App"), 
         method = "pearson", 
         use = "pairwise.complete.obs")
)
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
   <td style="text-align:left;"> Length.of.Membership </td>
   <td style="text-align:left;"> Yearly.Amount.Spent </td>
   <td style="text-align:right;"> 0.810 </td>
   <td style="text-align:right;"> 30.7225375 </td>
   <td style="text-align:right;"> 0.000 </td>
   <td style="text-align:right;"> 0.7764837 </td>
   <td style="text-align:right;"> 0.8373640 </td>
   <td style="text-align:left;"> Pearson </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Length.of.Membership </td>
   <td style="text-align:left;"> Time.on.App </td>
   <td style="text-align:right;"> 0.029 </td>
   <td style="text-align:right;"> 0.6506193 </td>
   <td style="text-align:right;"> 0.516 </td>
   <td style="text-align:right;"> -0.0586980 </td>
   <td style="text-align:right;"> 0.1165353 </td>
   <td style="text-align:left;"> Pearson </td>
  </tr>
</tbody>
</table>



<!-- data <- data %>% arrange(subjectkey) -->
<!-- graph_met <- graph_met %>% arrange(subject) -->

<!-- runs <- c("run-1", "run-2", "run-3", "run-4") -->
<!-- k <- c(0.1, 0.2, 0.3, 0.4, 0.5) -->

<!-- for (i in 1:length(runs)){ -->
<!--   for (j in 1:length(k)){ -->
<!--     # Get each run and k data -->
<!--     y <- graph_met %>% filter(run == runs[i] & k == k[j]) -->

<!--     # join the two data frames by subjectkey and subject columns -->
<!--     run <- left_join(data, y, by = c("subjectkey" = "subject")) -->

<!--     print(paste(runs[i], k[j])) -->
<!--     results <- cor_test(run,  -->
<!--                         vars = c("pea_wiscv_tss"),  -->
<!--                         vars2 = c("mod", "clust", "cpath", "geff"),  -->
<!--                         method = "pearson", use = "pairwise.complete.obs") -->
<!--     print(results)   -->
<!--     print(" ") -->
<!--   } -->
<!-- } -->

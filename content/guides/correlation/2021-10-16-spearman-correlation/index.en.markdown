---
title: Spearman Correlation Draft
author: Carlos Rodriguez
date: '2021-10-16'
slug: spearman-correlation
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-10-16T06:29:43-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 10
draft: true
---

### Load packages

```r
library(tidyverse)  # for data importing and visualization
library(rstatix)    # for performing statistics
library(kableExtra) # for displaying tables
```

### Load data

```r
essayData <- read.table(file = "EssayMarks.dat", header = TRUE, sep = "\t")
essayData$grade<-factor(essayData$grade, levels = c("First Class","Upper Second Class", "Lower Second Class", "Third Class"))
kable(head(data))
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;">  </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> function (..., list = character(), package = NULL, lib.loc = NULL, </td>
  </tr>
  <tr>
   <td style="text-align:left;"> verbose = getOption(&quot;verbose&quot;), envir = .GlobalEnv, overwrite = TRUE) </td>
  </tr>
  <tr>
   <td style="text-align:left;"> { </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fileExt &lt;- function(x) { </td>
  </tr>
  <tr>
   <td style="text-align:left;"> db &lt;- grepl(&quot;\\.[^.]+\\.(gz|bz2|xz)$&quot;, x) </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ans &lt;- sub(&quot;.*\\.&quot;, &quot;&quot;, x) </td>
  </tr>
</tbody>
</table>


```r
cor.test(essayData$hours, as.numeric(essayData$grade), alternative = "less", method = "kendall")
```

```
## Warning in cor.test.default(essayData$hours, as.numeric(essayData$grade), :
## Cannot compute exact p-value with ties
```

```
## 
## 	Kendall's rank correlation tau
## 
## data:  essayData$hours and as.numeric(essayData$grade)
## z = -1.3458, p-value = 0.08918
## alternative hypothesis: true tau is less than 0
## sample estimates:
##        tau 
## -0.1575566
```

```r
cor.test(essayData$hours, as.numeric(essayData$grade), alternative = "less", method = "spearman")
```

```
## Warning in cor.test.default(essayData$hours, as.numeric(essayData$grade), :
## Cannot compute exact p-value with ties
```

```
## 
## 	Spearman's rank correlation rho
## 
## data:  essayData$hours and as.numeric(essayData$grade)
## S = 18111, p-value = 0.1019
## alternative hypothesis: true rho is less than 0
## sample estimates:
##        rho 
## -0.1930781
```

```r
essayData$grade <-  as.numeric(essayData$grade)
cor_test(essayData, vars = c("hours"), vars2 = c("grade"), method = c("spearman"), alternative = "less")
```

```
## # A tibble: 1 × 6
##   var1  var2    cor statistic     p method  
##   <chr> <chr> <dbl>     <dbl> <dbl> <chr>   
## 1 hours grade -0.19    18111. 0.102 Spearman
```

```r
cor_test(essayData, vars = c("hours"), vars2 = c("grade"), method = c("kendall"), alternative = "less")
```

```
## # A tibble: 1 × 6
##   var1  var2    cor statistic      p method 
##   <chr> <chr> <dbl>     <dbl>  <dbl> <chr>  
## 1 hours grade -0.16     -1.35 0.0892 Kendall
```

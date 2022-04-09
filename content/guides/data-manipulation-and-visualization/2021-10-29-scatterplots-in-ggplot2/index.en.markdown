---
title: Scatterplots in ggplot2
author: Carlos Rodriguez
date: '2021-10-29'
slug: scatterplots-in-ggplot2
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-10-29T21:08:27-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 50
draft: false
---

Scatterplots are a great tool to visualize data. They can be used to understand the relationship between variables that may not otherwise be observed by just looking at the values in a spreadsheet. The following guide covers my appraoch to making scatter plots with the ggplot2 package.

In this guide, I'll use data from the correlation walkthrough [here](/guides/correlation/correlation-in-r). The data describes a hypothetical anxiety measure, exam test scores, and the number or hours spent studying to better understand factors that led to better grades.


```r
library(tidyverse)
library(kableExtra)
```




```r
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

### Basic ggplot scatterplot

```r
ggplot(data, aes(x = Anxiety, y = Exam)) +
  geom_point()
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-4-1.png" width="672" />

### Adding a linear regression line

```r
ggplot(data, aes(x = Anxiety, y = Exam)) +
  geom_point() +
  geom_smooth(method = "lm", se = TRUE)
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-5-1.png" width="672" />

### Visualize points by gender

```r
ggplot(data, aes(x = Anxiety, y = Exam, color = Gender)) +
  geom_point() +
  geom_smooth(method = "lm", se = FALSE)
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-6-1.png" width="672" />

### Changing colors, backgrounds, and axes

```r
colors = c( "#440154FF","#1565c0")

ggplot(data, aes(x = Anxiety, y = Exam, color = Gender)) +
  geom_point(alpha = 0.7) +
  scale_color_manual(values = colors) +
  geom_smooth(method = "lm", se = FALSE) +
  theme_minimal() +
  theme(axis.line = element_line(color = "grey70"))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-7-1.png" width="672" />

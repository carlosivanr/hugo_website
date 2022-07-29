---
title: Mixed design ANOVA
author: Carlos Rodriguez
date: '2022-07-29'
slug: mixed-anova
categories: []
tags: []
subtitle: ''
summary: 'Designs with one between-subjects factor and one within-subjects factor.'
authors: []
lastmod: "July 29, 2022"
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
---
<script src="{{< blogdown/postref >}}index.en_files/kePrint/kePrint.js"></script>
<link href="{{< blogdown/postref >}}index.en_files/lightable/lightable.css" rel="stylesheet" />








## The data set
<div style="border: 1px solid #ddd; padding: 0px; overflow-y: scroll; height:300px; "><table class="table" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;position: sticky; top:0; background-color: #FFFFFF;"> Subject </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;position: sticky; top:0; background-color: #FFFFFF;"> Angle </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;position: sticky; top:0; background-color: #FFFFFF;"> Latency </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;position: sticky; top:0; background-color: #FFFFFF;"> Group </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 450 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 510 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 630 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 390 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 480 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 540 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 570 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 630 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 660 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 450 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 660 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 720 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 5 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 510 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 5 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 660 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 5 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 630 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 6 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 360 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 6 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 450 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 6 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 450 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 7 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 510 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 7 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 600 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 7 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 720 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 510 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 660 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 780 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 9 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 510 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 9 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 660 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 9 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 660 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 510 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 540 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 660 </td>
   <td style="text-align:left;"> Young </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 11 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 420 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 11 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 570 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 11 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 690 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 12 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 600 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 12 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 720 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 12 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 810 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 13 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 450 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 13 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 540 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 13 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 690 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 14 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 630 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 14 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 660 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 14 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 780 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 15 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 420 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 15 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 570 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 15 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 780 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 16 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 600 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 16 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 780 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 16 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 870 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 17 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 630 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 17 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 690 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 17 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 870 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 18 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 480 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 18 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 570 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 18 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 720 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 19 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 690 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 19 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 750 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 19 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 900 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 20 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 510 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 20 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 690 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 20 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 810 </td>
   <td style="text-align:left;"> Old </td>
  </tr>
</tbody>
</table></div>


## Data plot
<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-1.png" width="672" />

## Perform ANOVA tests

```r
kable(
  get_anova_table(
          anova_test(data = data, 
                     dv = Latency, 
                     wid = Subject, 
                     within = Angle, 
                     between = Group,
                     effect.size = "pes", 
                     detailed = TRUE)))
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
   <td style="text-align:right;"> 18 </td>
   <td style="text-align:right;"> 22767360 </td>
   <td style="text-align:right;"> 327900 </td>
   <td style="text-align:right;"> 1249.809 </td>
   <td style="text-align:right;"> 0.000 </td>
   <td style="text-align:left;"> * </td>
   <td style="text-align:right;"> 0.986 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Group </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 18 </td>
   <td style="text-align:right;"> 132540 </td>
   <td style="text-align:right;"> 327900 </td>
   <td style="text-align:right;"> 7.276 </td>
   <td style="text-align:right;"> 0.015 </td>
   <td style="text-align:left;"> * </td>
   <td style="text-align:right;"> 0.288 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Angle </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 36 </td>
   <td style="text-align:right;"> 435090 </td>
   <td style="text-align:right;"> 54420 </td>
   <td style="text-align:right;"> 143.911 </td>
   <td style="text-align:right;"> 0.000 </td>
   <td style="text-align:left;"> * </td>
   <td style="text-align:right;"> 0.889 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Group:Angle </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 36 </td>
   <td style="text-align:right;"> 21090 </td>
   <td style="text-align:right;"> 54420 </td>
   <td style="text-align:right;"> 6.976 </td>
   <td style="text-align:right;"> 0.003 </td>
   <td style="text-align:left;"> * </td>
   <td style="text-align:right;"> 0.279 </td>
  </tr>
</tbody>
</table>


## Simple Effects Young

```r
kable(
  get_anova_table(
    anova_test(data = young, 
                     dv = Latency, 
                     wid = Subject, 
                     within = Angle, 
                     effect.size = "pes", 
                     detailed = TRUE)))
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
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 9712830 </td>
   <td style="text-align:right;"> 146070 </td>
   <td style="text-align:right;"> 598.449 </td>
   <td style="text-align:right;"> 0e+00 </td>
   <td style="text-align:left;"> * </td>
   <td style="text-align:right;"> 0.985 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Angle </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 18 </td>
   <td style="text-align:right;"> 144960 </td>
   <td style="text-align:right;"> 32040 </td>
   <td style="text-align:right;"> 40.719 </td>
   <td style="text-align:right;"> 2e-07 </td>
   <td style="text-align:left;"> * </td>
   <td style="text-align:right;"> 0.819 </td>
  </tr>
</tbody>
</table>

### Comparisons of Cell Means Young

```r
kable(
  young %>% 
  pairwise_t_test(Latency ~ Angle,
                  paired = TRUE,
                  p.adjust.method = "none"))
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
   <td style="text-align:left;"> Latency </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> -6.194225 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 1.60e-04 </td>
   <td style="text-align:right;"> 1.60e-04 </td>
   <td style="text-align:left;"> *** </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Latency </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> -8.158801 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 1.89e-05 </td>
   <td style="text-align:right;"> 1.89e-05 </td>
   <td style="text-align:left;"> **** </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Latency </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> -3.253957 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 1.00e-02 </td>
   <td style="text-align:right;"> 1.00e-02 </td>
   <td style="text-align:left;"> ** </td>
  </tr>
</tbody>
</table>

## Simple Effects Old

```r
kable(
  get_anova_table(
    anova_test(data = old, 
                     dv = Latency, 
                     wid = Subject, 
                     within = Angle, 
                     effect.size = "pes", 
                     detailed = TRUE)))
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
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 13187070 </td>
   <td style="text-align:right;"> 181830 </td>
   <td style="text-align:right;"> 652.718 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:left;"> * </td>
   <td style="text-align:right;"> 0.986 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Angle </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 18 </td>
   <td style="text-align:right;"> 311220 </td>
   <td style="text-align:right;"> 22380 </td>
   <td style="text-align:right;"> 125.155 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:left;"> * </td>
   <td style="text-align:right;"> 0.933 </td>
  </tr>
</tbody>
</table>

### Comparisons of Cell Means Old

```r
kable(
  old %>% 
    pairwise_t_test(Latency ~ Angle,
                  paired = TRUE,
                  p.adjust.method = "none"))
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
   <td style="text-align:left;"> Latency </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> -6.621705 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 9.68e-05 </td>
   <td style="text-align:right;"> 9.68e-05 </td>
   <td style="text-align:left;"> **** </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Latency </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> -13.897825 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 2.00e-07 </td>
   <td style="text-align:right;"> 2.00e-07 </td>
   <td style="text-align:left;"> **** </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Latency </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> -11.500000 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 1.10e-06 </td>
   <td style="text-align:right;"> 1.10e-06 </td>
   <td style="text-align:left;"> **** </td>
  </tr>
</tbody>
</table>

## Simple Effects Angle0

```r
angle0 <- summary(aov(Latency ~ 1 + Group, data = subset(data, Angle=="0")))
kable(angle0[[1]])
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> Df </th>
   <th style="text-align:right;"> Sum Sq </th>
   <th style="text-align:right;"> Mean Sq </th>
   <th style="text-align:right;"> F value </th>
   <th style="text-align:right;"> Pr(&gt;F) </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Group </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 21780 </td>
   <td style="text-align:right;"> 21780 </td>
   <td style="text-align:right;"> 3.161103 </td>
   <td style="text-align:right;"> 0.0923101 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Residuals </td>
   <td style="text-align:right;"> 18 </td>
   <td style="text-align:right;"> 124020 </td>
   <td style="text-align:right;"> 6890 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
  </tr>
</tbody>
</table>

## Simple Effects Angle4

```r
angle4 <- summary(aov(Latency ~ 1 + Group, data = subset(data, Angle=="4")))
kable(angle4[[1]])
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> Df </th>
   <th style="text-align:right;"> Sum Sq </th>
   <th style="text-align:right;"> Mean Sq </th>
   <th style="text-align:right;"> F value </th>
   <th style="text-align:right;"> Pr(&gt;F) </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Group </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 23805 </td>
   <td style="text-align:right;"> 23805 </td>
   <td style="text-align:right;"> 3.350457 </td>
   <td style="text-align:right;"> 0.0837955 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Residuals </td>
   <td style="text-align:right;"> 18 </td>
   <td style="text-align:right;"> 127890 </td>
   <td style="text-align:right;"> 7105 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
  </tr>
</tbody>
</table>

## Simple Effects Angle8

```r
angle8 <- summary(aov(Latency ~ 1 + Group, data = subset(data, Angle=="8")))
kable(angle8[[1]])
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> Df </th>
   <th style="text-align:right;"> Sum Sq </th>
   <th style="text-align:right;"> Mean Sq </th>
   <th style="text-align:right;"> F value </th>
   <th style="text-align:right;"> Pr(&gt;F) </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Group </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 108045 </td>
   <td style="text-align:right;"> 108045 </td>
   <td style="text-align:right;"> 14.91304 </td>
   <td style="text-align:right;"> 0.0011428 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Residuals </td>
   <td style="text-align:right;"> 18 </td>
   <td style="text-align:right;"> 130410 </td>
   <td style="text-align:right;"> 7245 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
  </tr>
</tbody>
</table>

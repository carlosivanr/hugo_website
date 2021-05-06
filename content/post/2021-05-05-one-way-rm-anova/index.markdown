---
title: One-way Repeated Measures ANOVA in R
author: Carlos Rodriguez
date: '2021-05-05'
slug: one-way-rm-anova
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-05-05360:40:40-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.



{{< tabs tabTotal="2" tabID="1" tabName1="rm-anova" tabID="2" tabName2="paired-t-test" >}}
<!-- -----------------------Tab 1---------------------------------- -->
{{< tab tabNum="1" >}}
### Repeated Measures ANOVA with jmv

```r
library(AMCP)
library(jmv)

#### Chapter 11 Table 5
# Load data
data("chapter_11_table_5")

# Inspect data
head(chapter_11_table_5)
```

```
##   Months30 Months36 Months42 Months48
## 1      108       96      110      122
## 2      103      117      127      133
## 3       96      107      106      107
## 4       84       85       92       99
## 5      118      125      125      116
## 6      110      107       96       91
```

```r
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
  effectSize = 'omega',
  emMeans = ~ `Age-in-months`,
  emmPlots = TRUE)
```

```
## 
##  REPEATED MEASURES ANOVA
## 
##  Within Subjects Effects                                                                        
##  ────────────────────────────────────────────────────────────────────────────────────────────── 
##                     Sum of Squares    df    Mean Square    F           p            ω²          
##  ────────────────────────────────────────────────────────────────────────────────────────────── 
##    Age-in-months          552.0000     3      184.00000    3.026919    0.0432186    0.0399919   
##    Residual              2006.0000    33       60.78788                                         
##  ────────────────────────────────────────────────────────────────────────────────────────────── 
##    Note. Type 3 Sums of Squares
## 
## 
##  Between Subjects Effects                                                           
##  ────────────────────────────────────────────────────────────────────────────────── 
##                Sum of Squares    df    Mean Square    F    p            ω²          
##  ────────────────────────────────────────────────────────────────────────────────── 
##    Residual          6624.000    11       602.1818                                  
##  ────────────────────────────────────────────────────────────────────────────────── 
##    Note. Type 3 Sums of Squares
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-1-1.png" width="672" />
{{< /tab >}}

<!-- -----------------------Tab 2---------------------------------- -->
{{< tab tabNum="2" >}}
#### Paired T test in RM ANOVA Framework

```r
#### Chapter 11 Table 1, A paired t test can be run as an anovaRM
data("chapter_11_table_1")
head(chapter_11_table_1)
```

```
##   YCondition1 YCondition2
## 1           8          10
## 2           3           6
## 3          12          13
## 4           5           9
## 5           7           8
## 6          13          14
```

```r
anovaRM(
  data = chapter_11_table_1,
        rm = list(
            list(
                label = 'Condition',
                levels = c('Low', 'High'))),
        rmCells = list(
            list(
                measure = 'YCondition1',
                cell = 'Low'),
            list(
                measure = 'YCondition2',
                cell = 'High')),
        rmTerms = list(
            'Condition'),
)
```

```
## 
##  REPEATED MEASURES ANOVA
## 
##  Within Subjects Effects                                                       
##  ───────────────────────────────────────────────────────────────────────────── 
##                 Sum of Squares    df    Mean Square    F           p           
##  ───────────────────────────────────────────────────────────────────────────── 
##    Condition         12.000000     1     12.0000000    15.00000    0.0117248   
##    Residual           4.000000     5      0.8000000                            
##  ───────────────────────────────────────────────────────────────────────────── 
##    Note. Type 3 Sums of Squares
## 
## 
##  Between Subjects Effects                                              
##  ───────────────────────────────────────────────────────────────────── 
##                Sum of Squares    df    Mean Square    F    p           
##  ───────────────────────────────────────────────────────────────────── 
##    Residual          118.0000     5       23.60000                     
##  ───────────────────────────────────────────────────────────────────── 
##    Note. Type 3 Sums of Squares
```
{{< /tab >}}
{{< /tabs >}}

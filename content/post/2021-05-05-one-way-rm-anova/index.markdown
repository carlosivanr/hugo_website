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

#### The data set
For this demo, we will use the chapter_11_table_5 dataset from the AMCP package. These data describe a hypthetical that tracked the age-normed general cognitive scores from the McCarthy Scales of Children's Abilities (MSCA) of 12 children at 4 different ages in months.


```r
library(AMCP)

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


<!-- -----------------------TABS---------------------------------- -->
{{< tabs tabTotal="3" tabID="1" tabName1="jmv" tabID="2" tabName2="rstatix" tabID="3" tabName3="paired-t-test">}}

<!-- -----------------------Tab 1---------------------------------- -->
{{< tab tabNum="1" >}}
### Repeated Measures ANOVA with jmv
In comparison to coding one-way and two-way ANOVAS, coding repeated measures ANOVA is a bit more cumbersome in jmv. After setting data to chapter_11_table_5, we have to set the rm option. The rm option takes a list of lists. For the one-way repeated measures design, we will need a list of one list that contains a label which will be "Age-in-months", and a character vector  of the levels of the age factor (i.e. 30, 60, 42, 48). Next, we will work on the rmCells by making another list of lists. This list will be made up of 4 lists, one for each level, and each list will have a "measure" that corresponds to a text description of the level, and a "cell" that corresponds to the levels used in the rm option. Now we can move on to specifying the repeated measure terms in "rmTerms." There are just a few more options before finishing up. For our purposes we will set spherCorr to 'none', but in practice you may want to apply a correction for sphericity depending on your data. We will then set the effectSize to 'omega', and set emMeans to produce the marginal means by months so that we can plot the data when emmPlots is set to TRUE. 


```r
library(AMCP)
library(jmv)

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

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-2-1.png" width="672" />
{{< /tab >}}


<!-- -----------------------Tab 2---------------------------------- -->
{{< tab tabNum="2" >}}
#### rstatix

```r
library(AMCP)
library(tidyverse)
library(rstatix)

# Load data
data("chapter_11_table_5")

# Create a new data frame with a subject id
rm_data <- cbind(id = c(1:12), chapter_11_table_5)

# Convert from wide to long format then convert id and age to factor
rm_data <- rm_data %>% 
  gather(., age, score, Months30:Months48) %>%
  convert_as_factor(id, age)

# Inspect data
head(rm_data)
```

```
##   id      age score
## 1  1 Months30   108
## 2  2 Months30   103
## 3  3 Months30    96
## 4  4 Months30    84
## 5  5 Months30   118
## 6  6 Months30   110
```

```r
# Conduct repeated measures ANOVA
rm_aov <- anova_test(
  data = rm_data,
  dv = score,
  wid = id,
  within = age,
  effect.size = "ges",
  error = NULL,
  white.adjust = FALSE,
  observed = NULL,
  detailed = FALSE,
)

get_anova_table(rm_aov, correction = "none")
```

```
## ANOVA Table (type III tests)
## 
##   Effect DFn DFd     F     p p<.05  ges
## 1    age   3  33 3.027 0.043     * 0.06
```
{{< /tab >}}

<!-- -----------------------Tab 3---------------------------------- -->
{{< tab tabNum="3" >}}
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

### Interpretation  
For the omnibus test, we obtain a significant effect of Age [F(33,3) = 3.03, p < 0.05] which suggests that the means of the 4 ages are not equal. The omega squared value is 0.04 which suggest the about 4% of the variance in scores is acounted for by age in the ages studied.

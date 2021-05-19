---
title: Two-way Repeated Measures ANOVA
author: Carlos Rodriguez
date: '2021-05-18'
slug: two-way-rm-anova
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-05-18T16:45:46-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 80
draft: true
---

Intro
### The data set


```r
library(AMCP)

# Load the data
data(chapter_12_table_1)

# Display part of the data
head(chapter_12_table_1)
```

```
##   Absent0 Absent4 Absent8 Present0 Present4 Present8
## 1     420     420     480      480      600      780
## 2     420     480     480      360      480      600
## 3     480     480     540      660      780      780
## 4     420     540     540      480      780      900
## 5     540     660     540      480      660      720
## 6     360     420     360      360      480      540
```

### Perform ANOVA Tests {#tests}
<!-- -----------------------TABS---------------------------------- -->
{{< tabs tabTotal="2" tabID="1" tabName1="jmv" tabName2="rstatix" >}}

<!-- -----------------------Tab 1---------------------------------- -->
{{< tab tabNum="1" >}}

```r
library(jmv)
library(tictoc)
# Repeated measures anova with jmv
tic()
anovaRM(
  data = chapter_12_table_1,
  rm = list(
    list(
      label = "Condition",
      levels = c("Absent", "Present")),
    list(
      label = "Angle",
      levels = c("0", "4", "8"))),
  rmCells = list(
    list(
      measure = "Absent0",
      cell = c("Absent", "0")),
    list(
      measure = "Absent4",
      cell = c("Absent", "4")),
    list(
      measure = "Absent8",
      cell = c("Absent", "8")),
    list(
      measure = "Present0",
      cell = c("Present", "0")),
    list(
      measure = "Present4",
      cell = c("Present", "4")),
    list(
      measure = "Present8",
      cell = c("Present", "8"))),
  rmTerms = ~ Condition + Angle + Condition:Angle,
  effectSize = 'partEta',
  emMeans = ~ Condition:Angle,
  emmPlots = TRUE
)
```

```
## 
##  REPEATED MEASURES ANOVA
## 
##  Within Subjects Effects                                                                           
##  ───────────────────────────────────────────────────────────────────────────────────────────────── 
##                       Sum of Squares    df    Mean Square    F           p             η²-p        
##  ───────────────────────────────────────────────────────────────────────────────────────────────── 
##    Condition               285660.00     1     285660.000    33.76596     0.0002560    0.7895522   
##    Residual                 76140.00     9       8460.000                                          
##    Angle                   289920.00     2     144960.000    40.71910     0.0000002    0.8189831   
##    Residual                 64080.00    18       3560.000                                          
##    Condition:Angle         105120.00     2      52560.000    45.31034    < .0000001    0.8342857   
##    Residual                 20880.00    18       1160.000                                          
##  ───────────────────────────────────────────────────────────────────────────────────────────────── 
##    Note. Type 3 Sums of Squares
## 
## 
##  Between Subjects Effects                                                           
##  ────────────────────────────────────────────────────────────────────────────────── 
##                Sum of Squares    df    Mean Square    F    p            η²-p        
##  ────────────────────────────────────────────────────────────────────────────────── 
##    Residual          292140.0     9       32460.00                                  
##  ────────────────────────────────────────────────────────────────────────────────── 
##    Note. Type 3 Sums of Squares
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/plot1-1.png" alt="figure caption." width="672" />
<p class="caption">Figure 1: figure caption.</p>
</div>

```r
toc()
```

```
## 3.71 sec elapsed
```

{{< /tab >}}

{{< tab tabNum="2" >}}

```r
library(tidyverse)
library(rstatix)
library(ggpubr)
library(tictoc)
# Create a new data frame with a subject id
rm_data <- cbind(id = c(1:10), chapter_12_table_1)

# Convert the data from wide to long
rm_data <-  rm_data %>% 
  gather(key = Condition.Angle, 
         value = Reaction_Time, 
         -id) %>%
  separate(col = Condition.Angle, 
           into = c("Condition", "Angle"), 
           sep = -1) %>%
  arrange(id, 
          Condition, 
          Angle) %>%
  convert_as_factor(Condition, Angle)

# Conduct repeated measures ANOVA
tic()
rm_aov <- anova_test(
  data = rm_data,
  dv = Reaction_Time,
  wid = id,
  within = c(Condition, Angle),
  effect.size = "pes",
)
get_anova_table(rm_aov, correction = "none")
```

```
## ANOVA Table (type III tests)
## 
##            Effect DFn DFd      F        p p<.05   pes
## 1       Condition   1   9 33.766 2.56e-04     * 0.790
## 2           Angle   2  18 40.719 2.09e-07     * 0.819
## 3 Condition:Angle   2  18 45.310 9.42e-08     * 0.834
```

```r
# Generate Plot
ggline(rm_data,
       "Condition", 
       "Reaction_Time",
       color = "Angle",
       add = "mean_se",
       palette = "jama",
       position = position_dodge(.2))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/rstatix-1.png" width="672" />

```r
# ggboxplot(rm_data, 
#           x = "Condition",
#           y = "Reaction_Time",
#           color = "Angle",
#           )
toc()
```

```
## 0.707 sec elapsed
```
{{< /tab >}}
{{< /tabs >}}


### Wrap Up
jmv is much more tedious to code repeated measures. Yes the tables and plots are nice, but it can be a pain in the but to work with. If you have long data, you'll need to convert it to wide. And it's also very slow. I once ran a two-way repeated measures ANOVA on data from over 200 participants. WHile jmv took over a minute, rstatix computed the computation in less than one second. This could be a consideration when planing which package to use.

With rstatix the syntax for repeated measures anova are much simpler. I wouldn't code two-way repeated measures in jmv. Rstatix is faster. 

Because of these transformations, it's important to know how to convert long to wide and wide to long. This can become especially tricky when you have multiple variables to keep track of.

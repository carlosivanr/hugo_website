---
title: One-way ANCOVA
author: Carlos Rodriguez
date: '2021-05-16'
slug: ancova
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-05-16T06:57:23-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 30
draft: true
#bibliography: "../references/refs.bib"
biblio-style: "apa"
link-citations: true
nocite: |
  @R-rstatix, @R-AMCP, @R-jmv, @R-ggpubr, @R-tidyverse
---

### The data set
For this demo, we will use the data from Chapter 9, Table 7 in the AMCP package. In this hypothetical study, participants are randomly assigned to one of three conditions to examine the efficacy of a treatment for depression. Participants receive a selective serotonin reuptake inhibitor (SSRI) in condition 1, a placebo in condition 2, or are assigned to a wait list control in condition 3.


```r
library(AMCP)
library(rstatix)
library(ggpubr)

# Load data
data(chapter_9_table_7)

# Convert group to factor
chapter_9_table_7$Condition <- as.factor(chapter_9_table_7$Condition)

# Inspect Data
head(chapter_9_table_7)
```

```
##   Condition Pre Post
## 1         1  18   12
## 2         1  16    0
## 3         1  16   10
## 4         1  15    9
## 5         1  14    0
## 6         1  20   11
```

<!-- -----------------------TABS---------------------------------- -->
{{< tabs tabTotal="2" tabID="1" tabName1="jmv" tabID="2" tabName2="rstatix" tabID="3" tabName3="paired-t-test">}}

<!-- -----------------------Tab 1---------------------------------- -->
{{< tab tabNum="1" >}}

```r
library(jmv)
ancova(formula = Post ~ Condition + Pre,
       data = chapter_9_table_7,
       effectSize = 'partEta',
       ss = "3",
       emMeans = ~ Condition,
       emmPlots = TRUE,
       emmTables = FALSE,
       ciWidthEmm = 95
)
```

```
## 
##  ANCOVA
## 
##  ANCOVA - Post                                                                               
##  ─────────────────────────────────────────────────────────────────────────────────────────── 
##                 Sum of Squares    df    Mean Square    F            p            η²p         
##  ─────────────────────────────────────────────────────────────────────────────────────────── 
##    Condition          217.1495     2      108.57474     3.732399    0.0375843    0.2230642   
##    Pre                313.3653     1      313.36526    10.772342    0.0029369    0.2929469   
##    Residuals          756.3347    26       29.08980                                          
##  ───────────────────────────────────────────────────────────────────────────────────────────
```

<div class="figure">
<img src="{{< blogdown/postref >}}index_files/figure-html/jmv-1.png" alt="Plot of post-treatment means by Condition." width="672" />
<p class="caption">Figure 1: Plot of post-treatment means by Condition.</p>
</div>
{{< /tab >}}

<!-- -----------------------Tab 2---------------------------------- -->
{{< tab tabNum="2" >}}

```r
# Conduct ANOVA test
aocv.model <- anova_test(Post ~ Condition + Pre, 
           data = chapter_9_table_7, 
           dv = Post,
           covariate = Pre,
           effect.size = "pes", 
           type = 3
           )
```

```
## Coefficient covariances computed by hccm()
```

```r
# Print ANOVA table
get_anova_table(aocv.model)
```

```
## ANOVA Table (type III tests)
## 
##      Effect DFn DFd      F     p p<.05   pes
## 1 Condition   2  26  3.732 0.038     * 0.223
## 2       Pre   1  26 10.772 0.003     * 0.293
```

### Post-hoc tests

```r
pwc <- chapter_9_table_7 %>% 
  emmeans_test(
    Post ~ Condition, covariate = Pre,
    p.adjust.method = "bonferroni"
    )

# Print post-hoc test
pwc
```

```
## # A tibble: 3 x 8
##   .y.   group1 group2    df statistic      p  p.adj p.adj.signif
## * <chr> <chr>  <chr>  <dbl>     <dbl>  <dbl>  <dbl> <chr>       
## 1 Post  1      2         26    -1.84  0.0770 0.231  ns          
## 2 Post  1      3         26    -2.67  0.0129 0.0388 *           
## 3 Post  2      3         26    -0.826 0.416  1      ns
```


### Plot of means 

```r
pwc <- pwc %>% add_xy_position(x = "Condition", fun = "mean_se")

ggline(get_emmeans(pwc), 
       x = "Condition", 
       y = "emmean",
       ylab = "Mean Depression Scores") +
  geom_errorbar(aes(ymin = conf.low, ymax = conf.high), width = 0.2) + 
  stat_pvalue_manual(pwc, hide.ns = TRUE, tip.length = FALSE) +
  labs(
    subtitle = get_test_label(aocv.model, detailed = TRUE),
    caption = get_pwc_label(pwc)
  )
```

<div class="figure">
<img src="{{< blogdown/postref >}}index_files/figure-html/plot-1.png" alt="Plot of post-treatment means by Condition." width="672" />
<p class="caption">Figure 2: Plot of post-treatment means by Condition.</p>
</div>

{{< /tab >}}
{{< /tabs >}}








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

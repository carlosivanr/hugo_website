---
title: Histograms
author: Carlos Rodriguez
date: '2024-06-26'
slug: percentage-histograms
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2024-06-26T09:31:48-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 30
draft: False
---




```r
library(tidyverse)
```


```r
head(mtcars)
```

```
##                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
## Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
## Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
## Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
## Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
## Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
## Valiant           18.1   6  225 105 2.76 3.460 20.22  1  0    3    1
```
## Traditional Histogram

```r
mtcars %>%
  ggplot(., aes(x = cyl)) +
  geom_bar() +
  theme_minimal()
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-3-1.png" width="672" />
## Histogram paneled by automatic transmistion

```r
mtcars %>%
  ggplot(., aes(x = cyl)) +
  geom_bar() +
  facet_wrap(~am) +
  theme_minimal()
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-4-1.png" width="672" />
## Clustered histogram

```r
mtcars %>%
  mutate(am = factor(am)) %>%
  ggplot(., aes(x = cyl)) +
  geom_bar(aes(color = am, fill = am),
                 position = position_dodge2(preserve = "single")) +
  theme_minimal()
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-5-1.png" width="672" />

## Clustered histogram with percentages
About 60% of the observations where am == 1 are 4 cylinder vehicles.
<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-6-1.png" width="672" />

## Denominator is the sample size
About 25% of all observations are automatic and 4 cylinder vehicles.

```r
mtcars %>%
  mutate(am = factor(am)) %>%
  ggplot(., aes(cyl, fill = am)) +
  geom_bar(aes(y = (..count..)/sum(..count..)),
           position = position_dodge2(preserve = "single")) +
  scale_y_continuous(labels = scales::label_percent()) +
  theme_minimal() +
  ylab("Percent of all observations") 
```

```
## Warning: The dot-dot notation (`..count..`) was deprecated in ggplot2 3.4.0.
## ℹ Please use `after_stat(count)` instead.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
## generated.
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-7-1.png" width="672" />


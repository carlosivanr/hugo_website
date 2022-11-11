---
title: Displaying Likert Style Responses
author: Carlos Rodriguez
date: '2022-11-10'
slug: displaying-likert-style-responses
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2022-11-10T19:51:37-07:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
draft: false
type: book
weight: 30
---







## Bar chart with perecentage of factored responses relative to the whole sample

```r
data %>%
  drop_na(sample_question_1.factor) %>%
  ggplot(., aes(sample_question_1.factor, group = 1)) +
  geom_bar(aes(y = ..prop.., fill = factor(..x..)), position = position_dodge()) +
  coord_flip(clip = "off") +
  scale_y_continuous(labels = scales::percent, limits = c(0, .75)) + # limits extends the chart to prevent clipping 
  geom_text(aes(label = scales::percent(..prop..), y= ..prop.. ), stat= "count", size = 3,
              hjust = -.15, colour = "black") +
  labs(y = "Percent", x = "") +
  theme_minimal() +
  theme(legend.position = "top", axis.text.x = element_text(angle = -0, hjust = 0)) +
  guides(fill = "none")
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-2-1.png" width="672" />

## Faceted bar chart by a grouping variable

```r
data %>%
  drop_na(sample_question_1.factor, site) %>%
  ggplot(., aes(sample_question_1.factor, group = 1)) +
  geom_bar(aes(y = ..prop.., fill = site), position = position_dodge()) + # Applies fill to site
  #geom_bar(aes(y = ..prop.., fill = factor(..x..)), position = position_dodge()) + # Applies fill to response
  coord_flip(clip = "off") +
  scale_y_continuous(labels = scales::percent) +
  geom_text(aes(label = scales::percent(round(..prop..,2)), y= ..prop.. ), stat= "count", size = 3,
              hjust = -.15, colour = "black") +
  labs(y = "Percent", x = "") +
  theme_minimal() +
  theme(legend.position = "top", axis.text.x = element_text(angle = -0, hjust = 0)) +
  guides(fill = "none") +
  facet_wrap(~ site, ncol = 2)
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-3-1.png" width="672" />

## Bar chart with perecentage of factored responses relative to a grouping variable

```r
data %>%
  drop_na(sample_question_1.factor, site) %>%
  ggplot(., aes(sample_question_1.factor, fill = site)) +
  geom_bar(aes(y=..count../tapply(..count.., ..fill.. ,sum)[..fill..]), position="dodge2") +
  coord_flip(clip = "off") +
  geom_text(aes(y=..count../tapply(..count.., ..fill.. ,sum)[..fill..], label = scales::percent(round(..count../tapply(..count.., ..fill.. ,sum)[..fill..],2))), stat="count", position=position_dodge(1), hjust=-0.15, size = 3) +
  scale_y_continuous(labels = scales::percent) +
  theme_minimal() +
  theme(legend.position = "top") +
  guides(fill = guide_legend(title = "Site")) +
  labs(y = "Percent", x = "")
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-4-1.png" width="672" />




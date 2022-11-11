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

``` r
data %>%
  drop_na(sample_question_1.factor) %>%
  ggplot(., aes(sample_question_1.factor, group = 1)) +
  geom_bar(aes(y = ..prop.., fill = factor(..x..)), position = position_dodge()) +
  geom_text(aes(label = scales::percent(..prop..), y= ..prop.. ), stat= "count", size = 3,
              hjust = -.15, colour = "black") +
  coord_flip(clip = "off") +
  scale_y_continuous(labels = scales::percent, limits = c(0, .75)) + # limits extends the chart to prevent clipping 
  labs(y = "Percent", x = "") +
  theme_minimal() +
  theme(legend.position = "top", axis.text.x = element_text(angle = -0, hjust = 0)) +
  guides(fill = "none")
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-2-1.png" width="672" />

## Faceted bar chart by a grouping variable

``` r
data %>%
  drop_na(sample_question_1.factor, site) %>%
  ggplot(., aes(sample_question_1.factor, group = 1)) +
  geom_bar(aes(y = ..prop.., fill = site), position = position_dodge()) + # Applies fill to site
  #geom_bar(aes(y = ..prop.., fill = factor(..x..)), position = position_dodge()) + # Applies fill to response
  geom_text(aes(label = scales::percent(round(..prop..,2)), y= ..prop.. ), stat= "count", size = 3,
              hjust = -.15, colour = "black") +
  coord_flip(clip = "off") +
  scale_y_continuous(labels = scales::percent) +
  labs(y = "Percent", x = "") +
  theme_minimal() +
  theme(legend.position = "top", axis.text.x = element_text(angle = -0, hjust = 0)) +
  guides(fill = "none") +
  facet_wrap(~ site, ncol = 2)
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-3-1.png" width="672" />

## Bar chart with perecentage of factored responses relative to a grouping variable

``` r
data %>%
  drop_na(sample_question_1.factor, site) %>%
  ggplot(., aes(sample_question_1.factor, fill = site)) +
  geom_bar(aes(y=..count../tapply(..count.., ..fill.. ,sum)[..fill..]), position="dodge2") +
  geom_text(aes(y=..count../tapply(..count.., ..fill.. ,sum)[..fill..], label = scales::percent(round(..count../tapply(..count.., ..fill.. ,sum)[..fill..],2))), stat="count", position=position_dodge(1), hjust=-0.15, size = 3) +
  coord_flip(clip = "off") +
  scale_y_continuous(labels = scales::percent) +
  theme_minimal() +
  theme(legend.position = "top") +
  guides(fill = guide_legend(title = "Site")) +
  labs(y = "Percent", x = "")
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-4-1.png" width="672" />

## Bar chart faceted by related survey items

``` r
data %>% 
  select(sample_question_2.factor:sample_question_3.factor) %>%
  drop_na() %>%
  pivot_longer(cols = everything(),
               names_to = "level",
               values_to = "Response") %>%
  group_by(Response, level) %>%
  ggplot(., aes(Response, group = 1)) +
  geom_bar(aes(y = ..prop.., fill = factor(..x..)), position = position_dodge()) +
  geom_text(aes(label = scales::percent(..prop..), y= ..prop.. ), stat= "count", size = 3,
              hjust = -.15, colour = "black") +
  scale_y_continuous(labels = scales::percent, limits = c(0, .75)) +
  coord_flip() +
  theme_minimal() +
  guides(fill = "none") +
  labs(y = "Percent", x = "") +
  facet_wrap(~level)
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-5-1.png" width="672" />

``` r
data %>% 
  select(sample_question_2.factor:sample_question_3.factor) %>%
  tbl_likert()
```

<div id="hjdpmjsgxz" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#hjdpmjsgxz .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#hjdpmjsgxz .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#hjdpmjsgxz .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#hjdpmjsgxz .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#hjdpmjsgxz .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#hjdpmjsgxz .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#hjdpmjsgxz .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#hjdpmjsgxz .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#hjdpmjsgxz .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#hjdpmjsgxz .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#hjdpmjsgxz .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#hjdpmjsgxz .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#hjdpmjsgxz .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#hjdpmjsgxz .gt_from_md > :first-child {
  margin-top: 0;
}

#hjdpmjsgxz .gt_from_md > :last-child {
  margin-bottom: 0;
}

#hjdpmjsgxz .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#hjdpmjsgxz .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#hjdpmjsgxz .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#hjdpmjsgxz .gt_row_group_first td {
  border-top-width: 2px;
}

#hjdpmjsgxz .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#hjdpmjsgxz .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#hjdpmjsgxz .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#hjdpmjsgxz .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#hjdpmjsgxz .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#hjdpmjsgxz .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#hjdpmjsgxz .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#hjdpmjsgxz .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#hjdpmjsgxz .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#hjdpmjsgxz .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#hjdpmjsgxz .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#hjdpmjsgxz .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#hjdpmjsgxz .gt_left {
  text-align: left;
}

#hjdpmjsgxz .gt_center {
  text-align: center;
}

#hjdpmjsgxz .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#hjdpmjsgxz .gt_font_normal {
  font-weight: normal;
}

#hjdpmjsgxz .gt_font_bold {
  font-weight: bold;
}

#hjdpmjsgxz .gt_font_italic {
  font-style: italic;
}

#hjdpmjsgxz .gt_super {
  font-size: 65%;
}

#hjdpmjsgxz .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#hjdpmjsgxz .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#hjdpmjsgxz .gt_indent_1 {
  text-indent: 5px;
}

#hjdpmjsgxz .gt_indent_2 {
  text-indent: 10px;
}

#hjdpmjsgxz .gt_indent_3 {
  text-indent: 15px;
}

#hjdpmjsgxz .gt_indent_4 {
  text-indent: 20px;
}

#hjdpmjsgxz .gt_indent_5 {
  text-indent: 25px;
}
</style>
<table class="gt_table">
  
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col"><strong>Characteristic</strong></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col"><strong>Not effective</strong><sup class="gt_footnote_marks">1</sup></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col"><strong>Somewhat effective</strong><sup class="gt_footnote_marks">1</sup></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col"><strong>Very effective</strong><sup class="gt_footnote_marks">1</sup></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col"><strong>Not sure</strong><sup class="gt_footnote_marks">1</sup></th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left">sample_question_2.factor</td>
<td class="gt_row gt_center">2 (1.4%)</td>
<td class="gt_row gt_center">58 (41%)</td>
<td class="gt_row gt_center">74 (52%)</td>
<td class="gt_row gt_center">8 (5.6%)</td></tr>
    <tr><td class="gt_row gt_left">sample_question_3.factor</td>
<td class="gt_row gt_center">2 (1.4%)</td>
<td class="gt_row gt_center">54 (38%)</td>
<td class="gt_row gt_center">83 (58%)</td>
<td class="gt_row gt_center">4 (2.8%)</td></tr>
  </tbody>
  
  <tfoot class="gt_footnotes">
    <tr>
      <td class="gt_footnote" colspan="5"><sup class="gt_footnote_marks">1</sup> n (%)</td>
    </tr>
  </tfoot>
</table>
</div>

## Bar chart with grouping variable and faceted by related survey item

``` r
# Create a dataframe with the group specific sample sizes. These will later be
# merged into a dataframe to calculate the percent of responses relative to a
# grouping variable.
n_site <- data %>%
  drop_na(site) %>%
  select(site, sample_question_2.factor:sample_question_3.factor) %>%
  group_by(site) %>%
  summarise_all(~sum(!is.na(.))) %>%
  pivot_longer(cols = sample_question_2.factor:sample_question_3.factor,
               names_to = "level",
               values_to = "n")
  

data %>% 
  select(sample_question_2.factor:sample_question_3.factor, site) %>%
  pivot_longer(cols = sample_question_2.factor:sample_question_3.factor,
               names_to = "level",
               values_to = "Response") %>%
  group_by(Response, site, level) %>%
  summarise(freq = n()) %>%
  left_join(., n_site, by = c("site", "level")) %>%
  drop_na() %>%
  mutate(prop = round((freq / n * 100), 1)) %>%
  ggplot(., aes(x = Response, y = prop, fill = site, label = str_c(prop,"%"))) +
  geom_col(position = "dodge2") +
  geom_text(position = position_dodge(.9), size = 3, hjust = -.1) +
  coord_flip(clip = "off")+
  scale_y_continuous(labels = scales::percent_format(scale = 1), limits = c(0, 100)) +
  theme_minimal() +
  guides(fill = guide_legend(title = "Site")) +
  theme(legend.position = "top", axis.text.x = element_text(angle = -0, hjust = 0)) +
  labs(y = "Percent", x = "") +
  facet_wrap(~level)
```

    ## `summarise()` has grouped output by 'Response', 'site'. You can override using
    ## the `.groups` argument.

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-7-1.png" width="672" />

``` r
data %>% 
  select(sample_question_2.factor:sample_question_3.factor, site) %>%
  tbl_summary(by = "site",
              digits = all_categorical() ~ 1)
```

    ## 8 observations missing `site` have been removed. To include these observations, use `forcats::fct_explicit_na()` on `site` column before passing to `tbl_summary()`.

<div id="cgekpfqild" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#cgekpfqild .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#cgekpfqild .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#cgekpfqild .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#cgekpfqild .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#cgekpfqild .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#cgekpfqild .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#cgekpfqild .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#cgekpfqild .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#cgekpfqild .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#cgekpfqild .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#cgekpfqild .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#cgekpfqild .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#cgekpfqild .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#cgekpfqild .gt_from_md > :first-child {
  margin-top: 0;
}

#cgekpfqild .gt_from_md > :last-child {
  margin-bottom: 0;
}

#cgekpfqild .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#cgekpfqild .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#cgekpfqild .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#cgekpfqild .gt_row_group_first td {
  border-top-width: 2px;
}

#cgekpfqild .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#cgekpfqild .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#cgekpfqild .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#cgekpfqild .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#cgekpfqild .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#cgekpfqild .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#cgekpfqild .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#cgekpfqild .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#cgekpfqild .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#cgekpfqild .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#cgekpfqild .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#cgekpfqild .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#cgekpfqild .gt_left {
  text-align: left;
}

#cgekpfqild .gt_center {
  text-align: center;
}

#cgekpfqild .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#cgekpfqild .gt_font_normal {
  font-weight: normal;
}

#cgekpfqild .gt_font_bold {
  font-weight: bold;
}

#cgekpfqild .gt_font_italic {
  font-style: italic;
}

#cgekpfqild .gt_super {
  font-size: 65%;
}

#cgekpfqild .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#cgekpfqild .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#cgekpfqild .gt_indent_1 {
  text-indent: 5px;
}

#cgekpfqild .gt_indent_2 {
  text-indent: 10px;
}

#cgekpfqild .gt_indent_3 {
  text-indent: 15px;
}

#cgekpfqild .gt_indent_4 {
  text-indent: 20px;
}

#cgekpfqild .gt_indent_5 {
  text-indent: 25px;
}
</style>
<table class="gt_table">
  
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col"><strong>Characteristic</strong></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col"><strong>Albuquerque</strong>, N = 52<sup class="gt_footnote_marks">1</sup></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col"><strong>San Diego</strong>, N = 23<sup class="gt_footnote_marks">1</sup></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col"><strong>Denver</strong>, N = 35<sup class="gt_footnote_marks">1</sup></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col"><strong>El Paso</strong>, N = 17<sup class="gt_footnote_marks">1</sup></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col"><strong>Los Angeles</strong>, N = 15<sup class="gt_footnote_marks">1</sup></th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left">sample_question_2.factor</td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center"></td></tr>
    <tr><td class="gt_row gt_left">    Not effective</td>
<td class="gt_row gt_center">0.0 (0.0%)</td>
<td class="gt_row gt_center">1.0 (4.3%)</td>
<td class="gt_row gt_center">1.0 (3.0%)</td>
<td class="gt_row gt_center">0.0 (0.0%)</td>
<td class="gt_row gt_center">0.0 (0.0%)</td></tr>
    <tr><td class="gt_row gt_left">    Somewhat effective</td>
<td class="gt_row gt_center">21.0 (40.4%)</td>
<td class="gt_row gt_center">13.0 (56.5%)</td>
<td class="gt_row gt_center">11.0 (33.3%)</td>
<td class="gt_row gt_center">8.0 (47.1%)</td>
<td class="gt_row gt_center">4.0 (26.7%)</td></tr>
    <tr><td class="gt_row gt_left">    Very effective</td>
<td class="gt_row gt_center">29.0 (55.8%)</td>
<td class="gt_row gt_center">6.0 (26.1%)</td>
<td class="gt_row gt_center">20.0 (60.6%)</td>
<td class="gt_row gt_center">7.0 (41.2%)</td>
<td class="gt_row gt_center">11.0 (73.3%)</td></tr>
    <tr><td class="gt_row gt_left">    Not sure</td>
<td class="gt_row gt_center">2.0 (3.8%)</td>
<td class="gt_row gt_center">3.0 (13.0%)</td>
<td class="gt_row gt_center">1.0 (3.0%)</td>
<td class="gt_row gt_center">2.0 (11.8%)</td>
<td class="gt_row gt_center">0.0 (0.0%)</td></tr>
    <tr><td class="gt_row gt_left">    Unknown</td>
<td class="gt_row gt_center">0</td>
<td class="gt_row gt_center">0</td>
<td class="gt_row gt_center">2</td>
<td class="gt_row gt_center">0</td>
<td class="gt_row gt_center">0</td></tr>
    <tr><td class="gt_row gt_left">sample_question_3.factor</td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center"></td></tr>
    <tr><td class="gt_row gt_left">    Not effective</td>
<td class="gt_row gt_center">0.0 (0.0%)</td>
<td class="gt_row gt_center">1.0 (4.3%)</td>
<td class="gt_row gt_center">1.0 (2.9%)</td>
<td class="gt_row gt_center">0.0 (0.0%)</td>
<td class="gt_row gt_center">0.0 (0.0%)</td></tr>
    <tr><td class="gt_row gt_left">    Somewhat effective</td>
<td class="gt_row gt_center">25.0 (48.1%)</td>
<td class="gt_row gt_center">12.0 (52.2%)</td>
<td class="gt_row gt_center">9.0 (26.5%)</td>
<td class="gt_row gt_center">6.0 (35.3%)</td>
<td class="gt_row gt_center">2.0 (13.3%)</td></tr>
    <tr><td class="gt_row gt_left">    Very effective</td>
<td class="gt_row gt_center">27.0 (51.9%)</td>
<td class="gt_row gt_center">9.0 (39.1%)</td>
<td class="gt_row gt_center">22.0 (64.7%)</td>
<td class="gt_row gt_center">10.0 (58.8%)</td>
<td class="gt_row gt_center">13.0 (86.7%)</td></tr>
    <tr><td class="gt_row gt_left">    Not sure</td>
<td class="gt_row gt_center">0.0 (0.0%)</td>
<td class="gt_row gt_center">1.0 (4.3%)</td>
<td class="gt_row gt_center">2.0 (5.9%)</td>
<td class="gt_row gt_center">1.0 (5.9%)</td>
<td class="gt_row gt_center">0.0 (0.0%)</td></tr>
    <tr><td class="gt_row gt_left">    Unknown</td>
<td class="gt_row gt_center">0</td>
<td class="gt_row gt_center">0</td>
<td class="gt_row gt_center">1</td>
<td class="gt_row gt_center">0</td>
<td class="gt_row gt_center">0</td></tr>
  </tbody>
  
  <tfoot class="gt_footnotes">
    <tr>
      <td class="gt_footnote" colspan="6"><sup class="gt_footnote_marks">1</sup> n (%)</td>
    </tr>
  </tfoot>
</table>
</div>

---
title: Histograms in ggplot2
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

In the ggplot2 package, there are two geom\_\*() layers that can be created to make histograms, geom_histogram() and geom_bar(). The following focuses on the use of geom_bar() to make different types of histograms. We will use the built-in mtcars data set to demonstrate.

``` r
library(tidyverse)
```

``` r
head(mtcars)
```

    ##                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
    ## Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
    ## Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
    ## Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
    ## Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
    ## Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
    ## Valiant           18.1   6  225 105 2.76 3.460 20.22  1  0    3    1

## Traditional Histogram

``` r
mtcars %>%
  ggplot(., aes(x = cyl)) +
  geom_bar() +
  theme_minimal()
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-3-1.png" width="672" />

## Paneled histogram

Here we make separate panels to show the histogram by am(automatic transmission) where a 1 denotes automatic and 0 denotes manual transmission.

``` r
mtcars %>%
  ggplot(., aes(x = cyl)) +
  geom_bar() +
  facet_wrap(~am) +
  theme_minimal()
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-4-1.png" width="672" />

### Modify the panel labels

To modify the labels, we can convert the variable am to a factor where 1 is set to Automatic and 0 is set to Manual, then setting the order of the levels match the previous plot.

``` r
mtcars %>%
  mutate(am = factor(ifelse(am == 1, "Automatic", "Manual"), levels = c("Manual", "Automatic"))) %>%
  ggplot(., aes(x = cyl)) +
  geom_bar() +
  facet_wrap(~am) +
  theme_minimal()
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-5-1.png" width="672" />

## Clustered histogram

In a this clustered histogram separate bars at each value of cyl are displayed, one for automatic and another for manual. The position option is needed to display the separate bars side by side, otherwise the bars will appear stacked instead.

``` r
colors = c( "#440154FF","#1565c0")
mtcars %>%
  mutate(am = factor(am)) %>%
  ggplot(., aes(x = cyl)) +
  geom_bar(aes(color = am, fill = am), position = position_dodge2(preserve = "single")) +
  theme_minimal() +
  scale_color_manual(values = colors) +
  scale_fill_manual(values = colors)
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-6-1.png" width="672" />

## Clustered histogram with percentages

### Denominator is the grouping variable

There may be situations where a percentage instead of a count facilitates comparisons across groups. In this version, the denominator for calculating a percentage is that of the total number of observations within each level of am. To produce such a plot, we first need to group the data by am and cyl, and then calculated frequencies and proportions. We can see that 4-cylinder vehicles make up about 60% of the observations where the transmission is an automatic one which can be compared to about 15% of 4-cylinder vehicles are equipped with a manual transmission.
<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-7-1.png" width="672" />
To ensure that the appropriate values are displayed, a quick gtsummary() table will display the actual percentages in a table.

``` r
mtcars %>%
  mutate(am = factor(am)) %>%
  select(cyl, am) %>%
  gtsummary::tbl_summary(by = am, digits = list(everything() ~ c(0,2))) 
```

<div id="afxicsoxyz" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>#afxicsoxyz table {
  font-family: system-ui, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol', 'Noto Color Emoji';
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
&#10;#afxicsoxyz thead, #afxicsoxyz tbody, #afxicsoxyz tfoot, #afxicsoxyz tr, #afxicsoxyz td, #afxicsoxyz th {
  border-style: none;
}
&#10;#afxicsoxyz p {
  margin: 0;
  padding: 0;
}
&#10;#afxicsoxyz .gt_table {
  display: table;
  border-collapse: collapse;
  line-height: normal;
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
&#10;#afxicsoxyz .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}
&#10;#afxicsoxyz .gt_title {
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
&#10;#afxicsoxyz .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 3px;
  padding-bottom: 5px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}
&#10;#afxicsoxyz .gt_heading {
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
&#10;#afxicsoxyz .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#afxicsoxyz .gt_col_headings {
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
&#10;#afxicsoxyz .gt_col_heading {
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
&#10;#afxicsoxyz .gt_column_spanner_outer {
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
&#10;#afxicsoxyz .gt_column_spanner_outer:first-child {
  padding-left: 0;
}
&#10;#afxicsoxyz .gt_column_spanner_outer:last-child {
  padding-right: 0;
}
&#10;#afxicsoxyz .gt_column_spanner {
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
&#10;#afxicsoxyz .gt_spanner_row {
  border-bottom-style: hidden;
}
&#10;#afxicsoxyz .gt_group_heading {
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
  text-align: left;
}
&#10;#afxicsoxyz .gt_empty_group_heading {
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
&#10;#afxicsoxyz .gt_from_md > :first-child {
  margin-top: 0;
}
&#10;#afxicsoxyz .gt_from_md > :last-child {
  margin-bottom: 0;
}
&#10;#afxicsoxyz .gt_row {
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
&#10;#afxicsoxyz .gt_stub {
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
&#10;#afxicsoxyz .gt_stub_row_group {
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
&#10;#afxicsoxyz .gt_row_group_first td {
  border-top-width: 2px;
}
&#10;#afxicsoxyz .gt_row_group_first th {
  border-top-width: 2px;
}
&#10;#afxicsoxyz .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#afxicsoxyz .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}
&#10;#afxicsoxyz .gt_first_summary_row.thick {
  border-top-width: 2px;
}
&#10;#afxicsoxyz .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#afxicsoxyz .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#afxicsoxyz .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}
&#10;#afxicsoxyz .gt_last_grand_summary_row_top {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: double;
  border-bottom-width: 6px;
  border-bottom-color: #D3D3D3;
}
&#10;#afxicsoxyz .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}
&#10;#afxicsoxyz .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#afxicsoxyz .gt_footnotes {
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
&#10;#afxicsoxyz .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#afxicsoxyz .gt_sourcenotes {
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
&#10;#afxicsoxyz .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#afxicsoxyz .gt_left {
  text-align: left;
}
&#10;#afxicsoxyz .gt_center {
  text-align: center;
}
&#10;#afxicsoxyz .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}
&#10;#afxicsoxyz .gt_font_normal {
  font-weight: normal;
}
&#10;#afxicsoxyz .gt_font_bold {
  font-weight: bold;
}
&#10;#afxicsoxyz .gt_font_italic {
  font-style: italic;
}
&#10;#afxicsoxyz .gt_super {
  font-size: 65%;
}
&#10;#afxicsoxyz .gt_footnote_marks {
  font-size: 75%;
  vertical-align: 0.4em;
  position: initial;
}
&#10;#afxicsoxyz .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}
&#10;#afxicsoxyz .gt_indent_1 {
  text-indent: 5px;
}
&#10;#afxicsoxyz .gt_indent_2 {
  text-indent: 10px;
}
&#10;#afxicsoxyz .gt_indent_3 {
  text-indent: 15px;
}
&#10;#afxicsoxyz .gt_indent_4 {
  text-indent: 20px;
}
&#10;#afxicsoxyz .gt_indent_5 {
  text-indent: 25px;
}
</style>
<table class="gt_table" data-quarto-disable-processing="false" data-quarto-bootstrap="false">
  <thead>
    <tr class="gt_col_headings">
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col" id="&lt;strong&gt;Characteristic&lt;/strong&gt;"><strong>Characteristic</strong></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="&lt;strong&gt;0&lt;/strong&gt;, N = 19&lt;span class=&quot;gt_footnote_marks&quot; style=&quot;white-space:nowrap;font-style:italic;font-weight:normal;&quot;&gt;&lt;sup&gt;1&lt;/sup&gt;&lt;/span&gt;"><strong>0</strong>, N = 19<span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;"><sup>1</sup></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="&lt;strong&gt;1&lt;/strong&gt;, N = 13&lt;span class=&quot;gt_footnote_marks&quot; style=&quot;white-space:nowrap;font-style:italic;font-weight:normal;&quot;&gt;&lt;sup&gt;1&lt;/sup&gt;&lt;/span&gt;"><strong>1</strong>, N = 13<span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;"><sup>1</sup></span></th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td headers="label" class="gt_row gt_left">cyl</td>
<td headers="stat_1" class="gt_row gt_center"><br /></td>
<td headers="stat_2" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    4</td>
<td headers="stat_1" class="gt_row gt_center">3 (15.79%)</td>
<td headers="stat_2" class="gt_row gt_center">8 (61.54%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    6</td>
<td headers="stat_1" class="gt_row gt_center">4 (21.05%)</td>
<td headers="stat_2" class="gt_row gt_center">3 (23.08%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    8</td>
<td headers="stat_1" class="gt_row gt_center">12 (63.16%)</td>
<td headers="stat_2" class="gt_row gt_center">2 (15.38%)</td></tr>
  </tbody>
  &#10;  <tfoot class="gt_footnotes">
    <tr>
      <td class="gt_footnote" colspan="3"><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;"><sup>1</sup></span> n (%)</td>
    </tr>
  </tfoot>
</table>
</div>

### Denominator is the sample size

The proportion of all observations can also be displayed. In this plot we see that bout 25% of all observations are automatic and 4 cylinder vehicles.

``` r
mtcars %>%
  mutate(am = factor(am)) %>%
  ggplot(., aes(cyl, fill = am)) +
  geom_bar(aes(y = (..count..)/sum(..count..)),
           position = position_dodge2(preserve = "single")) +
  scale_y_continuous(labels = scales::label_percent()) +
  theme_minimal() +
  scale_fill_manual(values = colors) +
  ylab("Percent of all observations") 
```

    ## Warning: The dot-dot notation (`..count..`) was deprecated in ggplot2 3.4.0.
    ## ℹ Please use `after_stat(count)` instead.
    ## This warning is displayed once every 8 hours.
    ## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
    ## generated.

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-9-1.png" width="672" />
To double check the values, we can display a variable in which cyl has been crossed with am.

``` r
mtcars %>%
  mutate(am = factor(ifelse(am == 1, "Automatic", "Manual"), levels = c("Manual", "Automatic"))) %>%
  mutate(var = str_c(cyl, am)) %>%
  select(var) %>%
  gtsummary::tbl_summary()
```

<div id="ybibxpjwiz" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>#ybibxpjwiz table {
  font-family: system-ui, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol', 'Noto Color Emoji';
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
&#10;#ybibxpjwiz thead, #ybibxpjwiz tbody, #ybibxpjwiz tfoot, #ybibxpjwiz tr, #ybibxpjwiz td, #ybibxpjwiz th {
  border-style: none;
}
&#10;#ybibxpjwiz p {
  margin: 0;
  padding: 0;
}
&#10;#ybibxpjwiz .gt_table {
  display: table;
  border-collapse: collapse;
  line-height: normal;
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
&#10;#ybibxpjwiz .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}
&#10;#ybibxpjwiz .gt_title {
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
&#10;#ybibxpjwiz .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 3px;
  padding-bottom: 5px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}
&#10;#ybibxpjwiz .gt_heading {
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
&#10;#ybibxpjwiz .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#ybibxpjwiz .gt_col_headings {
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
&#10;#ybibxpjwiz .gt_col_heading {
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
&#10;#ybibxpjwiz .gt_column_spanner_outer {
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
&#10;#ybibxpjwiz .gt_column_spanner_outer:first-child {
  padding-left: 0;
}
&#10;#ybibxpjwiz .gt_column_spanner_outer:last-child {
  padding-right: 0;
}
&#10;#ybibxpjwiz .gt_column_spanner {
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
&#10;#ybibxpjwiz .gt_spanner_row {
  border-bottom-style: hidden;
}
&#10;#ybibxpjwiz .gt_group_heading {
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
  text-align: left;
}
&#10;#ybibxpjwiz .gt_empty_group_heading {
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
&#10;#ybibxpjwiz .gt_from_md > :first-child {
  margin-top: 0;
}
&#10;#ybibxpjwiz .gt_from_md > :last-child {
  margin-bottom: 0;
}
&#10;#ybibxpjwiz .gt_row {
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
&#10;#ybibxpjwiz .gt_stub {
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
&#10;#ybibxpjwiz .gt_stub_row_group {
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
&#10;#ybibxpjwiz .gt_row_group_first td {
  border-top-width: 2px;
}
&#10;#ybibxpjwiz .gt_row_group_first th {
  border-top-width: 2px;
}
&#10;#ybibxpjwiz .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#ybibxpjwiz .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}
&#10;#ybibxpjwiz .gt_first_summary_row.thick {
  border-top-width: 2px;
}
&#10;#ybibxpjwiz .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#ybibxpjwiz .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#ybibxpjwiz .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}
&#10;#ybibxpjwiz .gt_last_grand_summary_row_top {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: double;
  border-bottom-width: 6px;
  border-bottom-color: #D3D3D3;
}
&#10;#ybibxpjwiz .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}
&#10;#ybibxpjwiz .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#ybibxpjwiz .gt_footnotes {
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
&#10;#ybibxpjwiz .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#ybibxpjwiz .gt_sourcenotes {
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
&#10;#ybibxpjwiz .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#ybibxpjwiz .gt_left {
  text-align: left;
}
&#10;#ybibxpjwiz .gt_center {
  text-align: center;
}
&#10;#ybibxpjwiz .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}
&#10;#ybibxpjwiz .gt_font_normal {
  font-weight: normal;
}
&#10;#ybibxpjwiz .gt_font_bold {
  font-weight: bold;
}
&#10;#ybibxpjwiz .gt_font_italic {
  font-style: italic;
}
&#10;#ybibxpjwiz .gt_super {
  font-size: 65%;
}
&#10;#ybibxpjwiz .gt_footnote_marks {
  font-size: 75%;
  vertical-align: 0.4em;
  position: initial;
}
&#10;#ybibxpjwiz .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}
&#10;#ybibxpjwiz .gt_indent_1 {
  text-indent: 5px;
}
&#10;#ybibxpjwiz .gt_indent_2 {
  text-indent: 10px;
}
&#10;#ybibxpjwiz .gt_indent_3 {
  text-indent: 15px;
}
&#10;#ybibxpjwiz .gt_indent_4 {
  text-indent: 20px;
}
&#10;#ybibxpjwiz .gt_indent_5 {
  text-indent: 25px;
}
</style>
<table class="gt_table" data-quarto-disable-processing="false" data-quarto-bootstrap="false">
  <thead>
    <tr class="gt_col_headings">
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col" id="&lt;strong&gt;Characteristic&lt;/strong&gt;"><strong>Characteristic</strong></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="&lt;strong&gt;N = 32&lt;/strong&gt;&lt;span class=&quot;gt_footnote_marks&quot; style=&quot;white-space:nowrap;font-style:italic;font-weight:normal;&quot;&gt;&lt;sup&gt;1&lt;/sup&gt;&lt;/span&gt;"><strong>N = 32</strong><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;"><sup>1</sup></span></th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td headers="label" class="gt_row gt_left">var</td>
<td headers="stat_0" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    4Automatic</td>
<td headers="stat_0" class="gt_row gt_center">8 (25%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    4Manual</td>
<td headers="stat_0" class="gt_row gt_center">3 (9.4%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    6Automatic</td>
<td headers="stat_0" class="gt_row gt_center">3 (9.4%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    6Manual</td>
<td headers="stat_0" class="gt_row gt_center">4 (13%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    8Automatic</td>
<td headers="stat_0" class="gt_row gt_center">2 (6.3%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    8Manual</td>
<td headers="stat_0" class="gt_row gt_center">12 (38%)</td></tr>
  </tbody>
  &#10;  <tfoot class="gt_footnotes">
    <tr>
      <td class="gt_footnote" colspan="2"><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;"><sup>1</sup></span> n (%)</td>
    </tr>
  </tfoot>
</table>
</div>

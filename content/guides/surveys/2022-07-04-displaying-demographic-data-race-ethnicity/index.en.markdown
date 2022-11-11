---
title: 'Displaying Race & Ethnicity Data'
author: Carlos Rodriguez
date: '2022-11-10'
slug: displaying-demographic-data-race-ethnicity
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2022-11-10 19:49:01'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 20
---

It is not uncommon for surveys to collect race and ethnicity data from participants. In some cases, these questions allow participants to select more than one response often under the instructions of “select all that apply.” This guide covers how to create a table and bar chart in R to display race and ethnicity responses where participants can select more than one option.

### Packages

``` r
library(tidyverse)
library(gtsummary)
library(bstfun)
```

## Create Tables

### Prepare data

The first step is to create a data frame in a way that looks like the table below. In this format, each row represents a participant and each column represents one of the available responses coded as a binary variable where 1 indicates that the participant selected that category. Each participant can then endorse multiple categories.

<table>
<caption>
Table 1: Example data frame.
</caption>
<thead>
<tr>
<th style="text-align:right;">
American Indian or Alaskan Native
</th>
<th style="text-align:right;">
Asian
</th>
<th style="text-align:right;">
Black or African American
</th>
<th style="text-align:right;">
White
</th>
<th style="text-align:right;">
Hispanic or Latino
</th>
<th style="text-align:right;">
Another race
</th>
<th style="text-align:right;">
Prefer not to answer
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
</tr>
<tr>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
</tr>
<tr>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
</tr>
<tr>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
</tr>
<tr>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
</tr>
<tr>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
</tr>
</tbody>
</table>

### Create a vector of the columns to be displayed

Once the data frame is prepared, create a character vector of the column names that contain the responses of interest. In the example data, these column names start with the string “demographic” and contain “race.” However, some of these column names end with “.factor” or contain the string “other” and need to be excluded. To accomplish this, I use the {dplyr} verb `select()` nested within the `names()` function. This vector will be used as an argument in a subsequent step.

``` r
race_vars <- names(data %>% select(starts_with("demographic") & 
                              !ends_with(".factor") & 
                              contains("race") &
                              !contains("other")))
```

    ## [1] "demographic_4_race___1" "demographic_4_race___2" "demographic_4_race___3"
    ## [4] "demographic_4_race___4" "demographic_4_race___5" "demographic_4_race___6"
    ## [7] "demographic_4_race___8"

### Create a tbl_summary() table

To create the table, begin by piping the data to `select()` and use the character vector of column names of the variables of interest. Next, pipe the output to the `tbl_summary()` function from the {gtsummary} package. Finally, pipe the output table to the `add_variable_grouping()` function of the {bstfun} package to indent all of the variables under a grouping named “Race/Ethnicity.” In the example data, all of the colum names are labelled. Thus, even though the columns are named something like “demographic_4\_race\_\_\_1”, the `tbl_summary()` function will display the corresponding label “American Indian or Alaskan Native.”

``` r
data %>% 
  select(all_of(race_vars)) %>%
  tbl_summary() %>%
  add_variable_grouping("Race/Ethnicity" = race_vars)
```

<div id="biejdhkmyc" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#biejdhkmyc .gt_table {
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

#biejdhkmyc .gt_heading {
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

#biejdhkmyc .gt_title {
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

#biejdhkmyc .gt_subtitle {
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

#biejdhkmyc .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#biejdhkmyc .gt_col_headings {
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

#biejdhkmyc .gt_col_heading {
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

#biejdhkmyc .gt_column_spanner_outer {
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

#biejdhkmyc .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#biejdhkmyc .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#biejdhkmyc .gt_column_spanner {
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

#biejdhkmyc .gt_group_heading {
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

#biejdhkmyc .gt_empty_group_heading {
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

#biejdhkmyc .gt_from_md > :first-child {
  margin-top: 0;
}

#biejdhkmyc .gt_from_md > :last-child {
  margin-bottom: 0;
}

#biejdhkmyc .gt_row {
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

#biejdhkmyc .gt_stub {
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

#biejdhkmyc .gt_stub_row_group {
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

#biejdhkmyc .gt_row_group_first td {
  border-top-width: 2px;
}

#biejdhkmyc .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#biejdhkmyc .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#biejdhkmyc .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#biejdhkmyc .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#biejdhkmyc .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#biejdhkmyc .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#biejdhkmyc .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#biejdhkmyc .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#biejdhkmyc .gt_footnotes {
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

#biejdhkmyc .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#biejdhkmyc .gt_sourcenotes {
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

#biejdhkmyc .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#biejdhkmyc .gt_left {
  text-align: left;
}

#biejdhkmyc .gt_center {
  text-align: center;
}

#biejdhkmyc .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#biejdhkmyc .gt_font_normal {
  font-weight: normal;
}

#biejdhkmyc .gt_font_bold {
  font-weight: bold;
}

#biejdhkmyc .gt_font_italic {
  font-style: italic;
}

#biejdhkmyc .gt_super {
  font-size: 65%;
}

#biejdhkmyc .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#biejdhkmyc .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#biejdhkmyc .gt_indent_1 {
  text-indent: 5px;
}

#biejdhkmyc .gt_indent_2 {
  text-indent: 10px;
}

#biejdhkmyc .gt_indent_3 {
  text-indent: 15px;
}

#biejdhkmyc .gt_indent_4 {
  text-indent: 20px;
}

#biejdhkmyc .gt_indent_5 {
  text-indent: 25px;
}
</style>
<table class="gt_table">
  
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col"><strong>Characteristic</strong></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col"><strong>N = 150</strong><sup class="gt_footnote_marks">1</sup></th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left">Race/Ethnicity</td>
<td class="gt_row gt_center"></td></tr>
    <tr><td class="gt_row gt_left">    American Indian or Alaskan Native</td>
<td class="gt_row gt_center">3 (2.0%)</td></tr>
    <tr><td class="gt_row gt_left">    Asian</td>
<td class="gt_row gt_center">9 (6.0%)</td></tr>
    <tr><td class="gt_row gt_left">    Black or African American</td>
<td class="gt_row gt_center">7 (4.7%)</td></tr>
    <tr><td class="gt_row gt_left">    White</td>
<td class="gt_row gt_center">92 (61%)</td></tr>
    <tr><td class="gt_row gt_left">    Hispanic or Latino</td>
<td class="gt_row gt_center">31 (21%)</td></tr>
    <tr><td class="gt_row gt_left">    Another race</td>
<td class="gt_row gt_center">2 (1.3%)</td></tr>
    <tr><td class="gt_row gt_left">    Prefer not to answer</td>
<td class="gt_row gt_center">10 (6.7%)</td></tr>
  </tbody>
  
  <tfoot class="gt_footnotes">
    <tr>
      <td class="gt_footnote" colspan="2"><sup class="gt_footnote_marks">1</sup> n (%)</td>
    </tr>
  </tfoot>
</table>
</div>

### Sort the order by frequency

So far, we have a decent looking table. However, we may want to sort the responses according to the frequency (%). Since our columns consist of binary responses, we can take the sums of each column, and then use the sums to determine the order. One way to accomplish this task, is by piping the columns of interest to the `colSums()` function followed by the `order()` function and assigning it to a vector named index. Next, use the index to order the columns of interest and assign it to a new vectore named ordered_race_vars. Finally, use the ordered_race_vars vector in the `select()` and `add_variable_grouping()` functions as above. All this does is essentially change the order in which the columns are selected and piped into the `tbl_summary()` function.

``` r
index <- data %>% 
  select(all_of(race_vars)) %>%
  colSums() %>%
  order(decreasing = TRUE)

ordered_race_vars <- race_vars[index]

data %>%
  select(all_of(ordered_race_vars)) %>% 
  tbl_summary() %>%
  add_variable_grouping("Race/Ethnicity" = ordered_race_vars)
```

<div id="rayvounifi" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#rayvounifi .gt_table {
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

#rayvounifi .gt_heading {
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

#rayvounifi .gt_title {
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

#rayvounifi .gt_subtitle {
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

#rayvounifi .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#rayvounifi .gt_col_headings {
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

#rayvounifi .gt_col_heading {
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

#rayvounifi .gt_column_spanner_outer {
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

#rayvounifi .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#rayvounifi .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#rayvounifi .gt_column_spanner {
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

#rayvounifi .gt_group_heading {
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

#rayvounifi .gt_empty_group_heading {
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

#rayvounifi .gt_from_md > :first-child {
  margin-top: 0;
}

#rayvounifi .gt_from_md > :last-child {
  margin-bottom: 0;
}

#rayvounifi .gt_row {
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

#rayvounifi .gt_stub {
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

#rayvounifi .gt_stub_row_group {
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

#rayvounifi .gt_row_group_first td {
  border-top-width: 2px;
}

#rayvounifi .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#rayvounifi .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#rayvounifi .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#rayvounifi .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#rayvounifi .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#rayvounifi .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#rayvounifi .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#rayvounifi .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#rayvounifi .gt_footnotes {
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

#rayvounifi .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#rayvounifi .gt_sourcenotes {
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

#rayvounifi .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#rayvounifi .gt_left {
  text-align: left;
}

#rayvounifi .gt_center {
  text-align: center;
}

#rayvounifi .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#rayvounifi .gt_font_normal {
  font-weight: normal;
}

#rayvounifi .gt_font_bold {
  font-weight: bold;
}

#rayvounifi .gt_font_italic {
  font-style: italic;
}

#rayvounifi .gt_super {
  font-size: 65%;
}

#rayvounifi .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#rayvounifi .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#rayvounifi .gt_indent_1 {
  text-indent: 5px;
}

#rayvounifi .gt_indent_2 {
  text-indent: 10px;
}

#rayvounifi .gt_indent_3 {
  text-indent: 15px;
}

#rayvounifi .gt_indent_4 {
  text-indent: 20px;
}

#rayvounifi .gt_indent_5 {
  text-indent: 25px;
}
</style>
<table class="gt_table">
  
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col"><strong>Characteristic</strong></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col"><strong>N = 150</strong><sup class="gt_footnote_marks">1</sup></th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left">Race/Ethnicity</td>
<td class="gt_row gt_center"></td></tr>
    <tr><td class="gt_row gt_left">    White</td>
<td class="gt_row gt_center">92 (61%)</td></tr>
    <tr><td class="gt_row gt_left">    Hispanic or Latino</td>
<td class="gt_row gt_center">31 (21%)</td></tr>
    <tr><td class="gt_row gt_left">    Prefer not to answer</td>
<td class="gt_row gt_center">10 (6.7%)</td></tr>
    <tr><td class="gt_row gt_left">    Asian</td>
<td class="gt_row gt_center">9 (6.0%)</td></tr>
    <tr><td class="gt_row gt_left">    Black or African American</td>
<td class="gt_row gt_center">7 (4.7%)</td></tr>
    <tr><td class="gt_row gt_left">    American Indian or Alaskan Native</td>
<td class="gt_row gt_center">3 (2.0%)</td></tr>
    <tr><td class="gt_row gt_left">    Another race</td>
<td class="gt_row gt_center">2 (1.3%)</td></tr>
  </tbody>
  
  <tfoot class="gt_footnotes">
    <tr>
      <td class="gt_footnote" colspan="2"><sup class="gt_footnote_marks">1</sup> n (%)</td>
    </tr>
  </tfoot>
</table>
</div>

<br>

## Create Bar Charts

### Extract column labels

One way to visually display survey responses to race and ethnicity questions is to use the {ggplot} package. While `tbl_summary()` will play nicely with columns that are labelled, the `geom_*()` functions do not. Thus my strategy is to use the native column names to generate a bar chart, and then use the columns labels to label the plot. The rationale for this approach is avoid additional problems if your eventual column names contain spaces or special characters that tend to not play well with R column-naming conventions.

1.  One option to extract the labels in the example data is to use the `map_df()` function from the {purrr} package in combination with the `attr()` function to extract the labels. The following code chunk essentially applies the `attr()` function to all of the input columns.

``` r
# Alternative form using purrr
race_labels <- as.character(data %>% 
                             select(all_of(race_vars)) %>% 
                             map_df(., ~ attr(.x, "label")))

race_labels
```

    ## [1] "American Indian or Alaskan Native" "Asian"                            
    ## [3] "Black or African American"         "White"                            
    ## [5] "Hispanic or Latino"                "Another race"                     
    ## [7] "Prefer not to answer"

### Prepare data

The next step is to use the race_vars and race_labels in a chain of commands that selects the columns, converts them to numeric, renames them, converts to long format, and then factors the categories. The reason for converting to numeric class here is because the labelled columns do not play well with {dplyr} functions. Converting to numeric essentially clears out any labelled meta data from the column and facilitates any subsequent {dplyr} verbs to be successfully applied to the data. The output shows an example of what the data look like at this step.

``` r
plot_data <- data %>% 
  select(all_of(race_vars)) %>%
  mutate_at(all_of(race_vars), ~ as.numeric(.)) %>% 
  rename_at(all_of(race_vars), ~ race_labels) %>%
  pivot_longer(all_of(race_labels),
               names_to = "Category",
               values_to = "Endorsed") %>%
  mutate(Category = factor(Category, levels = race_labels))
```

    ## # A tibble: 1,050 × 2
    ##    Category                          Endorsed
    ##    <fct>                                <dbl>
    ##  1 American Indian or Alaskan Native        0
    ##  2 Asian                                    0
    ##  3 Black or African American                0
    ##  4 White                                    1
    ##  5 Hispanic or Latino                       0
    ##  6 Another race                             0
    ##  7 Prefer not to answer                     0
    ##  8 American Indian or Alaskan Native        0
    ##  9 Asian                                    0
    ## 10 Black or African American                0
    ## # … with 1,040 more rows

3.  Building from here, the next step is to use the long format date to count the number of responses for each category, and then calculate the proportion of responses out of all participants. This can be accomplished with the `group_by()`, `summarise()`, and `mutate()` functions.

``` r
plot_data <- plot_data %>%
  group_by(Category) %>%
  summarise(Frequency = sum(Endorsed, na.rm = TRUE)) %>%
  mutate(Proportion = Frequency/nrow(data))
```

    ## # A tibble: 7 × 3
    ##   Category                          Frequency Proportion
    ##   <fct>                                 <dbl>      <dbl>
    ## 1 American Indian or Alaskan Native         3     0.02  
    ## 2 Asian                                     9     0.06  
    ## 3 Black or African American                 7     0.0467
    ## 4 White                                    92     0.613 
    ## 5 Hispanic or Latino                       31     0.207 
    ## 6 Another race                              2     0.0133
    ## 7 Prefer not to answer                     10     0.0667

### Plot the prepared data

At this point, we have the basic building blocks to plot the data.

``` r
plot_data %>%
  ggplot(., aes(x = Category, y = Proportion)) +
  geom_col()
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-10-1.png" width="672" />
From here, we can order the bars by Proportion by using the `reorder()` function and add a color and fill and flip the coordinates to fix the overplotted x-axis tick labels.

``` r
plot_data %>%
  ggplot(aes(x = reorder(Category, Proportion), y = Proportion)) +
  geom_col(color = "#1565c0", fill = "#1565c0") +
  coord_flip()
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-11-1.png" width="672" />

From here, we can apply some additional modifications by changing the theme and axis lines.

``` r
plot_data %>%
  ggplot(aes(x = reorder(Category, Proportion), y = Proportion)) +
  geom_col(color = "#1565c0", fill = "#1565c0") +
  coord_flip() +
  xlab("Category") +
  theme_minimal() +
  theme(axis.line = element_line(color = "grey70"))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-12-1.png" width="672" />

---
title: survey_data
author: Carlos Rodriguez
date: '2022-07-04'
slug: survey-data
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2022-07-04T07:46:16-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

1.  Download REDCap data

<!-- -->

1.  will download the actual data in a .csv
2.  an R script that will perform some preprocessing

<!-- -->

2.  Modify the R script

<!-- -->

1.  comment out the rm(list = ls()) line
2.  add library(tidyverse)
3.  trim white space in any of the responses as they will influence how they are displayed when making tables.

``` r
library(tidyverse)
library(gtsummary)
library(bstfun)
```

# Grouped responses tables for race and ethnicity

-   strategy one is to combine some variables, but then it presents the problem
    of what to do with individuals who endorse more than one option
-   tbl_summary will present the number of endorsements out of the entire sample
    rather than responses. we want % of participants not % of responses because some may select more than one

``` r
race_vars <- names(data %>% select(starts_with("demographic") & 
                              !ends_with(".factor") & 
                              contains("race") &
                              !contains("other")))

data %>% 
  select(all_of(race_vars)) %>%
  tbl_summary() %>%
  add_variable_grouping("Race/Ethnicity (check all that apply)" = race_vars)
```

<div id="khioemakyp" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#khioemakyp .gt_table {
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

#khioemakyp .gt_heading {
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

#khioemakyp .gt_title {
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

#khioemakyp .gt_subtitle {
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

#khioemakyp .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#khioemakyp .gt_col_headings {
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

#khioemakyp .gt_col_heading {
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

#khioemakyp .gt_column_spanner_outer {
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

#khioemakyp .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#khioemakyp .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#khioemakyp .gt_column_spanner {
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

#khioemakyp .gt_group_heading {
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

#khioemakyp .gt_empty_group_heading {
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

#khioemakyp .gt_from_md > :first-child {
  margin-top: 0;
}

#khioemakyp .gt_from_md > :last-child {
  margin-bottom: 0;
}

#khioemakyp .gt_row {
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

#khioemakyp .gt_stub {
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

#khioemakyp .gt_stub_row_group {
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

#khioemakyp .gt_row_group_first td {
  border-top-width: 2px;
}

#khioemakyp .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#khioemakyp .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#khioemakyp .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#khioemakyp .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#khioemakyp .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#khioemakyp .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#khioemakyp .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#khioemakyp .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#khioemakyp .gt_footnotes {
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

#khioemakyp .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#khioemakyp .gt_sourcenotes {
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

#khioemakyp .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#khioemakyp .gt_left {
  text-align: left;
}

#khioemakyp .gt_center {
  text-align: center;
}

#khioemakyp .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#khioemakyp .gt_font_normal {
  font-weight: normal;
}

#khioemakyp .gt_font_bold {
  font-weight: bold;
}

#khioemakyp .gt_font_italic {
  font-style: italic;
}

#khioemakyp .gt_super {
  font-size: 65%;
}

#khioemakyp .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#khioemakyp .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#khioemakyp .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#khioemakyp .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#khioemakyp .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#khioemakyp .gt_fraction_denominator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: -0.05em;
}
</style>
<table class="gt_table">
  
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1"><strong>Characteristic</strong></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1"><strong>N = 202</strong><sup class="gt_footnote_marks">1</sup></th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left">Race/Ethnicity (check all that apply)</td>
<td class="gt_row gt_center"></td></tr>
    <tr><td class="gt_row gt_left" style="text-align: left; text-indent: 10px;">American Indian or Alaskan Native</td>
<td class="gt_row gt_center">6 (3.0%)</td></tr>
    <tr><td class="gt_row gt_left" style="text-align: left; text-indent: 10px;">Asian</td>
<td class="gt_row gt_center">9 (4.5%)</td></tr>
    <tr><td class="gt_row gt_left" style="text-align: left; text-indent: 10px;">Black or African American</td>
<td class="gt_row gt_center">11 (5.4%)</td></tr>
    <tr><td class="gt_row gt_left" style="text-align: left; text-indent: 10px;">White</td>
<td class="gt_row gt_center">122 (60%)</td></tr>
    <tr><td class="gt_row gt_left" style="text-align: left; text-indent: 10px;">Hispanic or Latino</td>
<td class="gt_row gt_center">45 (22%)</td></tr>
    <tr><td class="gt_row gt_left" style="text-align: left; text-indent: 10px;">Another race</td>
<td class="gt_row gt_center">4 (2.0%)</td></tr>
    <tr><td class="gt_row gt_left" style="text-align: left; text-indent: 10px;">Prefer not to answer</td>
<td class="gt_row gt_center">14 (6.9%)</td></tr>
  </tbody>
  
  <tfoot class="gt_footnotes">
    <tr>
      <td class="gt_footnote" colspan="2"><sup class="gt_footnote_marks">1</sup> n (%)</td>
    </tr>
  </tfoot>
</table>
</div>

``` r
race_names <- c("American Indian or Alaskan Native", "Asian", "Black or African American",
         "White", "Hispanic or Latino", "Another race", "Prefer not to answer")
data %>% 
  select(all_of(race_vars)) %>%
  mutate_at(all_of(race_vars), ~as.numeric(.)) %>%
  rename("American Indian or Alaskan Native" = demographic_4_race___1,
         "Asian" = demographic_4_race___2,
         "Black or African American" = demographic_4_race___3,
         "White" = demographic_4_race___4,
         "Hispanic or Latino" = demographic_4_race___5,
         "Another race" = demographic_4_race___6,
         "Prefer not to answer" = demographic_4_race___8) %>%
  pivot_longer(`American Indian or Alaskan Native`:`Prefer not to answer`,
               names_to = "Category",
               values_to = "Endorsed") %>%
  mutate(Category = factor(Category, levels = race_names)) %>%
  group_by(Category) %>%
  summarise(Freq = sum(Endorsed)) %>%
  mutate(Proportion = Freq/nrow(data)) %>%
  ggplot(aes(x = Category, y = Proportion)) +
  geom_col(color = "#1565c0", fill = "#1565c0", alpha = 0.3) +
  coord_flip() +
  theme_minimal() +
  theme(axis.line = element_line(color = "grey70"))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/plot of race/ethnicity-1.png" width="672" />

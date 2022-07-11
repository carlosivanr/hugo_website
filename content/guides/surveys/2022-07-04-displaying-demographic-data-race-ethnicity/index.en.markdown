---
title: 'Displaying Race & Ethnicity Data'
author: Carlos Rodriguez
date: '2022-07-10'
slug: displaying-demographic-data-race-ethnicity
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2022-07-10 23:05:13'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

It is not uncommon for surveys to collect race and ethnicity data from participants. In some cases, these questions can allow participants to select multiple responses if they would like to endorse more than one race or ethnicity. In this post, I will cover how to create a table and bar chart to display race and ethnicity data.

### Packages

``` r
library(tidyverse)
library(gtsummary)
library(bstfun)
```

### Create a data frame

The first step in accomplishing this task is to prepare the data frame in a way that looks like the example. In this preparation, each row represent a participant and each column represent a distinct race or ethnic category coded as a binary value (i.e. 0 or 1, where 1 indicates that the participant belongs to that category).

<table>
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
1
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
1
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

### Tables

For displaying race and ethnicity data in a table, I use a combination of the [gt_summary](https://www.danieldsjoberg.com/gtsummary/) and [btsfun](https://www.danieldsjoberg.com/bstfun/index.html) packages. First create a character vector of the columns names that contain the race and ethnicity responses. In my sample data, these columns start with “demographic” and contain “race.” However, I want to exclude some of the columns that end with a “.factor” and contain the string “other.” I then wrap this selection with the `names()` function to assign the column names to a character vector. This vector will be used as an argument in a function called `add_variable_grouping()` and it comes in handy when using the select() verb to subset the data.

``` r
race_vars <- names(data %>% select(starts_with("demographic") & 
                              !ends_with(".factor") & 
                              contains("race") &
                              !contains("other")))
race_vars
```

    ## [1] "demographic_4_race___1" "demographic_4_race___2" "demographic_4_race___3"
    ## [4] "demographic_4_race___4" "demographic_4_race___5" "demographic_4_race___6"
    ## [7] "demographic_4_race___8"

Next, I begin by piping my data set to the select the columns in the character vector I created previously. Next, I pipe that data through the `tbl_summary()` function. Finally, I pipe that table to the `add_variable_grouping()` function to indent all of the columns under a grouping variable, “Race/Ethnicity.” The data displayed here are labelled, so even though the columns are named something like “demographic_4\_race\_\_\_\*“, the `tbl_summary()` function will display the corresponding labels.

``` r
data %>% 
  select(all_of(race_vars)) %>%
  tbl_summary() %>%
  add_variable_grouping("Race/Ethnicity" = race_vars)
```

<div id="lreraharci" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#lreraharci .gt_table {
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

#lreraharci .gt_heading {
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

#lreraharci .gt_title {
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

#lreraharci .gt_subtitle {
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

#lreraharci .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#lreraharci .gt_col_headings {
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

#lreraharci .gt_col_heading {
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

#lreraharci .gt_column_spanner_outer {
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

#lreraharci .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#lreraharci .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#lreraharci .gt_column_spanner {
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

#lreraharci .gt_group_heading {
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

#lreraharci .gt_empty_group_heading {
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

#lreraharci .gt_from_md > :first-child {
  margin-top: 0;
}

#lreraharci .gt_from_md > :last-child {
  margin-bottom: 0;
}

#lreraharci .gt_row {
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

#lreraharci .gt_stub {
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

#lreraharci .gt_stub_row_group {
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

#lreraharci .gt_row_group_first td {
  border-top-width: 2px;
}

#lreraharci .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#lreraharci .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#lreraharci .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#lreraharci .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#lreraharci .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#lreraharci .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#lreraharci .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#lreraharci .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#lreraharci .gt_footnotes {
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

#lreraharci .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#lreraharci .gt_sourcenotes {
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

#lreraharci .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#lreraharci .gt_left {
  text-align: left;
}

#lreraharci .gt_center {
  text-align: center;
}

#lreraharci .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#lreraharci .gt_font_normal {
  font-weight: normal;
}

#lreraharci .gt_font_bold {
  font-weight: bold;
}

#lreraharci .gt_font_italic {
  font-style: italic;
}

#lreraharci .gt_super {
  font-size: 65%;
}

#lreraharci .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#lreraharci .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#lreraharci .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#lreraharci .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#lreraharci .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#lreraharci .gt_fraction_denominator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: -0.05em;
}
</style>
<table class="gt_table">
  
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1"><strong>Characteristic</strong></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1"><strong>N = 150</strong><sup class="gt_footnote_marks">1</sup></th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left">Race/Ethnicity</td>
<td class="gt_row gt_center"></td></tr>
    <tr><td class="gt_row gt_left" style="text-align: left; text-indent: 10px;">American Indian or Alaskan Native</td>
<td class="gt_row gt_center">4 (2.7%)</td></tr>
    <tr><td class="gt_row gt_left" style="text-align: left; text-indent: 10px;">Asian</td>
<td class="gt_row gt_center">6 (4.0%)</td></tr>
    <tr><td class="gt_row gt_left" style="text-align: left; text-indent: 10px;">Black or African American</td>
<td class="gt_row gt_center">8 (5.3%)</td></tr>
    <tr><td class="gt_row gt_left" style="text-align: left; text-indent: 10px;">White</td>
<td class="gt_row gt_center">91 (61%)</td></tr>
    <tr><td class="gt_row gt_left" style="text-align: left; text-indent: 10px;">Hispanic or Latino</td>
<td class="gt_row gt_center">34 (23%)</td></tr>
    <tr><td class="gt_row gt_left" style="text-align: left; text-indent: 10px;">Another race</td>
<td class="gt_row gt_center">3 (2.0%)</td></tr>
    <tr><td class="gt_row gt_left" style="text-align: left; text-indent: 10px;">Prefer not to answer</td>
<td class="gt_row gt_center">12 (8.0%)</td></tr>
  </tbody>
  
  <tfoot class="gt_footnotes">
    <tr>
      <td class="gt_footnote" colspan="2"><sup class="gt_footnote_marks">1</sup> n (%)</td>
    </tr>
  </tfoot>
</table>
</div>

<br>

### Bar Charts

The process for making bar charts can be a bit more complicated depending on the packages and functions you use. The approach demonstrated here is based on tidyverse functions only. The first step in making bar charts is to create a character vector of the labels assigned to my data. I use a simple for-loop to go through each column in my race_vars vector and assign its corresponding label to a new vector called race_names.

``` r
# Use the vector of race_vars to get the column labels
race_names <- ""
for (i in 1:length(race_vars)){
  race_names[i] <- attr(data[, race_vars[i]], "label")
}
race_names
```

    ## [1] "American Indian or Alaskan Native" "Asian"                            
    ## [3] "Black or African American"         "White"                            
    ## [5] "Hispanic or Latino"                "Another race"                     
    ## [7] "Prefer not to answer"

The data that I am working with in this example contains label attributes that sometimes don’t play well with some tidyverse functions. So after selecting the race_vars columns, I convert them to numeric with the `mutate_at()`. Next, I rename the selected columns with the race_names vector. From there, I convert my wide data frame to long using the `pivot_longer()` verb. Since the columns have been renamed in the previous step, the new names have to be used to select the columns to pivot longer to. In the long data frame, the separate race/ethnicity columns are collapsed into one column called “Category” and a second column called “Endorsed” that indicates whether or not that person endorsed one of the race/ethnicity options. From there, one could do a couple of things. The first option is to convert the Category into a factor, and use the levels argument to display the responses in a specific order rather than the default alphabetical order. The other option is to use reorder() within the ggplot call to order the responses by proportion. Both options are shown here, but the reorder() function take precedence. After setting the levels, I group by category and `summarise()` the endorsement values by adding them up. Next, I create a new column called proportion that is Freq over the number of participants given by the number of rows in the data frame, not responses.

Finally, the remaining data frame is ready to be piped into `ggplot()` where I will subsequently flip the coordinates to display horizontal bars and change the x label to “Category” with the `xlab()` layer. The color and fill arguments inside `geom_col()`, `theme_minimal()`, and axis.line argument of the `theme()`layer make up the finishing touches to this plot.

``` r
data %>% 
  select(all_of(race_vars)) %>%
  mutate_at(all_of(race_vars), ~ as.numeric(.)) %>% 
  rename_at(all_of(race_vars), ~ race_names) %>%
  pivot_longer(`American Indian or Alaskan Native`:`Prefer not to answer`,
               names_to = "Category",
               values_to = "Endorsed") %>%
  mutate(Category = factor(Category, levels = race_names)) %>%
  group_by(Category) %>%
  summarise(Freq = sum(Endorsed, na.rm = TRUE)) %>%
  mutate(Proportion = Freq/nrow(data)) %>%
  ggplot(aes(x = reorder(Category, Proportion), y = Proportion)) +
  geom_col(color = "#1565c0", fill = "#1565c0") +
  coord_flip() +
  theme_minimal() +
  xlab("Category") +
  theme(axis.line = element_line(color = "grey70"))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/plot of race/ethnicity-1.png" width="672" />

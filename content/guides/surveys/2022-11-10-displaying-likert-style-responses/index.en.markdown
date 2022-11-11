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

Surveys often contain the responses to a given question in a Likert style format where the available responses are something like “Strongly Agree”, “Agree”, “Neutral”, “Disagree”, or “Strongly Disagree.” The following represents a “cheat-sheet” for using {ggplot} to display these responses in a bar chart.

## Bar chart with perecentage of factored responses relative to the whole sample

This version calculates and displays the percent of responses from the entire survey sample. The key for this type of chart is to set group = 1 in the `aes()` call, set clip = “off” in the `coord_flip()` layer in combination with setting limits in the `scale_y_continuous()` layer to prevent the percent labels from getting clipped. The legend and x label (which is actually on the y axis as display because of `coord_flip()` have been removed for simplicity. Also, the `theme()` axis.text.x setting is in place in case the x-axis ticks need to be rotated to prevent overplotting.
**Requires:**
- Factored responses that are ordered in the way they need to appear in the chart

``` r
data %>%
  drop_na(sample_question_1.factor) %>%
  ggplot(., aes(sample_question_1.factor, group = 1)) +
  geom_bar(aes(y = ..prop.., fill = factor(..x..)), position = position_dodge()) +
  geom_text(aes(label = scales::percent(..prop..), y= ..prop.. ), stat= "count", size = 3,
              hjust = -.15, colour = "black") +
  coord_flip(clip = "off") +
  scale_y_continuous(labels = scales::percent, limits = c(0, 1)) + # limits extends the chart to prevent clipping 
  labs(y = "Percent", x = "") +
  theme_minimal() +
  theme(legend.position = "top", axis.text.x = element_text(angle = -0, hjust = 0)) +
  guides(fill = "none")
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-2-1.png" width="672" />

## Faceted bar chart faceted by a grouping variable

This version is an extension of basic bar chart, but adds the `facet_wrap()` layer to display responses to a grouping variable. In this example, the survey responses were collected from various “sites” that can be displayed separately. This option can be useful if the number of grouping variables is small. The two different `geom_bar()` layers control which variable to fill with color, either the response or the grouping variable (site).

``` r
data %>%
  drop_na(sample_question_1.factor, site) %>%
  ggplot(., aes(sample_question_1.factor, group = 1)) +
  #geom_bar(aes(y = ..prop.., fill = site), position = position_dodge()) + # Applies fill to site
  geom_bar(aes(y = ..prop.., fill = factor(..x..)), position = position_dodge()) + # Applies fill to response
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

This style displays the same information as the faceted bar chart above, but places all of the bars in one panel. Again, this style works best when the number of values in a grouping variable is small to prevent over crowding each x-axis tick.

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

Rather than faceting by a grouping variable, this style of chart will create multiple panels for multiple survey items. The key to this approach is to select the columns that are to be displayed and then converting the data to long format. Once the data are in long format, the remaining percentage calculations can be accomplished within the `geom_bar()` layer and displayed by the `geom_text()` layer. This style can be useful for displaying related survey items that a reader may want to compare.

**Requires:**
- At least two input columns of with ordered and factored responses with the same number of levels.

``` r
data %>% 
  select(sample_question_2.factor:sample_question_3.factor) %>%
  pivot_longer(cols = everything(),
               names_to = "level",
               values_to = "Response") %>%
  drop_na() %>%
  ggplot(., aes(Response, group = 1)) +
  geom_bar(aes(y = ..prop.., fill = factor(..x..)), position = position_dodge()) +
  geom_text(aes(label = scales::percent(..prop.., accuracy = 0.1L), y= ..prop.. ), stat= "count", size = 3,
              hjust = -.15, colour = "black") +
  scale_y_continuous(labels = scales::percent, limits = c(0, 1)) +
  coord_flip() +
  theme_minimal() +
  guides(fill = "none") +
  labs(y = "Percent", x = "") +
  facet_wrap(~level)
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-5-1.png" width="672" />
Responses in this type of char can be double checked with a call to `tbl_likert()` from the {bstfun} package. However, note that the rounding between `tbl_liker()` and the ggplot call will be slightly off due to rounding error.

``` r
data %>% 
  select(sample_question_2.factor:sample_question_3.factor) %>%
  tbl_likert(digits = list(everything() ~ 1))
```

<div id="nqliperxli" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#nqliperxli .gt_table {
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

#nqliperxli .gt_heading {
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

#nqliperxli .gt_title {
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

#nqliperxli .gt_subtitle {
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

#nqliperxli .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#nqliperxli .gt_col_headings {
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

#nqliperxli .gt_col_heading {
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

#nqliperxli .gt_column_spanner_outer {
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

#nqliperxli .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#nqliperxli .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#nqliperxli .gt_column_spanner {
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

#nqliperxli .gt_group_heading {
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

#nqliperxli .gt_empty_group_heading {
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

#nqliperxli .gt_from_md > :first-child {
  margin-top: 0;
}

#nqliperxli .gt_from_md > :last-child {
  margin-bottom: 0;
}

#nqliperxli .gt_row {
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

#nqliperxli .gt_stub {
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

#nqliperxli .gt_stub_row_group {
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

#nqliperxli .gt_row_group_first td {
  border-top-width: 2px;
}

#nqliperxli .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#nqliperxli .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#nqliperxli .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#nqliperxli .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#nqliperxli .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#nqliperxli .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#nqliperxli .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#nqliperxli .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#nqliperxli .gt_footnotes {
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

#nqliperxli .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#nqliperxli .gt_sourcenotes {
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

#nqliperxli .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#nqliperxli .gt_left {
  text-align: left;
}

#nqliperxli .gt_center {
  text-align: center;
}

#nqliperxli .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#nqliperxli .gt_font_normal {
  font-weight: normal;
}

#nqliperxli .gt_font_bold {
  font-weight: bold;
}

#nqliperxli .gt_font_italic {
  font-style: italic;
}

#nqliperxli .gt_super {
  font-size: 65%;
}

#nqliperxli .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#nqliperxli .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#nqliperxli .gt_indent_1 {
  text-indent: 5px;
}

#nqliperxli .gt_indent_2 {
  text-indent: 10px;
}

#nqliperxli .gt_indent_3 {
  text-indent: 15px;
}

#nqliperxli .gt_indent_4 {
  text-indent: 20px;
}

#nqliperxli .gt_indent_5 {
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
<td class="gt_row gt_center">2.0 (1.4%)</td>
<td class="gt_row gt_center">58.0 (40.8%)</td>
<td class="gt_row gt_center">74.0 (52.1%)</td>
<td class="gt_row gt_center">8.0 (5.6%)</td></tr>
    <tr><td class="gt_row gt_left">sample_question_3.factor</td>
<td class="gt_row gt_center">2.0 (1.4%)</td>
<td class="gt_row gt_center">54.0 (37.8%)</td>
<td class="gt_row gt_center">83.0 (58.0%)</td>
<td class="gt_row gt_center">4.0 (2.8%)</td></tr>
  </tbody>
  
  <tfoot class="gt_footnotes">
    <tr>
      <td class="gt_footnote" colspan="5"><sup class="gt_footnote_marks">1</sup> n (%)</td>
    </tr>
  </tfoot>
</table>
</div>

## Bar chart with grouping variable and faceted by related survey item

Building off of the previous chart, this style will facet two or more survey items and include the percent of responses relative to the grouping variable. Like the preceding chart, a key to this approach is convert the columns of interest into long format. From there, the next step is to group by all variables to count the number of responses. Next, we want to ungroup and drop the NAs followed by grouping again by the item (level) and grouping variable (site). Next, are two instances of using the mutate verb. One is to calculate the numerical percentage and the other is to create the label to display on top of the bar.

**Requires:**
- Two or more factored and ordered survey responses
- One grouping variable

``` r
data %>% 
  select(sample_question_2.factor:sample_question_3.factor, site) %>%
  pivot_longer(cols = sample_question_2.factor:sample_question_3.factor,
               names_to = "level",
               values_to = "response") %>%
  group_by(response, site, level) %>%
  summarise(freq = n()) %>%
  ungroup() %>%
  drop_na() %>%
  group_by(level, site) %>%
  mutate(prop = round(freq/sum(freq, na.rm = T),3) * 100) %>% # Get the % to plot
  mutate(prop_label = scales::percent(freq/sum(freq, na.rm = T), accuracy = 0.1L)) %>% # Get the % label
  ggplot(., aes(x = response, y = prop, fill = site, label = prop_label)) +
  geom_col(position = "dodge2") +
  geom_text(position = position_dodge(.9), size = 3, hjust = -.1) +
  coord_flip(clip = "off") +
  scale_y_continuous(labels = scales::percent_format(scale = 1), limits = c(0, 100)) +
  theme_minimal() +
  guides(fill = guide_legend(title = "Site")) +
  theme(legend.position = "top", axis.text.x = element_text(angle = -0, hjust = 0)) +
  labs(y = "Percent", x = "") +
  facet_wrap(~level)
```

    ## `summarise()` has grouped output by 'response', 'site'. You can override using
    ## the `.groups` argument.

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-7-1.png" width="672" />

To display

``` r
data %>% 
  select(sample_question_2.factor:sample_question_3.factor, site) %>%
  tbl_summary(by = "site",
              digits = all_categorical() ~ 1)
```

    ## 8 observations missing `site` have been removed. To include these observations, use `forcats::fct_explicit_na()` on `site` column before passing to `tbl_summary()`.

<div id="ykotobopqj" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#ykotobopqj .gt_table {
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

#ykotobopqj .gt_heading {
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

#ykotobopqj .gt_title {
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

#ykotobopqj .gt_subtitle {
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

#ykotobopqj .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#ykotobopqj .gt_col_headings {
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

#ykotobopqj .gt_col_heading {
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

#ykotobopqj .gt_column_spanner_outer {
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

#ykotobopqj .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#ykotobopqj .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#ykotobopqj .gt_column_spanner {
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

#ykotobopqj .gt_group_heading {
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

#ykotobopqj .gt_empty_group_heading {
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

#ykotobopqj .gt_from_md > :first-child {
  margin-top: 0;
}

#ykotobopqj .gt_from_md > :last-child {
  margin-bottom: 0;
}

#ykotobopqj .gt_row {
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

#ykotobopqj .gt_stub {
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

#ykotobopqj .gt_stub_row_group {
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

#ykotobopqj .gt_row_group_first td {
  border-top-width: 2px;
}

#ykotobopqj .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#ykotobopqj .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#ykotobopqj .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#ykotobopqj .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#ykotobopqj .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#ykotobopqj .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#ykotobopqj .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#ykotobopqj .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#ykotobopqj .gt_footnotes {
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

#ykotobopqj .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#ykotobopqj .gt_sourcenotes {
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

#ykotobopqj .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#ykotobopqj .gt_left {
  text-align: left;
}

#ykotobopqj .gt_center {
  text-align: center;
}

#ykotobopqj .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#ykotobopqj .gt_font_normal {
  font-weight: normal;
}

#ykotobopqj .gt_font_bold {
  font-weight: bold;
}

#ykotobopqj .gt_font_italic {
  font-style: italic;
}

#ykotobopqj .gt_super {
  font-size: 65%;
}

#ykotobopqj .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#ykotobopqj .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#ykotobopqj .gt_indent_1 {
  text-indent: 5px;
}

#ykotobopqj .gt_indent_2 {
  text-indent: 10px;
}

#ykotobopqj .gt_indent_3 {
  text-indent: 15px;
}

#ykotobopqj .gt_indent_4 {
  text-indent: 20px;
}

#ykotobopqj .gt_indent_5 {
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

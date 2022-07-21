---
title: 'Displaying Race & Ethnicity Data'
author: Carlos Rodriguez
date: '2022-07-14'
slug: displaying-demographic-data-race-ethnicity
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2022-07-14 07:57:17'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---








It is not uncommon for surveys to collect race and ethnicity data from participants. In some cases, these questions can allow participants to select multiple responses if they would like to endorse more than one race or ethnicity. In this post, I will cover how to create a table and bar chart to display race and ethnicity data.


### Packages

```r
library(tidyverse)
library(gtsummary)
library(bstfun)
```

### Tables
For displaying race and ethnicity data in a table, I use a combination of the [gt_summary](https://www.danieldsjoberg.com/gtsummary/) and [btsfun](https://www.danieldsjoberg.com/bstfun/index.html) packages. In order to use these packages, my first step is to create a data frame in a way that looks like the table below. In this preparation, each row represent a participant and each column represent a distinct race or ethnic category coded as a binary variable where 1 indicates that the participant selected that category. Each participant can then endorse multiple categories.

<table>
<caption>Table 1: The aim to create a data frame like this.</caption>
 <thead>
  <tr>
   <th style="text-align:right;"> American Indian or Alaskan Native </th>
   <th style="text-align:right;"> Asian </th>
   <th style="text-align:right;"> Black or African American </th>
   <th style="text-align:right;"> White </th>
   <th style="text-align:right;"> Hispanic or Latino </th>
   <th style="text-align:right;"> Another race </th>
   <th style="text-align:right;"> Prefer not to answer </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
</tbody>
</table>

1. My example data are already formatted in such a way, so my first step will be to create a character vector of the columns names that contain the responses of interest. In my example data, these columns start with "demographic" and contain "race." However, I want to exclude some of the columns that end with a ".factor" and contain the string "other." I then wrap this selection with the `names()` function to assign the column names to a character vector. This vector will be used as an argument in a subsequent step and it comes in handy when using the `select()` verb to subset the data.

```r
race_vars <- names(data %>% select(starts_with("demographic") & 
                              !ends_with(".factor") & 
                              contains("race") &
                              !contains("other")))
race_vars
```

```
## [1] "demographic_4_race___1" "demographic_4_race___2" "demographic_4_race___3"
## [4] "demographic_4_race___4" "demographic_4_race___5" "demographic_4_race___6"
## [7] "demographic_4_race___8"
```

2. Next, I begin piping the data to `select()` using the vector of variables created in Step 1 and then through the `tbl_summary()` function. Then, we can pipe the output table to the `add_variable_grouping()` function to indent all of the columns under a grouping variable, "Race/Ethnicity", and once again uses our vector of variables from Step 1. The example data displayed here are labelled, so even though the columns are named something like "demographic_4_race___*", the `tbl_summary()` function will display the corresponding labels automatically.

```r
data %>% 
  select(all_of(race_vars)) %>%
  tbl_summary() %>%
  add_variable_grouping("Race/Ethnicity" = race_vars)
```

<table>
<caption>Table 2: Percent of participants endorsing a category.</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> Characteristic </th>
   <th style="text-align:center;"> N = 150 </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Race/Ethnicity </td>
   <td style="text-align:center;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> American Indian or Alaskan Native </td>
   <td style="text-align:center;"> 4 (2.7%) </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Asian </td>
   <td style="text-align:center;"> 5 (3.3%) </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Black or African American </td>
   <td style="text-align:center;"> 7 (4.7%) </td>
  </tr>
  <tr>
   <td style="text-align:left;"> White </td>
   <td style="text-align:center;"> 93 (62%) </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Hispanic or Latino </td>
   <td style="text-align:center;"> 29 (19%) </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Another race </td>
   <td style="text-align:center;"> 2 (1.3%) </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Prefer not to answer </td>
   <td style="text-align:center;"> 10 (6.7%) </td>
  </tr>
</tbody>
</table>

<br>

### Bar Charts
One way to display this type of information is in a bar char that displays the percent of participants that endorse any given category. Unlike `tbl_summary()` where the columns labels are automatically displayed, `ggplot()` requires manually setting labels if the column names aren't presentable.

1. As a result, my first step is to get the labels of the columns. I show two different approaches in getting labels. The first is through a for-loop and the second is through the `map_df()` function in the purrr package. If the data are not labelled, then creating a vector of labels can still be helpful if you want to preserve your column names for what ever reason and simply change the labels on the plot.

```r
# Use the vector of race_vars to get the column labels
race_names <- ""
for (i in 1:length(race_vars)){
  race_names[i] <- attr(data[, race_vars[i]], "label")
}

# Alternative form using purrr
race_names <- as.character(data %>% 
                             select(all_of(race_vars)) %>% 
                             map_df(., ~attr(.x, "label"))
                           )

race_names
```

```
## [1] "American Indian or Alaskan Native" "Asian"                            
## [3] "Black or African American"         "White"                            
## [5] "Hispanic or Latino"                "Another race"                     
## [7] "Prefer not to answer"
```


2. My next step is to use the race_vars and race_names in a chain of commands that selects the columns, converts them numeric, renames them, converts to long format, and then factors the categories The reason for converting numeric here is because the labelled columns some times do not play well with tidyverse functions. Converting to numeric facilitates the use of the `rename_at()` verb with the vector of variable names. The output shows an example of what the data look like at this step.

```r
data %>% 
  select(all_of(race_vars)) %>%
  mutate_at(all_of(race_vars), ~ as.numeric(.)) %>% 
  rename_at(all_of(race_vars), ~ race_names) %>%
  pivot_longer(all_of(race_names),
               names_to = "Category",
               values_to = "Endorsed") %>%
  mutate(Category = factor(Category, levels = race_names))
```

```
## # A tibble: 1,050 × 2
##    Category                          Endorsed
##    <fct>                                <dbl>
##  1 American Indian or Alaskan Native        0
##  2 Asian                                    0
##  3 Black or African American                1
##  4 White                                    0
##  5 Hispanic or Latino                       1
##  6 Another race                             0
##  7 Prefer not to answer                     0
##  8 American Indian or Alaskan Native        0
##  9 Asian                                    0
## 10 Black or African American                0
## # … with 1,040 more rows
```



3. Building from there, the next step is to group by category, summarise, and calculate the proportion of participants that endorse a particular category


```r
data %>% 
  select(all_of(race_vars)) %>%
  mutate_at(all_of(race_vars), ~ as.numeric(.)) %>% 
  rename_at(all_of(race_vars), ~ race_names) %>%
  pivot_longer(all_of(race_names),
               names_to = "Category",
               values_to = "Endorsed") %>%
  mutate(Category = factor(Category, levels = race_names)) %>%
  group_by(Category) %>%
  summarise(Frequency = sum(Endorsed, na.rm = TRUE)) %>%
  mutate(Proportion = Frequency/nrow(data))
```

```
## # A tibble: 7 × 3
##   Category                          Frequency Proportion
##   <fct>                                 <dbl>      <dbl>
## 1 American Indian or Alaskan Native         4     0.0267
## 2 Asian                                     5     0.0333
## 3 Black or African American                 7     0.0467
## 4 White                                    93     0.62  
## 5 Hispanic or Latino                       29     0.193 
## 6 Another race                              2     0.0133
## 7 Prefer not to answer                     10     0.0667
```

4. The other option is to use `reorder()` within the ggplot call to order the responses by proportion. Both options are shown here, but the `reorder()` function is what is displayed as it happens later in the chain of commands. After setting the levels, I group by category and `summarise()` the sum of participants that endorsed a given category in a new column called "Frequency". Next, I create a new column called "Proportion" that is "Frequency" over the number of participants given by the number of rows in the data frame, not responses.

3. Finally, the remaining data frame is ready to be piped into `ggplot()` where I will subsequently flip the coordinates to display horizontal bars and change the x label to "Category" with the `xlab()` layer. The color and fill arguments inside `geom_col()`, `theme_minimal()`, and the axis.line argument of the `theme()`functions add the finishing touches to this plot. 




```r
data %>% 
  select(all_of(race_vars)) %>%
  mutate_at(all_of(race_vars), ~ as.numeric(.)) %>% 
  rename_at(all_of(race_vars), ~ race_names) %>%
  pivot_longer(all_of(race_names),
               names_to = "Category",
               values_to = "Endorsed") %>%
  mutate(Category = factor(Category, levels = race_names)) %>%
  group_by(Category) %>%
  summarise(Frequency = sum(Endorsed, na.rm = TRUE)) %>%
  mutate(Proportion = Frequency/nrow(data)) %>%
  ggplot(aes(x = reorder(Category, Proportion), y = Proportion)) +
  geom_col(color = "#1565c0", fill = "#1565c0") +
  coord_flip() +
  theme_minimal() +
  xlab("Category") +
  theme(axis.line = element_line(color = "grey70"))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/plot of race/ethnicity-1.png" width="672" />

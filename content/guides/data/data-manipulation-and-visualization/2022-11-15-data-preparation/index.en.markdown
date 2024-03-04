---
title: Data Preparation
author: Carlos Rodriguez
date: '2022-11-15'
slug: data-preparation
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2022-11-15T07:14:39-07:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
draft: False
weight: 5
---

# Common data preparation tasks

```r
pacman::p_load(tidyverse,
               magrittr,
               haven,
               install = FALSE)
```



## Dealing with NAs


<!-- ### na_if() -->

### replace_na()
Replaces any NA value with the desired character or integer.
- Requires character or integer class vector
- Does not work with factors, use `fct_explicit_na()` 
- Does not work with labelled variables, convert to numeric or character first

**Multiple variables**

If data are labelled, first convert to numeric. Useful for the preparation of binary variables.

```r
data %>%
  mutate(across(sample_question_1.integer:sample_question_3.integer, ~ as.numeric(.))) %>% #convert to numeric or character if variables are labelled
  mutate(across(sample_question_1.integer:sample_question_3.integer, ~ replace_na(., 0)))
```

### fct_explicit_na()
Replaces any NA value in a factored variable with "Missing". This is useful in cases where one needs to plot "Missing" values because the default for {ggplot2} is to drop any values with NAs.

- Requires factored variable

**One variable case**

```r
data %>% 
  mutate(sample_question_1.factor = fct_explicit_na(sample_question_1.factor, "Missing"))
```

**Multiple variables**

```r
data %>% 
  mutate(across(sample_question_1.factor:sample_question_3.factor, ~ fct_explicit_na(., "Missing")))
```

### drop_na()
The `drop_na()` function eliminates any rows that contain an NA value in the input variable. Useful for filtering before passing the output to {ggplot2} functions.

**One variable case**

```r
data %>%
  drop_na(sample_question_1.factor)
```

**Multiple variables**

```r
data %>%
  drop_na(sample_question_1.factor:sample_question_3.factor)
```


## Recoding values
### recode_()
Old values are placed on the let-hand side (LHS) while new values are placed on the right-hand side (RHS) of the recode statements.
- Useful for converting numeric class variables to character. 
- Does not work with labelled variables. Convert to numeric or character class first.
- Integers are encased with back ticks.

**One variable case**

```r
data %>%
  mutate(sample_question_1.integer = as.numeric(sample_question_1.integer)) %>% #convert to numeric or character if variables are labelled
  mutate(sample_question_1.integer = recode(sample_question_1.integer,
                                            `1` = "Strongly Disagree", 
                                            `2` = "Disagree",
                                            `3` = "Neutral",
                                            `4` = "Agree",
                                            `5` = "Strongly Agree"))            
```


**Multiple variables**
In addition to the requirements of the one variable case, the `recode()` function requires that all variables have the same set of values.

```r
data %>%
  mutate(across(sample_question_1.integer:sample_question_3.integer, ~ as.numeric(.))) %>%
  mutate(across(sample_question_1.integer:sample_question_3.integer, ~ recode(.,
                                            `1` = "Strongly Disagree", 
                                            `2` = "Disagree",
                                            `3` = "Neutral",
                                            `4` = "Agree",
                                            `5` = "Strongly Agree")))
```


<!-- ### ifelse() -->
<!-- ### if_else() -->
<!-- - For dates because it preserves the input type -->

<!-- ### collapse_() -->

## Renaming columns
Renaming columns can be useful before piping data into {ggplot2} functions to avoid having to set x- and y-axis labels.

### rename()
- New values are placed the LHS, while old values are placed on the RHS which is the opposite of `recode()`

```r
data %>%
  rename("New name: Q1" = sample_question_1.factor,
         "New name: Q2" = sample_question_2.factor,
         "New name: Q3" = sample_question_3.factor)
```

### rename_at()

```r
#set the old column names that will be renamed
col_names <- names(data %>% select(sample_question_1.factor:sample_question_3.factor))

# Set the new col names that will be used
labels <- c("Q1", "Q2", "Q3")
  
# Rename the columns by the the values in labels
data %>%
  rename_at(all_of(col_names), ~ labels)
```


## Row-wise operations
### rowSums()
**Multiple variables**
- Requires integer class variables
- Limited to sums

```r
# In my experience rowSums() is fastest, but it's limited to sums
data %>%
  mutate(row_sum = select(., sample_question_1.integer:sample_question_3:integer)) %>% rowSums(., na.rm = TRUE)
```

### rowwise()
**Multiple variables**

Functions similar to the `group_by()` function and modifies how other {dplyr} verbs work.

```r
# Example using the select() verb and everything() function
data %>%
  select(sample_question_1.integer:sample_question_3:integer) %>% 
  rowwise() %>% 
  mutate(sum = sum(c_across(everything()), na.rm = TRUE))

# Example using select() verb within c_across()
data %>% 
  rowwise() %>% 
  mutate(sum = sum(c_across(sample_question_1.integer:sample_question_3:integer), na.rm = TRUE))
```

## Strip characters

```r
ids <- c("[2987202982]", "[2123402982]", "[2009283471]" )
ids <- as.data.frame(ids)
```

### Remove everything to the right of the bracket

```r
ids %>%
  mutate(ids = sub("].*", "", ids))
```

```
##           ids
## 1 [2987202982
## 2 [2123402982
## 3 [2009283471
```

### Remove everthing to the left

```r
ids %>%
  mutate(ids = sub(".*\\[", "", ids))
```

```
##           ids
## 1 2987202982]
## 2 2123402982]
## 3 2009283471]
```


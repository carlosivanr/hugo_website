---
title: Filtering rows with strings in Python
author: Carlos Rodriguez
date: '2021-09-21'
slug: filtering-rows-with-strings-in-python
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-09-21T17:45:18-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 50
draft: true
---


In my research, I sometimes run into situations where I have to filter out a list of subjects from a larger one. This typically occurs when I need to select a sub-sample to undergo additional processing or to conduct a statistical anlysis on. There are a number of ways to tackle this problem, but in this post I'll cover one way to filter a list of strings with another list of strings in Python.




### Filter a list of strings with another
This approach will rely on the pandas package. After importing the package, I created a data frame from a list of strings representing subject numbers. In the wild, this would probably be done by importing a .txt or .csv file, but your mileage may vary. Next, I created a second data frame from a list of subjects that I would like to omit. Let's suppose that these subjects have poor quality data and we would like to exclude them from analysis. From there I created a function `filter_out()` that takes two inputs. The first input is the larger list to filter, and the second input is a list of subject to be removed. To understand this function, I like to begin with the `isin()` function.  This function will tell us which rows match a set of input values (the subjects to omit). It returns a boolean series (True or False). Since these are the rows we would like to omit, we simply invert that series to create an index and use it to select the rows. The rationale for creating a function is to re-use it while writing scripts. There are a number of ways to branch off from here, but having a way to select sub-samples like this is always a good tool to have.


```python
import pandas as pd

# Create pandas data frames of subject lists
all_subjects = pd.DataFrame(
['Sub_18', 'Sub_20', 'Sub_23', 'Sub_26', 'Sub_28', 'Sub_29', 
 'Sub_42', 'Sub_43', 'Sub_48', 'Sub_49', 'Sub_61', 'Sub_73', 
 'Sub_74', 'Sub_75', 'Sub_79', 'Sub_86', 'Sub_91', 'Sub_94', 
 'Sub_96', 'Sub_98', 'Sub_02'])

subjects_to_omit = pd.DataFrame(['Sub_48', 'Sub_49', 'Sub_79'])

# Filter function; filters y out of x
def filter_out(x, y):
    return x[0][~x[0].isin(y[0])]

# Create the sub sample as a pandas data frame
sub_sample = pd.DataFrame(filter_out(all_subjects, subjects_to_omit))

# Alternative form without a function. In Python, zero indexes the first column
#sub_sample = all_subjects[0][~all_subjects[0].isin(subjects_to_omit[0])]
```


```python
# Display lengths of the data frames to show how the've changed
len(all_subjects)
```

```
## 21
```

```python
len(sub_sample)
```

```
## 18
```

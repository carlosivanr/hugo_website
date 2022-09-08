---
title: R Snippets
author: Carlos Rodriguez
date: '2022-06-14'
slug: r-snippets
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2022-06-14T20:01:06-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
---


One of my favorite features in RStudio is the code snippet. This feature allows users to save templates of code that can be used to save you time. Maybe there's a function or chain of piped commands that one uses frequently. Instead of building these from scratch every time, the code snippet can be used to serve as a guide post and modified for each situation. Some snippets are already baked into RStudio, but one can also create your own for your own needs.

## Examples of snippets

### Not in function
I frequently use the dplyr `%in%` operator to filter rows from one data frame by a vector in another data frame. However, sometimes it's nice to be able to do the opposite and filter a dataframe by values that are not in a vector. An example may be when I need to find a set of participant IDs that are not in another list. One solution that has been proposed for this task is to negate the %in% operator with a function.

```r
library(dplyr)
`%!in%` = Negate(`%in%`)
```
While this certainly gets the job done, I never liked having to declare this function before its use. Rather than creating a negate function, one could negate `%in%` in a call to the filter verb. Below is the general approach where the exclamation point is placed before the column to be filtered. I would read this as "filter rows where the homeworld is not in worlds_of_interest".


```r
worlds_of_interest = c("Tatooine", "Naboo")
```

```r
# Filter rows that do not match worlds_of_interest
filter(starwars, !homeworld %in% worlds_of_interest)
```


<table>
 <thead>
  <tr>
   <th style="text-align:left;"> name </th>
   <th style="text-align:right;"> height </th>
   <th style="text-align:right;"> mass </th>
   <th style="text-align:left;"> hair_color </th>
   <th style="text-align:left;"> skin_color </th>
   <th style="text-align:left;"> eye_color </th>
   <th style="text-align:right;"> birth_year </th>
   <th style="text-align:left;"> sex </th>
   <th style="text-align:left;"> gender </th>
   <th style="text-align:left;"> homeworld </th>
   <th style="text-align:left;"> species </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Leia Organa </td>
   <td style="text-align:right;"> 150 </td>
   <td style="text-align:right;"> 49 </td>
   <td style="text-align:left;"> brown </td>
   <td style="text-align:left;"> light </td>
   <td style="text-align:left;"> brown </td>
   <td style="text-align:right;"> 19 </td>
   <td style="text-align:left;"> female </td>
   <td style="text-align:left;"> feminine </td>
   <td style="text-align:left;"> Alderaan </td>
   <td style="text-align:left;"> Human </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Obi-Wan Kenobi </td>
   <td style="text-align:right;"> 182 </td>
   <td style="text-align:right;"> 77 </td>
   <td style="text-align:left;"> auburn, white </td>
   <td style="text-align:left;"> fair </td>
   <td style="text-align:left;"> blue-gray </td>
   <td style="text-align:right;"> 57 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:left;"> masculine </td>
   <td style="text-align:left;"> Stewjon </td>
   <td style="text-align:left;"> Human </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Wilhuff Tarkin </td>
   <td style="text-align:right;"> 180 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:left;"> auburn, grey </td>
   <td style="text-align:left;"> fair </td>
   <td style="text-align:left;"> blue </td>
   <td style="text-align:right;"> 64 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:left;"> masculine </td>
   <td style="text-align:left;"> Eriadu </td>
   <td style="text-align:left;"> Human </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Chewbacca </td>
   <td style="text-align:right;"> 228 </td>
   <td style="text-align:right;"> 112 </td>
   <td style="text-align:left;"> brown </td>
   <td style="text-align:left;"> unknown </td>
   <td style="text-align:left;"> blue </td>
   <td style="text-align:right;"> 200 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:left;"> masculine </td>
   <td style="text-align:left;"> Kashyyyk </td>
   <td style="text-align:left;"> Wookiee </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Han Solo </td>
   <td style="text-align:right;"> 180 </td>
   <td style="text-align:right;"> 80 </td>
   <td style="text-align:left;"> brown </td>
   <td style="text-align:left;"> fair </td>
   <td style="text-align:left;"> brown </td>
   <td style="text-align:right;"> 29 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:left;"> masculine </td>
   <td style="text-align:left;"> Corellia </td>
   <td style="text-align:left;"> Human </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Greedo </td>
   <td style="text-align:right;"> 173 </td>
   <td style="text-align:right;"> 74 </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> green </td>
   <td style="text-align:left;"> black </td>
   <td style="text-align:right;"> 44 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:left;"> masculine </td>
   <td style="text-align:left;"> Rodia </td>
   <td style="text-align:left;"> Rodian </td>
  </tr>
</tbody>
</table>

An alternative approach where the data frame is piped into the filter verb will produce the same results.

```r
# Filter rows that do not match via pipe operator
starwars %>%
  filter(!homeworld %in% worlds_of_interest)
```



## How to create your own snippets

1. Go to Tools in the RStudio menu bar
2. Select Global Options
3. Select Code in the left hand pane
4. Select on the Edit Snippets button at the bottom of the window
5. The next windows should display the R code snippets.


```r
# my snippets ---------------------------------------
snippet notin
	df %>% filter(!${1:col_name} %in% ${2:col_name2})
```

In my code snippet, beginning to type `"notin"` in RStudio will present auto complete options. If I press tab to autocomplete the snippet, it will complete the function where the function has been typed out and all that is needed is to fill in the place-holders, `"df"`, `"col_name"`, and `"col_name2"`. In the snippets file, each variable that will need to be filled in is encased in curly braces preceding with a dollar sign, and each variable is also preceeded with a number and a colon. The numbers are the order of variables. I saved this snipped towards the top of my snippets file within a section called "my snippets."

I hope this gets the point across of how snippets can be useful to save you time while coding. They're especially usefull for code chunks that are often reused or for functions that you want to avoid looking up when you need them.

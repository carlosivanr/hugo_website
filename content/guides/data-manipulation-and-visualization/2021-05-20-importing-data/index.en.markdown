---
title: Importing Data in R
author: Carlos Rodriguez
date: '2021-05-20'
slug: importing-data
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-05-20T07:22:55-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 5
---

R can import a number of different file types for data wrangling or analyses. The following is a go-to guide for importing .csv, Excel, SPSS, .txt, and .dat files. Occassionally, additional packages may be require to import a specific type of file.

 
  
  

<!-- -----------------------TABS---------------------------------- -->
{{< tabs tabTotal="5" tabName1="CSV" tabName2="Excel" tabName3="SPSS" tabName4="TXT" tabName5="DAT">}}


<!-- -----------------------Tab 1---------------------------------- -->
{{< tab tabNum="1" >}}
<!-- ### read.table() -->
<!-- ```{r, eval=FALSE} -->
<!-- # Read csv files -->
<!-- library(readr) -->
<!-- #read_csv("file_name.csv") -->
<!-- ``` -->

### read.csv()
`read.csv()` is a base R function that can import .csv files so there is no need to load a package. The code snippet below will import "file_name.csv" and assign it to an object called data.

```r
# Read csv files
data <- read_csv("file_name.csv")
```

### read_csv()
`read_csv()` is from the readr package. The distinction between `read.csv()` and `read_csv()` is that the latter will read a .csv file as a tibble. Tibbles are data frames that have been modified to work well with other tidyverse functions. In fact, readr is part of the tidyverse package and the following code will work when either the tidyverse or readr package is loaded. In my experience, the only drawback to using `read_csv()` is that in can take much longer to read in large .csv files. So the size of the file you are working with and whether or not you need it as a tibble may help you decide which function to use. One thing about the `read_csv()` is that it may produce messages if the column types aren't specified before hand. This can get annoying if you have lots of columns in your file. To avoid the message add the `col_types = cols()` argument.

```r
# Read csv files
install.packages("readr")
library(readr)

# Read .csv file
data <- read_csv("file_name.csv")

# Read .csv files without Parsed column specification output
data <- read_csv("file_name.csv", col_types = cols())
```
{{< /tab >}}


<!-- -----------------------Tab 2---------------------------------- -->
{{< tab tabNum="2" >}}
### read_excel()
To read Excel files, use the readxl package and the `read_excel()` function. This approach will also let you specify what sheet to load, otherwise it defaults to the first sheet.

```r
# Read excel files
install.packages("readxl")
library("readxl")

# Read .xlsx file
read_excel("file_name.xlsx")
```

### read.xlsx()
Another route is to use openxlsx package. I haven't used this package much for importing data, but have used it to write multiple data frames to sheets in an excel file. If you run into any java related errors when working with Excel files, this would be the one I'd try.

```r
install.packages("openxlsx")
library(openxlsx) #for outputting tables, avoid java errors

# Read .xlsx file
read.xlsx("file_name.xlsx", sheet = 1)
```
{{< /tab >}}


<!-- -----------------------Tab 3---------------------------------- -->
{{< tab tabNum="3" >}}
### read_sav()
To load SPSS (.sav), SAS, or Stata files use the haven package. The haven package is also part of the tidyverse so the following function works when loading either the tidyverse or haven package.

```r
# Read commercial statistical analysis software files
install.packages("haven")
library(haven)

# Read .sav file
data <- read_sav("file_name.sav")
```
{{< /tab >}}


<!-- -----------------------Tab 4---------------------------------- -->
{{< tab tabNum="4" >}}
### read.delim()
To load .txt files, the base R function `read.delim()` does the job. Depending on the file you are working with, you may need to specify whether there is a header and specific separator. The header option will need to be set to TRUE in cases where the first row contains column names. As for the separator, its often a tab (\"\\t\").

```r
# Read .txt files
data <- read.delim("file_name.txt", header = TRUE, sep = "\t")
```
{{< /tab >}}

<!-- -----------------------Tab 5---------------------------------- -->
{{< tab tabNum="5" >}}
### read.table()
Every now and then I run into .dat files. To load these in R, I tend to use the `read.table()` function.

```r
# Read .txt files
data <- read.table("file_name.dat", header = TRUE)
```
{{< /tab >}}


{{< /tabs >}}

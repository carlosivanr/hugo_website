---
title: SAS
author: Carlos Rodriguez
date: '2022-06-16'
slug: sas
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2022-06-16T08:47:00-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---






```r
library(SASmarkdown)

saspath <- "C:/Program Files/SASHome/SASFoundation/9.4/sas.exe"
sasopts <- "-nosplash -linesize 75"
knitr::opts_chunk$set(engine="sashtml", 
                      engine.path=saspath,
                      engine.opts=sasopts, 
                      comment=NA)


# see what the initial knitr engine settings are
knitr::opts_chunk$get()$engine
knitr::opts_chunk$get()$engine.path
knitr::opts_chunk$get()$engine.opts
```



```sas
DATA test;
INPUT pid age gender$;
CARDS;
100 45 Male
200 50 Male
300 50 Female
;
RUN;

PROC PRINT DATA = test;
RUN;

/* Task is to know what the frequency of males and females 
- encapsulate strings in quotes
- TABLES will take a variable, otherwise all variables will be displayed */
PROC FREQ DATA = test;
WHERE gender = "Male";
TABLES gender;
RUN;

PROC MEANS DATA = test;
VAR age;
RUN;

# PROC GCHART DATA = test;
# VBAR3d gender;
# RUN;

```

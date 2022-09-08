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
draft: true
type: book

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


```r
# this chunk has the engine set back to `R`

# run a short bit of r code
# scatterplot of cars dataset
plot(cars)
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-3-1.png" width="672" />

```r
# check the current engine
knitr::opts_chunk$get()$engine
```

```
## [1] "R"
```

```r
# reset the engine globally (i.e. so it will work outside of
# this chunk so you don't have to keep typing engine=`R`)
knitr::opts_chunk$set(engine="R",  engine.path=NULL, 
        engine.opts=NULL, comment=NA)

# confirm that this change was applied
knitr::opts_chunk$get()$engine
```

```
## [1] "R"
```

```r
knitr::opts_chunk$get()$engine.path
```

```
## NULL
```

```r
knitr::opts_chunk$get()$engine.opts
```

```
## NULL
```

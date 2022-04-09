---
title: Getting started with reticulate
author: Carlos Rodriguez
date: '2021-09-18'
slug: setup-reticulate
categories: []
tags: []
subtitle: ''
summary: 'This post briefly describes a few steps to get R and Python working with each other.'
authors: []
lastmod: '2021-09-18T05:32:51-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 55
draft: True
---

One of the nice things about RStudio is that it can work with Python via the reticulate package. This is a really useful feature that allows one to use the best of both R and Python worlds.

### 1. Install the reticulate package
Once you have a working Python installation, getting started with reticulate is straightforward. The first step will be to install the "reticulate" package in RStudio. 

```r
install.packages("reticulate")
```

### 2. Specify which Python to use
After installing and loading the reticulate package, the `use_python()` function can be used to set the path for your python installation. If you're working on a Mac or Linux system, the command which python should list the path to python on your system. You can then copy paste that into the command and execute it in RStudio.

```r
library(reticulate)
use_python("/usr/local/bin/python")
```
### 3. Set which Python environment to use
The final step is to set which environment to use since it is recommended to create separate Python environments containing all of your working packages.

```r
use_virtualenv("your-virtual-env")
```

### References
https://rstudio.github.io/reticulate/
https://bookdown.org/yihui/rmarkdown-cookbook/eng-python.html

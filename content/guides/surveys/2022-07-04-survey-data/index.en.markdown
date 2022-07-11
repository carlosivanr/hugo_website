---
title: Preparing Survey Data
author: Carlos Rodriguez
date: '2022-07-04'
slug: survey-data
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2022-07-04T07:46:16-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
draft: true
---



1. Download REDCap data
  a. will download the actual data in a .csv
  b. an R script that will perform some preprocessing
  
2. Modify the R script
  a. comment out the rm(list = ls()) line
  b. add library(tidyverse)
  c. trim white space in any of the responses as they will influence how they are displayed when making tables.
  
  






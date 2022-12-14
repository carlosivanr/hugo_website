---
title: Setup RedCapR API
author: Carlos Rodriguez
date: '2022-09-20'
slug: redcapr-setup
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: "December 13, 2022"
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
draft: true
weight: 25
---



## Load library

```r
library(REDCapR)
```

## Create a template file named credentials

```r
# Creates an example local credential file
create_credential_local(
  "credentials"
  )
```

## Modify the template file
- Remove all rows except the first one
- Replace uri with "https://redcap.ucdenver.edu/api/"
- Replace username with your own
- Enter the project ID, (found on the RedCap project website)
- Enter a comment to describe the project or leave it blank with empty ""
- Save the file in a secure location, where it may be able to be accessed by multiple Git R projects
- Ensure that it is ignored by any version control system

## Create a credential object

```r
# set path to credential file
path_credential <- "./credentials"

credential  <- retrieve_credential_local(
  path_credential = path_credential,
  project_id      = 25177
)
```


## Read in the REDCap data

```r
data <-
  redcap_read(
    redcap_uri  = credential$redcap_uri,
    token       = credential$token
  )$data
```




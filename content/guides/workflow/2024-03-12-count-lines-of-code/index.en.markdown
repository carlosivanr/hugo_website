---
title: Count Lines of Code
author: Carlos Rodriguez
date: '2024-03-12'
slug: count-lines-of-code
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2024-03-12T21:32:31-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

Windows Powershell
- In Windows Powershell, navigate to the directory containing the files to count
- Use the following command which will count the lines of code in all .R script files
```console
dir -Recurse *.R | Get-Content | Measure-Object -Line
```


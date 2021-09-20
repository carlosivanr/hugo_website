---
# Documentation: https://wowchemy.com/docs/managing-content/

title: Detection of prenatal alcohol exposure using machine learning classification
  of resting-state functional network connectivity data
subtitle: ''
summary: ''
authors:
- C. I. Rodriguez
- V. M. Vergara
- S. Davies
- V. D. Calhoun
- D. D. Savage
- D. A. Hamilton
tags: []
categories: []
date: '2021-01-01'
lastmod: 2021-04-24T16:25:35-06:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ''
  focal_point: ''
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
publishDate: '2021-04-24T22:25:34.921460Z'
#publication_types:
#- '2'
abstract: ''
#publication: '*Alcohol*'
url_pdf: media/rodriguez_2021_pae_svm.pdf
doi: 10.1016/j.alcohol.2021.03.001
---

# Summary
In this study, we utilized resting state fMRI acquired from rodents exposed to moderate levels of prenatal alcohol and saccharin exposed controls. After standard preprocessing procedures, we applied group independent component analysis to extract independent components representing functional networks. The average network timecourses were then zero-lagged cross correlated to measure functional network connectivity (FNC). FNC measures were then utilized as features for binary classification using support vector machines. We found that a quadratic SVM correctly classified alcohol exposed females at a rate of about 80%. Our data provide evidence that machine learning techniques may prove useful for the detection of prenatal alcohol exposure.

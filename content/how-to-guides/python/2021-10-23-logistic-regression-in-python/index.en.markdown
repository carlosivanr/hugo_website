---
title: Logistic Regression in Python
author: Carlos Rodriguez
date: '2021-10-23'
slug: logistic-regression-in-python
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-10-23T19:57:25-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 70
draft: False
---






### Import packages

```python
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import classification_report
```

### Load data

```python
ad_data = pd.read_csv("advertising.csv")
```

### Display the first three rows

```python
ad_data.head(3)
```
<table>
 <thead>
  <tr>
   <th style="text-align:right;"> Daily Time Spent on Site </th>
   <th style="text-align:right;"> Age </th>
   <th style="text-align:right;"> Area Income </th>
   <th style="text-align:right;"> Daily Internet Usage </th>
   <th style="text-align:left;"> Ad Topic Line </th>
   <th style="text-align:left;"> City </th>
   <th style="text-align:right;"> Male </th>
   <th style="text-align:left;"> Country </th>
   <th style="text-align:left;"> Timestamp </th>
   <th style="text-align:right;"> Clicked on Ad </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 68.95 </td>
   <td style="text-align:right;"> 35 </td>
   <td style="text-align:right;"> 61833.90 </td>
   <td style="text-align:right;"> 256.09 </td>
   <td style="text-align:left;"> Cloned 5thgeneration orchestration </td>
   <td style="text-align:left;"> Wrightburgh </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:left;"> Tunisia </td>
   <td style="text-align:left;"> 2016-03-27 00:53:11 </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 80.23 </td>
   <td style="text-align:right;"> 31 </td>
   <td style="text-align:right;"> 68441.85 </td>
   <td style="text-align:right;"> 193.77 </td>
   <td style="text-align:left;"> Monitored national standardization </td>
   <td style="text-align:left;"> West Jodi </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> Nauru </td>
   <td style="text-align:left;"> 2016-04-04 01:39:02 </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 69.47 </td>
   <td style="text-align:right;"> 26 </td>
   <td style="text-align:right;"> 59785.94 </td>
   <td style="text-align:right;"> 236.50 </td>
   <td style="text-align:left;"> Organic bottom-line service-desk </td>
   <td style="text-align:left;"> Davidton </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:left;"> San Marino </td>
   <td style="text-align:left;"> 2016-03-13 20:35:42 </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
</tbody>
</table>

### Display Info

```python
ad_data.info()
```

```
## <class 'pandas.core.frame.DataFrame'>
## RangeIndex: 1000 entries, 0 to 999
## Data columns (total 10 columns):
##  #   Column                    Non-Null Count  Dtype  
## ---  ------                    --------------  -----  
##  0   Daily Time Spent on Site  1000 non-null   float64
##  1   Age                       1000 non-null   int64  
##  2   Area Income               1000 non-null   float64
##  3   Daily Internet Usage      1000 non-null   float64
##  4   Ad Topic Line             1000 non-null   object 
##  5   City                      1000 non-null   object 
##  6   Male                      1000 non-null   int64  
##  7   Country                   1000 non-null   object 
##  8   Timestamp                 1000 non-null   object 
##  9   Clicked on Ad             1000 non-null   int64  
## dtypes: float64(3), int64(3), object(4)
## memory usage: 78.2+ KB
```

### Describe Data

```python
ad_data.describe()
```

```
##        Daily Time Spent on Site          Age  ...         Male  Clicked on Ad
## count               1000.000000  1000.000000  ...  1000.000000     1000.00000
## mean                  65.000200    36.009000  ...     0.481000        0.50000
## std                   15.853615     8.785562  ...     0.499889        0.50025
## min                   32.600000    19.000000  ...     0.000000        0.00000
## 25%                   51.360000    29.000000  ...     0.000000        0.00000
## 50%                   68.215000    35.000000  ...     0.000000        0.50000
## 75%                   78.547500    42.000000  ...     1.000000        1.00000
## max                   91.430000    61.000000  ...     1.000000        1.00000
## 
## [8 rows x 6 columns]
```

### Histogram of Age

```python
sns.histplot(ad_data, x = "Age", bins = 30)
```

```
## <matplotlib.axes._subplots.AxesSubplot object at 0x7ffe1b7f9780>
```

```python
plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-1.png" alt="Histogram of Age." width="672" />
<p class="caption">Figure 1: Histogram of Age.</p>
</div>


### Jointplot of Age and Area Income

```python
sns.jointplot(data=ad_data, x = "Age", y = "Area Income", height = 5)
```

```
## <seaborn.axisgrid.JointGrid object at 0x7ffe1b87f278>
```

```python
plt.tight_layout()
plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-3.png" alt="Jointplot of Age and Area Income." width="480" />
<p class="caption">Figure 2: Jointplot of Age and Area Income.</p>
</div>


### Kernel Density Estimation Plot of Age and Area Income

```python
sns.jointplot(data=ad_data, x = "Age", y = "Area Income", kind = 'kde', height = 5)
```

```
## <seaborn.axisgrid.JointGrid object at 0x7ffe1bc23710>
```

```python
plt.tight_layout()
plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-5.png" alt="KDE plot of Age and Area Income." width="480" />
<p class="caption">Figure 3: KDE plot of Age and Area Income.</p>
</div>


### Jointplot of Daily Time Spent on Site and Daily Internet Usage

```python
sns.jointplot(data = ad_data, x = "Daily Time Spent on Site", y = "Daily Internet Usage", height = 5)
```

```
## <seaborn.axisgrid.JointGrid object at 0x7ffe1c525a20>
```

```python
plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-7.png" alt="Jointplot plot of Daily Time Spent on Site and Daily Internet Usage." width="480" />
<p class="caption">Figure 4: Jointplot plot of Daily Time Spent on Site and Daily Internet Usage.</p>
</div>


### Pairplot of ad_data

```python
sns.pairplot(data = ad_data, hue = "Clicked on Ad", height = 6)
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/plot5-9.png" alt="Pairplot of ad_data." width="1540" />
<p class="caption">Figure 5: Pairplot of ad_data.</p>
</div>


### Split data into training and testing sets

```python
#ad_data.columns # used to print out columns to select the predictor variables
X = ad_data[['Daily Time Spent on Site', 'Age', 'Area Income', 'Daily Internet Usage', 'Male']]
y = ad_data["Clicked on Ad"]
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=101)
```

### Train and fit logistic regression model to the training set

```python
logmodel = LogisticRegression() #creates an instantiation of logistic regression model
logmodel.fit(X_train,y_train)
```

```
## LogisticRegression()
```

### Predictions and evaluations

```python
predictions = logmodel.predict(X_test)
```

```python
print(classification_report(y_test,predictions))
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> precision </th>
   <th style="text-align:right;"> recall </th>
   <th style="text-align:right;"> f1-score </th>
   <th style="text-align:right;"> support </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 0.9141104 </td>
   <td style="text-align:right;"> 0.9490446 </td>
   <td style="text-align:right;"> 0.9312500 </td>
   <td style="text-align:right;"> 157.0000000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:right;"> 0.9416058 </td>
   <td style="text-align:right;"> 0.9020979 </td>
   <td style="text-align:right;"> 0.9214286 </td>
   <td style="text-align:right;"> 143.0000000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> accuracy </td>
   <td style="text-align:right;"> 0.9266667 </td>
   <td style="text-align:right;"> 0.9266667 </td>
   <td style="text-align:right;"> 0.9266667 </td>
   <td style="text-align:right;"> 0.9266667 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> macro avg </td>
   <td style="text-align:right;"> 0.9278581 </td>
   <td style="text-align:right;"> 0.9255712 </td>
   <td style="text-align:right;"> 0.9263393 </td>
   <td style="text-align:right;"> 300.0000000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> weighted avg </td>
   <td style="text-align:right;"> 0.9272166 </td>
   <td style="text-align:right;"> 0.9266667 </td>
   <td style="text-align:right;"> 0.9265685 </td>
   <td style="text-align:right;"> 300.0000000 </td>
  </tr>
</tbody>
</table>

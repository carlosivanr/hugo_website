---
title: K Nearest Neighbors
author: Carlos Rodriguez
date: '2021-10-26'
slug: k-nearest-neighbors
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-10-26T20:59:00-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 75
draft: true
---

### Import packages

``` python
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.neighbors import KNeighborsClassifier
from sklearn.metrics import classification_report, confusion_matrix
```

### Load data

``` python
df = pd.read_csv("KNN_Project_Data")
```

``` python
df.head(3)
```

<table>

<thead>

<tr>

<th style="text-align:right;">

XVPM

</th>

<th style="text-align:right;">

GWYH

</th>

<th style="text-align:right;">

TRAT

</th>

<th style="text-align:right;">

TLLZ

</th>

<th style="text-align:right;">

IGGA

</th>

<th style="text-align:right;">

HYKR

</th>

<th style="text-align:right;">

EDFS

</th>

<th style="text-align:right;">

GUUB

</th>

<th style="text-align:right;">

MGJM

</th>

<th style="text-align:right;">

JHZC

</th>

<th style="text-align:right;">

TARGET CLASS

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:right;">

1636.671

</td>

<td style="text-align:right;">

817.9885

</td>

<td style="text-align:right;">

2565.995

</td>

<td style="text-align:right;">

358.3472

</td>

<td style="text-align:right;">

550.4175

</td>

<td style="text-align:right;">

1618.871

</td>

<td style="text-align:right;">

2147.6413

</td>

<td style="text-align:right;">

330.7279

</td>

<td style="text-align:right;">

1494.879

</td>

<td style="text-align:right;">

845.1361

</td>

<td style="text-align:right;">

0

</td>

</tr>

<tr>

<td style="text-align:right;">

1013.403

</td>

<td style="text-align:right;">

577.5873

</td>

<td style="text-align:right;">

2644.141

</td>

<td style="text-align:right;">

280.4282

</td>

<td style="text-align:right;">

1161.8734

</td>

<td style="text-align:right;">

2084.108

</td>

<td style="text-align:right;">

853.4050

</td>

<td style="text-align:right;">

447.1576

</td>

<td style="text-align:right;">

1193.033

</td>

<td style="text-align:right;">

861.0818

</td>

<td style="text-align:right;">

1

</td>

</tr>

<tr>

<td style="text-align:right;">

1300.036

</td>

<td style="text-align:right;">

820.5187

</td>

<td style="text-align:right;">

2025.854

</td>

<td style="text-align:right;">

525.5623

</td>

<td style="text-align:right;">

922.2063

</td>

<td style="text-align:right;">

2552.355

</td>

<td style="text-align:right;">

818.6767

</td>

<td style="text-align:right;">

845.4915

</td>

<td style="text-align:right;">

1968.368

</td>

<td style="text-align:right;">

1647.1863

</td>

<td style="text-align:right;">

1

</td>

</tr>

</tbody>

</table>

### Exploratory Data Analysis

``` python
sns.pairplot(df,hue='TARGET CLASS',palette='coolwarm')
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/eda-1.png" width="1298" />

### Standardize Variables

``` python
scaler = StandardScaler()
scaler.fit(df.drop('TARGET CLASS', axis=1))
```

    ## StandardScaler()

``` python
scaled_features = scaler.transform(df.drop('TARGET CLASS', axis=1))
df_feat = pd.DataFrame(scaled_features,columns=df.columns[:-1])
```

``` python
df_feat.head()
```

<table>

<thead>

<tr>

<th style="text-align:right;">

XVPM

</th>

<th style="text-align:right;">

GWYH

</th>

<th style="text-align:right;">

TRAT

</th>

<th style="text-align:right;">

TLLZ

</th>

<th style="text-align:right;">

IGGA

</th>

<th style="text-align:right;">

HYKR

</th>

<th style="text-align:right;">

EDFS

</th>

<th style="text-align:right;">

GUUB

</th>

<th style="text-align:right;">

MGJM

</th>

<th style="text-align:right;">

JHZC

</th>

<th style="text-align:right;">

TARGET CLASS

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:right;">

1636.671

</td>

<td style="text-align:right;">

817.9885

</td>

<td style="text-align:right;">

2565.995

</td>

<td style="text-align:right;">

358.3472

</td>

<td style="text-align:right;">

550.4175

</td>

<td style="text-align:right;">

1618.871

</td>

<td style="text-align:right;">

2147.6413

</td>

<td style="text-align:right;">

330.7279

</td>

<td style="text-align:right;">

1494.879

</td>

<td style="text-align:right;">

845.1361

</td>

<td style="text-align:right;">

0

</td>

</tr>

<tr>

<td style="text-align:right;">

1013.403

</td>

<td style="text-align:right;">

577.5873

</td>

<td style="text-align:right;">

2644.141

</td>

<td style="text-align:right;">

280.4282

</td>

<td style="text-align:right;">

1161.8734

</td>

<td style="text-align:right;">

2084.108

</td>

<td style="text-align:right;">

853.4050

</td>

<td style="text-align:right;">

447.1576

</td>

<td style="text-align:right;">

1193.033

</td>

<td style="text-align:right;">

861.0818

</td>

<td style="text-align:right;">

1

</td>

</tr>

<tr>

<td style="text-align:right;">

1300.036

</td>

<td style="text-align:right;">

820.5187

</td>

<td style="text-align:right;">

2025.854

</td>

<td style="text-align:right;">

525.5623

</td>

<td style="text-align:right;">

922.2063

</td>

<td style="text-align:right;">

2552.355

</td>

<td style="text-align:right;">

818.6767

</td>

<td style="text-align:right;">

845.4915

</td>

<td style="text-align:right;">

1968.368

</td>

<td style="text-align:right;">

1647.1863

</td>

<td style="text-align:right;">

1

</td>

</tr>

</tbody>

</table>

### Train Test Split

``` python
X_train, X_test, y_train, y_test = train_test_split(scaled_features,df['TARGET CLASS'], test_size=0.30)
```

### Use KNN

``` python
knn = KNeighborsClassifier(n_neighbors=1)
knn.fit(X_train,y_train)
```

    ## KNeighborsClassifier(n_neighbors=1)

### Predictions and Evaluations

``` python
predictions = knn.predict(X_test)
```

**Classification Report **

``` python
print(classification_report(y_test,predictions))
```

<table>

<thead>

<tr>

<th style="text-align:left;">

</th>

<th style="text-align:right;">

precision

</th>

<th style="text-align:right;">

recall

</th>

<th style="text-align:right;">

f1-score

</th>

<th style="text-align:right;">

support

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

0

</td>

<td style="text-align:right;">

0.7872340

</td>

<td style="text-align:right;">

0.7655172

</td>

<td style="text-align:right;">

0.7762238

</td>

<td style="text-align:right;">

145.0000000

</td>

</tr>

<tr>

<td style="text-align:left;">

1

</td>

<td style="text-align:right;">

0.7861635

</td>

<td style="text-align:right;">

0.8064516

</td>

<td style="text-align:right;">

0.7961783

</td>

<td style="text-align:right;">

155.0000000

</td>

</tr>

<tr>

<td style="text-align:left;">

accuracy

</td>

<td style="text-align:right;">

0.7866667

</td>

<td style="text-align:right;">

0.7866667

</td>

<td style="text-align:right;">

0.7866667

</td>

<td style="text-align:right;">

0.7866667

</td>

</tr>

<tr>

<td style="text-align:left;">

macro avg

</td>

<td style="text-align:right;">

0.7866988

</td>

<td style="text-align:right;">

0.7859844

</td>

<td style="text-align:right;">

0.7862011

</td>

<td style="text-align:right;">

300.0000000

</td>

</tr>

<tr>

<td style="text-align:left;">

weighted avg

</td>

<td style="text-align:right;">

0.7866809

</td>

<td style="text-align:right;">

0.7866667

</td>

<td style="text-align:right;">

0.7865336

</td>

<td style="text-align:right;">

300.0000000

</td>

</tr>

</tbody>

</table>

**Confusion Matrix**

``` python
confusion_matrix(y_test, predictions)
```

<table>

<tbody>

<tr>

<td style="text-align:right;">

111

</td>

<td style="text-align:right;">

34

</td>

</tr>

<tr>

<td style="text-align:right;">

30

</td>

<td style="text-align:right;">

125

</td>

</tr>

</tbody>

</table>

### Choosing a K value

``` python
error_rate = []

# Will take some time
for i in range(1,40):
    
    knn = KNeighborsClassifier(n_neighbors=i)
    knn.fit(X_train,y_train)
    predictions_i = knn.predict(X_test)
    error_rate.append(np.mean(predictions_i != y_test))
```

    ## KNeighborsClassifier(n_neighbors=1)
    ## KNeighborsClassifier(n_neighbors=2)
    ## KNeighborsClassifier(n_neighbors=3)
    ## KNeighborsClassifier(n_neighbors=4)
    ## KNeighborsClassifier()
    ## KNeighborsClassifier(n_neighbors=6)
    ## KNeighborsClassifier(n_neighbors=7)
    ## KNeighborsClassifier(n_neighbors=8)
    ## KNeighborsClassifier(n_neighbors=9)
    ## KNeighborsClassifier(n_neighbors=10)
    ## KNeighborsClassifier(n_neighbors=11)
    ## KNeighborsClassifier(n_neighbors=12)
    ## KNeighborsClassifier(n_neighbors=13)
    ## KNeighborsClassifier(n_neighbors=14)
    ## KNeighborsClassifier(n_neighbors=15)
    ## KNeighborsClassifier(n_neighbors=16)
    ## KNeighborsClassifier(n_neighbors=17)
    ## KNeighborsClassifier(n_neighbors=18)
    ## KNeighborsClassifier(n_neighbors=19)
    ## KNeighborsClassifier(n_neighbors=20)
    ## KNeighborsClassifier(n_neighbors=21)
    ## KNeighborsClassifier(n_neighbors=22)
    ## KNeighborsClassifier(n_neighbors=23)
    ## KNeighborsClassifier(n_neighbors=24)
    ## KNeighborsClassifier(n_neighbors=25)
    ## KNeighborsClassifier(n_neighbors=26)
    ## KNeighborsClassifier(n_neighbors=27)
    ## KNeighborsClassifier(n_neighbors=28)
    ## KNeighborsClassifier(n_neighbors=29)
    ## KNeighborsClassifier(n_neighbors=30)
    ## KNeighborsClassifier(n_neighbors=31)
    ## KNeighborsClassifier(n_neighbors=32)
    ## KNeighborsClassifier(n_neighbors=33)
    ## KNeighborsClassifier(n_neighbors=34)
    ## KNeighborsClassifier(n_neighbors=35)
    ## KNeighborsClassifier(n_neighbors=36)
    ## KNeighborsClassifier(n_neighbors=37)
    ## KNeighborsClassifier(n_neighbors=38)
    ## KNeighborsClassifier(n_neighbors=39)

``` python
plt.figure(figsize=(10,6))
```

``` python
plt.plot(range(1,40),error_rate,color='blue', linestyle='dashed', marker='o',
         markerfacecolor='red', markersize=10)
```

    ## [<matplotlib.lines.Line2D object at 0x7ff4ea93f940>]

``` python
plt.title('Error Rate vs. K Value')
```

    ## Text(0.5, 1.0, 'Error Rate vs. K Value')

``` python
plt.xlabel('K')
```

    ## Text(0.5, 0, 'K')

``` python
plt.ylabel('Error Rate')
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/packages-1.png" width="960" />

### Retrain with new K value

``` python
# NOW WITH K=30
knn = KNeighborsClassifier(n_neighbors=30)

knn.fit(X_train,y_train)
```

    ## KNeighborsClassifier(n_neighbors=30)

``` python
predictions = knn.predict(X_test)
```

**Classification Report**

``` python
print(classification_report(y_test,predictions))
```

<table>

<thead>

<tr>

<th style="text-align:left;">

</th>

<th style="text-align:right;">

precision

</th>

<th style="text-align:right;">

recall

</th>

<th style="text-align:right;">

f1-score

</th>

<th style="text-align:right;">

support

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

0

</td>

<td style="text-align:right;">

0.8439716

</td>

<td style="text-align:right;">

0.8206897

</td>

<td style="text-align:right;">

0.8321678

</td>

<td style="text-align:right;">

145.00

</td>

</tr>

<tr>

<td style="text-align:left;">

1

</td>

<td style="text-align:right;">

0.8364780

</td>

<td style="text-align:right;">

0.8580645

</td>

<td style="text-align:right;">

0.8471338

</td>

<td style="text-align:right;">

155.00

</td>

</tr>

<tr>

<td style="text-align:left;">

accuracy

</td>

<td style="text-align:right;">

0.8400000

</td>

<td style="text-align:right;">

0.8400000

</td>

<td style="text-align:right;">

0.8400000

</td>

<td style="text-align:right;">

0.84

</td>

</tr>

<tr>

<td style="text-align:left;">

macro avg

</td>

<td style="text-align:right;">

0.8402248

</td>

<td style="text-align:right;">

0.8393771

</td>

<td style="text-align:right;">

0.8396508

</td>

<td style="text-align:right;">

300.00

</td>

</tr>

<tr>

<td style="text-align:left;">

weighted avg

</td>

<td style="text-align:right;">

0.8400999

</td>

<td style="text-align:right;">

0.8400000

</td>

<td style="text-align:right;">

0.8399002

</td>

<td style="text-align:right;">

300.00

</td>

</tr>

</tbody>

</table>

**Confusion Matrix**

``` python
confusion_matrix(y_test, predictions)
```

<table>

<tbody>

<tr>

<td style="text-align:right;">

119

</td>

<td style="text-align:right;">

26

</td>

</tr>

<tr>

<td style="text-align:right;">

22

</td>

<td style="text-align:right;">

133

</td>

</tr>

</tbody>

</table>

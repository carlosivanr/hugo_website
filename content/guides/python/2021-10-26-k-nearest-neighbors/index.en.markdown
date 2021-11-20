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
draft: false
---

The following describes a Python-based workflow for using k-nearest neighbors (KNN) to solve a classification problem. KNN is method of classifying values into a predefined number of groups based on their proximity to neighboring points. The number of neighboring points used in classification is referred to as K. In this example, we will use a fabricated data set in which the variables have been anonymized and our task to classify the data. All data were sourced from the Python for Data Science and Machine Learning Bootcamp by Pieran Data hosted on Udemy.com.

### Import packages

``` python
# Data Analysis & Visualization Packages
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt

# KNN Packages
from sklearn.preprocessing import StandardScaler
from sklearn.model_selection import train_test_split
from sklearn.neighbors import KNeighborsClassifier
from sklearn.metrics import classification_report, confusion_matrix
```

### Load data

``` python
df = pd.read_csv("KNN_Project_Data")
```

### Inspect data
Notice the column names are anonymized into 4 random letters. We have 10 columns of numeric variables and a Target Class variable consisting of zeros or ones.
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
Before using the KNN algorithm, we can produce a pairplot of all of the variables. With this number of variable, the pairplot will fairly large and since the data are fabricated, there is not a lot of insight that can be gathered. However, this step is shown as a critical step in visually inspecting data.
``` python
sns.pairplot(df,hue='TARGET CLASS',palette='coolwarm')
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/eda-1.png" width="1298" />

### Standardize Variables
Our first step in preparing the data for KNN is to standardize the variables. In sci-kit learn, the `StandardScaler()` function can easily accomplish this task. We first create an instance of `StandardScaler()`. We don't want to standardize the Target Class variable so the `.drop()` method comes in handy here to standardize the features only. The axis=1 call is to specify that we want to drop the data down the column. In python the axis=0 represents rows and axis=1 represents columns. Next we will use the `.transform()` method to standardize and scale the features. Once again, the Target Class variable is excluded from this step. Finally, we convert the scaled features into a data frame using all of the columns except for the last Target Class variable with slice notation.

``` python
scaler = StandardScaler()
scaler.fit(df.drop('TARGET CLASS', axis=1))
```

    ## StandardScaler()

``` python
scaled_features = scaler.transform(df.drop('TARGET CLASS', axis=1))
df_feat = pd.DataFrame(scaled_features, columns = df.columns[:-1])
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

### Split data into testing and training sets
Our next task is to create separate training and testing data sets. Our X training and testing sets will be created from the scaled features, and our y training and testing sets will be created from the Target Class variables. Finally, we will split the data in such a way that 30% of the data is retained for testing and the rest is left over for training.
``` python
X_train, X_test, y_train, y_test = train_test_split(scaled_features, 
                                                    df['TARGET CLASS'], 
                                                    test_size=0.30)
```

### Classify using KNN
Now we move on to creating a KNN model. We begin by first instantiating a KNN object. Since we do not know the optimal number of K we can begin with one. Later on, we will use the elbow method to determine a better K value. Once we instantiate the KNN model, we can use the training data to fit the model.
``` python
knn = KNeighborsClassifier(n_neighbors=1)
knn.fit(X_train,y_train)
```

    ## KNeighborsClassifier(n_neighbors=1)

### Predictions and Evaluations
After fitting our training data, we can then use the X test data to make predictions on data that the model has not "seen". 
``` python
predictions = knn.predict(X_test)
```

**Classification Report** - Next, we can compare our predicted and actual y values with the `classification_report()` function. Notice that our model is 78% accurate.
``` python
print(classification_report(y_test, predictions))
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

**Confusion Matrix** - The confusion matrix is one way of representing the performance of a classifier. In the first column, the matrix displays the number of correct classifications of the "0" label and the number of incorrect classifications of the "0" label. The second column displays the incorrect number of classifications of the "1" label and the number of correct classifications of the "0" label. 

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
One way to choose a K value is to use the elbow method. In this method, we will write a for-loop to run a KNN model for multiple values of K. In this case, we will run 40 iterations of KNN models with K values from 1 through 40, and then plot the error rate for each iteration. The optimal value of k is to select the lowest value of k associated with the lowest error rate. One way is to visualize a horizontal asymptote. The plot will show a point where the error rate does not reduce, so we want to select a value of k corresponding to this. If we look at the plot error rates stop fluctuating after about k = 30.  
``` python
error_rate = []

for i in range(1,40):
    
    knn = KNeighborsClassifier(n_neighbors=i)
    knn.fit(X_train,y_train)
    predictions_i = knn.predict(X_test)
    error_rate.append(np.mean(predictions_i != y_test)) # Average error rate
```

``` python
plt.figure(figsize=(10,6))
plt.plot(range(1,40),error_rate,color='blue', linestyle='dashed', marker='o',
         markerfacecolor='red', markersize=10)
plt.title('Error Rate vs. K Value')
plt.xlabel('K')
plt.ylabel('Error Rate')
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/packages-1.png" width="960" />

### Retrain with new K value
Now that we know that K=30 will reduce the error rate, we can re-run the knn algorithm and compare classification performance.
``` python
# n_neighbors set to 30
knn = KNeighborsClassifier(n_neighbors=30)

knn.fit(X_train,y_train)
```

    ## KNeighborsClassifier(n_neighbors=30)

``` python
predictions = knn.predict(X_test)
```

**Classification Report**
Our knn model with K=30 has improved accuracy. We went from 78% to 84% suggesting a modest improvement in accuracy.
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

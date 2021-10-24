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

The following describes a Python-based workflow for creating a logistic regression model to examine  behavioral data from online users. The task at hand is to build a model that can predict whether or not a an online user will click on an ad based on their Age, Area Income, whether or not they self-identify as Male, and a couple of behavioral features related to internet use. Since clicking on an ad is a binary event, either it happens or it doesn't, logistic regression is one way to build a model to answer our question. All data are fabricated and were sourced from the Python for Data Science and Machine Learning Bootcamp by Pieran Data hosted on Udemy.com.




### Import packages

```python
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import classification_report, confusion_matrix
```

### Load data

```python
ad_data = pd.read_csv("advertising.csv")
```

### Insepect the data
Before performing any visualizations or analyses, let's take a look at the structure of the data and make note of all of different variables. We will use the `.head()`, `.describe()`, and `.info()` methods on the customers dataframe. Notice that our variable of interest, "Clicked on Ad" is coded as a binary event with either a 0 (no click), or a 1 (clicked). In addition to this information, we see variables related to age, internet usage , location, area income, time, and the type of ad. Using the `.info()` method we see a list of all of the variables, counts of those variables, and their corresponding data types (or class for R users). Finally, with the `.describe()` method, we can examine basic measures of central tendency such as the mean and dispersion such as the minimum, maximum, and standard deviation for all of the numerical variables. 







**Head**

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

**Info**

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

**Describe**

```python
ad_data.describe()
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> Daily Time Spent on Site </th>
   <th style="text-align:right;"> Age </th>
   <th style="text-align:right;"> Area Income </th>
   <th style="text-align:right;"> Daily Internet Usage </th>
   <th style="text-align:right;"> Male </th>
   <th style="text-align:right;"> Clicked on Ad </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> count </td>
   <td style="text-align:right;"> 1000.00000 </td>
   <td style="text-align:right;"> 1000.000000 </td>
   <td style="text-align:right;"> 1000.00 </td>
   <td style="text-align:right;"> 1000.00000 </td>
   <td style="text-align:right;"> 1000.0000000 </td>
   <td style="text-align:right;"> 1000.0000000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mean </td>
   <td style="text-align:right;"> 65.00020 </td>
   <td style="text-align:right;"> 36.009000 </td>
   <td style="text-align:right;"> 55000.00 </td>
   <td style="text-align:right;"> 180.00010 </td>
   <td style="text-align:right;"> 0.4810000 </td>
   <td style="text-align:right;"> 0.5000000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> std </td>
   <td style="text-align:right;"> 15.85361 </td>
   <td style="text-align:right;"> 8.785562 </td>
   <td style="text-align:right;"> 13414.63 </td>
   <td style="text-align:right;"> 43.90234 </td>
   <td style="text-align:right;"> 0.4998889 </td>
   <td style="text-align:right;"> 0.5002502 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> min </td>
   <td style="text-align:right;"> 32.60000 </td>
   <td style="text-align:right;"> 19.000000 </td>
   <td style="text-align:right;"> 13996.50 </td>
   <td style="text-align:right;"> 104.78000 </td>
   <td style="text-align:right;"> 0.0000000 </td>
   <td style="text-align:right;"> 0.0000000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 25% </td>
   <td style="text-align:right;"> 51.36000 </td>
   <td style="text-align:right;"> 29.000000 </td>
   <td style="text-align:right;"> 47031.80 </td>
   <td style="text-align:right;"> 138.83000 </td>
   <td style="text-align:right;"> 0.0000000 </td>
   <td style="text-align:right;"> 0.0000000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 50% </td>
   <td style="text-align:right;"> 68.21500 </td>
   <td style="text-align:right;"> 35.000000 </td>
   <td style="text-align:right;"> 57012.30 </td>
   <td style="text-align:right;"> 183.13000 </td>
   <td style="text-align:right;"> 0.0000000 </td>
   <td style="text-align:right;"> 0.5000000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 75% </td>
   <td style="text-align:right;"> 78.54750 </td>
   <td style="text-align:right;"> 42.000000 </td>
   <td style="text-align:right;"> 65470.64 </td>
   <td style="text-align:right;"> 218.79250 </td>
   <td style="text-align:right;"> 1.0000000 </td>
   <td style="text-align:right;"> 1.0000000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> max </td>
   <td style="text-align:right;"> 91.43000 </td>
   <td style="text-align:right;"> 61.000000 </td>
   <td style="text-align:right;"> 79484.80 </td>
   <td style="text-align:right;"> 269.96000 </td>
   <td style="text-align:right;"> 1.0000000 </td>
   <td style="text-align:right;"> 1.0000000 </td>
  </tr>
</tbody>
</table>

### Exploratory Data Analysis
**Histogram of Age** -
We can begin exploratory data analysis by creating a histogram of Age to better understand the distribution of this variable. The data has a bell curve shape, but does show a slight skew which may expected if the hypothetical website targets users over the age of 20. In addition, we may expect less older users utilizing the website as compared to younger users.

```python
sns.histplot(ad_data, x = "Age", bins = 30)
```

```
## <matplotlib.axes._subplots.AxesSubplot object at 0x7f98416f9898>
```

```python
plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/packages-1.png" alt="Histogram of Age." width="672" />
<p class="caption">Figure 1: Histogram of Age.</p>
</div>


**Jointplot of Age and Area Income** -
Next, a jointplot combines a scatterplot with histograms of the designated variables. In this plot area income displays a distribution that is similar to that of Age, except it has a slight negative skew. The data indicates that higher area incomes are concentrated among the lower age ranges, possibly due to individuals retiring in older age, or reflecting the lack of data from older individuals.

```python
sns.jointplot(data=ad_data, x = "Age", y = "Area Income", height = 5)
```

```
## <seaborn.axisgrid.JointGrid object at 0x7f98417770b8>
```

```python
plt.tight_layout()
plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/packages-3.png" alt="Jointplot of Age and Area Income." width="480" />
<p class="caption">Figure 2: Jointplot of Age and Area Income.</p>
</div>


**Kernel Density Estimation Plot of Age and Daily Time Spent on Site** -
The jointplots from the seaborn package can also be set to different kinds. One kind is the kernel density estimation which displays a distribution of the data in a form of line. In this plot, we can see that Daily Time Spent on Site has a slight bi-modal distribution. One of the peaks is concentrated in the range of 60-90 minutes among 20-40 year olds, and another peak around 40 minutes among 35-45 year-olds.

```python
sns.jointplot(data=ad_data, x = "Age", y = "Daily Time Spent on Site", kind = "kde", height = 5)
```

```
## <seaborn.axisgrid.JointGrid object at 0x7f98422606d8>
```

```python
plt.tight_layout()
plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/packages-5.png" alt="KDE plot of Age and Daily Time Spent on Site." width="480" />
<p class="caption">Figure 3: KDE plot of Age and Daily Time Spent on Site.</p>
</div>


**Jointplot of Daily Time Spent on Site and Daily Internet Usage** -
A third jointplot of Daily Time Spent on Sity and Daily Internet Usage demonstrates two clusters. One cluster spends a higher amount of daily time using the internet which is associated with a higher amount of time spent on the site. On the other hand, another cluster spends less daily time on the internet which is associated with less time spent on the site. 

```python
sns.jointplot(data = ad_data, x = "Daily Time Spent on Site", y = "Daily Internet Usage", height = 5)
```

```
## <seaborn.axisgrid.JointGrid object at 0x7f98427dce10>
```

```python
plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/packages-7.png" alt="Jointplot plot of Daily Time Spent on Site and Daily Internet Usage." width="480" />
<p class="caption">Figure 4: Jointplot plot of Daily Time Spent on Site and Daily Internet Usage.</p>
</div>


**Pairplot of ad_data** -
We can make similar plots for all of the numerical data using the `pairplot()` function. Additionally, we can specify to color the data points based on whether or not the user clicked on the ad.

```python
sns.pairplot(data = ad_data, hue = "Clicked on Ad")
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/plot5-9.png" alt="Pairplot of ad_data." width="674" />
<p class="caption">Figure 5: Pairplot of ad_data.</p>
</div>


### Modeling
**Split data into training and testing sets** -
To begin modeling the data we will first create two dataframes. The first dataframe, X, will consist of all of our predictor variables. Since the dataset contains non-numeric variables such as City, Ad Topic Line, Country, and Timestamp, these variables will be excluded. Next we proceed to create the y data frame which will consist of our binary response or outcome variable, Clicked on Ad. Finally, we will use the `train_test_split()` to split the dataset into separate training and testing sets with the test set size set to 30% of the ad_data dataframe.

```python
#ad_data.columns # used to print out columns to select the predictor variables
X = ad_data[["Daily Time Spent on Site", "Age", "Area Income", "Daily Internet Usage", "Male"]]
y = ad_data["Clicked on Ad"]
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=101)
```

**Model fitting** -
After preparing and splitting the data set, we can proceed to fitting a logistic regression model. This is a two-step process that begins with creating an instantiation of a logistic regregression model, followed by passing the X_train and y_train datasets to the `.fit()` method.

```python
logmodel = LogisticRegression() #creates an instantiation of logistic regression model
logmodel.fit(X_train,y_train)
```

```
## LogisticRegression()
```

### Predictions and evaluations
After building our model, we can use the X_test dataset to make predictions. In other words, we will use the test predictor values to predict outcome values and save the predictions to a data frame. Then, we can compare our predictions with the actual y values in the test dataset to evaluate how well the model performs in predicting whether or not someone will click on the ad. One way to evaluate performance is to display the classification report. We notice that this model is 93% accurate at predicting if someone will click on the ad based on the included variables.

**Classification Report**

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

**Confusion Matrix**

```python
confusion_matrix(y_test, predictions)
```

<table>
<tbody>
  <tr>
   <td style="text-align:right;"> 149 </td>
   <td style="text-align:right;"> 8 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 14 </td>
   <td style="text-align:right;"> 129 </td>
  </tr>
</tbody>
</table>

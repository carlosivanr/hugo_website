---
title: Linear Regression in Python
author: Carlos Rodriguez
date: '2021-09-17'
slug: regression-python
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-09-17T22:43:40-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 65
draft: False
---


The following describes a Python-based workflow for creating a linear regression model to examine e-commerce data from an online clothing store. The store is attempting to learn where to focus their efforts to increase their yearly sales. All data are fabricated and were sourced from the Python for Data Science and Machine Learning Bootcamp by Pieran Data hosted on Udemy.com.[^1]



### Import Packages

```python
# Data Analysis & Visualization Packages
import numpy as np
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

# Linear Regression Packages
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression
from sklearn import metrics
```

### Load Data

```python
customers = pd.read_csv('Ecommerce Customers')
```

### Inspect Data
Before analysing and visualizing the data, we will take a look at the structure of the data and make note of all of different variables.


*Head*

```python
customers.head(3)
```
<table>
 <thead>
  <tr>
   <th style="text-align:left;"> Email </th>
   <th style="text-align:left;"> Address </th>
   <th style="text-align:left;"> Avatar </th>
   <th style="text-align:right;"> Avg. Session Length </th>
   <th style="text-align:right;"> Time on App </th>
   <th style="text-align:right;"> Time on Website </th>
   <th style="text-align:right;"> Length of Membership </th>
   <th style="text-align:right;"> Yearly Amount Spent </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> mstephenson@fernandez.com </td>
   <td style="text-align:left;"> 835 Frank Tunnel
Wrightmouth, MI 82180-9605 </td>
   <td style="text-align:left;"> Violet </td>
   <td style="text-align:right;"> 34.49727 </td>
   <td style="text-align:right;"> 12.65565 </td>
   <td style="text-align:right;"> 39.57767 </td>
   <td style="text-align:right;"> 4.082621 </td>
   <td style="text-align:right;"> 587.9511 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> hduke@hotmail.com </td>
   <td style="text-align:left;"> 4547 Archer Common
Diazchester, CA 06566-8576 </td>
   <td style="text-align:left;"> DarkGreen </td>
   <td style="text-align:right;"> 31.92627 </td>
   <td style="text-align:right;"> 11.10946 </td>
   <td style="text-align:right;"> 37.26896 </td>
   <td style="text-align:right;"> 2.664034 </td>
   <td style="text-align:right;"> 392.2049 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> pallen@yahoo.com </td>
   <td style="text-align:left;"> 24645 Valerie Unions Suite 582
Cobbborough, DC 99414-7564 </td>
   <td style="text-align:left;"> Bisque </td>
   <td style="text-align:right;"> 33.00091 </td>
   <td style="text-align:right;"> 11.33028 </td>
   <td style="text-align:right;"> 37.11060 </td>
   <td style="text-align:right;"> 4.104543 </td>
   <td style="text-align:right;"> 487.5475 </td>
  </tr>
</tbody>
</table>

*Info*

```python
customers.info()
```

```
## <class 'pandas.core.frame.DataFrame'>
## RangeIndex: 500 entries, 0 to 499
## Data columns (total 8 columns):
##  #   Column                Non-Null Count  Dtype  
## ---  ------                --------------  -----  
##  0   Email                 500 non-null    object 
##  1   Address               500 non-null    object 
##  2   Avatar                500 non-null    object 
##  3   Avg. Session Length   500 non-null    float64
##  4   Time on App           500 non-null    float64
##  5   Time on Website       500 non-null    float64
##  6   Length of Membership  500 non-null    float64
##  7   Yearly Amount Spent   500 non-null    float64
## dtypes: float64(5), object(3)
## memory usage: 31.4+ KB
```

*Describe*

```python
customers.describe()
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> Avg. Session Length </th>
   <th style="text-align:right;"> Time on App </th>
   <th style="text-align:right;"> Time on Website </th>
   <th style="text-align:right;"> Length of Membership </th>
   <th style="text-align:right;"> Yearly Amount Spent </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> count </td>
   <td style="text-align:right;"> 500.0000000 </td>
   <td style="text-align:right;"> 500.0000000 </td>
   <td style="text-align:right;"> 500.000000 </td>
   <td style="text-align:right;"> 500.0000000 </td>
   <td style="text-align:right;"> 500.00000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mean </td>
   <td style="text-align:right;"> 33.0531935 </td>
   <td style="text-align:right;"> 12.0524879 </td>
   <td style="text-align:right;"> 37.060445 </td>
   <td style="text-align:right;"> 3.5334616 </td>
   <td style="text-align:right;"> 499.31404 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> std </td>
   <td style="text-align:right;"> 0.9925631 </td>
   <td style="text-align:right;"> 0.9942156 </td>
   <td style="text-align:right;"> 1.010489 </td>
   <td style="text-align:right;"> 0.9992775 </td>
   <td style="text-align:right;"> 79.31478 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> min </td>
   <td style="text-align:right;"> 29.5324290 </td>
   <td style="text-align:right;"> 8.5081522 </td>
   <td style="text-align:right;"> 33.913847 </td>
   <td style="text-align:right;"> 0.2699011 </td>
   <td style="text-align:right;"> 256.67058 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 25% </td>
   <td style="text-align:right;"> 32.3418220 </td>
   <td style="text-align:right;"> 11.3881534 </td>
   <td style="text-align:right;"> 36.349257 </td>
   <td style="text-align:right;"> 2.9304497 </td>
   <td style="text-align:right;"> 445.03828 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 50% </td>
   <td style="text-align:right;"> 33.0820076 </td>
   <td style="text-align:right;"> 11.9832313 </td>
   <td style="text-align:right;"> 37.069367 </td>
   <td style="text-align:right;"> 3.5339750 </td>
   <td style="text-align:right;"> 498.88788 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 75% </td>
   <td style="text-align:right;"> 33.7119850 </td>
   <td style="text-align:right;"> 12.7538497 </td>
   <td style="text-align:right;"> 37.716432 </td>
   <td style="text-align:right;"> 4.1265019 </td>
   <td style="text-align:right;"> 549.31383 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> max </td>
   <td style="text-align:right;"> 36.1396625 </td>
   <td style="text-align:right;"> 15.1269943 </td>
   <td style="text-align:right;"> 40.005182 </td>
   <td style="text-align:right;"> 6.9226893 </td>
   <td style="text-align:right;"> 765.51846 </td>
  </tr>
</tbody>
</table>


### Exploratory Data Analysis with Seaborn
After taking a look the tabulated data, we can make some basic visualizations to show the relationships between all of the numerical variables such as Time on Website, Time on App, Length of Membership, and Yearly Amount Spent. One place to start is to display paired plots with the Seaborn package.

```python
sns.pairplot(data = customers)
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/plot4-1.png" alt="Pairplot of all continous variables in the customers data frame." width="616" />
<p class="caption">Figure 1: Pairplot of all continous variables in the customers data frame.</p>
</div>


After inspecting the paired plots, the relationship between Length of Membership and Yearly Amount Spent stands out. In the code chunk below, Seaborn was used to build a linear model plot of these two variables. In addition to displaying a scatterplot of the data, the lmplot displays a regression line that characterizes the relationship between the variables.

```python
sns.lmplot(x = "Length of Membership", y = "Yearly Amount Spent", data = customers)
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/plot5-3.png" alt="Linear model plot of the Length of Membership and Yearly Amount Spent." width="245" />
<p class="caption">Figure 2: Linear model plot of the Length of Membership and Yearly Amount Spent.</p>
</div>


### Prepare Data for Linear Regression Modelling
After performing some exploratory data analysis, we can then move on to building a linear model. First,  we will need to prepare the data by collecting all of the response variables and predictor variables into separate data frames. Next, the data are split into separate training and testing data sets with the `train_test_split()` function from the scikit-learn package.

```python
# Preparing numeric data for training and testing a regression model ie cross validation
# customers.columns # Prints the columns that are then copied and pasted int X an y.
X = customers[['Avg. Session Length', 'Time on App', 'Time on Website', 'Length of Membership']]
y = customers["Yearly Amount Spent"]

# Splitting the data into test and train datasets, holding out 30% for testing
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=101)
```

### Train Linear Regression Model
After creating our training set data, a linear model can be fit. In this case we will build a model that will predict the Yearly Amount Spent from the remaining  continuous variables in the e-commerce data set. Finally, we will create a data frame to print out the model coefficients.

```python
# Initialize an empty LinearyRegression() object
lm = LinearRegression()
```


```python
# Fit the x and y variables. In other words, predict y (yearly amount spent), 
# from all of the numerical X variables
lm.fit(X_train,y_train)
```

```
## LinearRegression()
```

```python
# print the coefficients to each "feature" in the dataset, feature would be 
# interchangeable with variable
coeff_df = pd.DataFrame(lm.coef_,X.columns,columns=['Coefficient'])
```
<table>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> Coefficient </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Avg. Session Length </td>
   <td style="text-align:right;"> 25.9815497 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Time on App </td>
   <td style="text-align:right;"> 38.5901588 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Time on Website </td>
   <td style="text-align:right;"> 0.1904053 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Length of Membership </td>
   <td style="text-align:right;"> 61.2790965 </td>
  </tr>
</tbody>
</table>

### Test Linear Regression Model
After building a model with the training data, our next step is to use the predictor variables in the testing data and our model, to make predictions about the Yearly Amount Spent. Then we can compare how well our predicted values match up with actual values in the testing data.


```python
# Predict y from the X variables in the test set with the linear model created in the train set
predictions = lm.predict(X_test)
sns.scatterplot(x = y_test, y = predictions, data = pd.DataFrame(y_test, predictions))
```

```
## <matplotlib.axes._subplots.AxesSubplot object at 0x7fcbbccb5198>
```

```python
plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-1.png" alt="Scatterplot of the predicted and actual y values from the testing data set." width="672" />
<p class="caption">Figure 3: Scatterplot of the predicted and actual y values from the testing data set.</p>
</div>




### Evaluate Linear Regression Model
There are a number of ways that one can evaluate regression models. These metrics include the mean absolute error, the mean squared error, the root mean squared error, and R squared (explained variance score). The R squared valued can obtained by the `metrics.explained_variance_score()` function of the scikit-learn package. Without getting into the weeds, the R squared value is a measure of the proportion of variance in one variable that is explained by another. In this context, roughly 98% of the variance in Yearly Amount Spent can be explained by Length of Membership. As a reminder, these are fabricated data for illustrative purposes only and it would be rare to see such a relationship out in the wild.

```python
metrics.explained_variance_score(y_test, predictions)
```

```
## 0.9890771231889607
```



### Visually Inspect Residuals
Our last step is to plot the residuals which are the differences between the predicted Yearly Amount Spent values using the generted model and the actual values in the test data set. We would generally expect these to be normally distributed, otherwise linear regression may be a poor analysis choice for these data.

```python
sns.histplot((y_test-predictions),bins=50, kde = True)
```

```
## <matplotlib.axes._subplots.AxesSubplot object at 0x7fcbbd298358>
```

```python
plt.show()
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-3.png" width="672" />


### Interpretation
According to the regression coefficients, for each unit increase in the predictor variables Avg. Session Length, Time on App, Time on Website, and Length of Membership, we would expect a respectivley 25.98, 38.59, 0.19, and 61.28 unit increase in the Yearly Amount Spent response variable. Thus, the clothing company may be best served by focusing their efforts on the Length of Membership and perhaps the Time on App measures. Perhaps there means within the company to maintain or improve membership services and application development. However, it should be noted that although these variables are related, it is beyond the scope of this analysis to determine causal relationships. We don't exactly know if Yearly Amount Spent or Length of Membership drives the other or if there is an additional intermediary variable that is presently unknown. However, for now we have atleast determined a strong relationship between these two variables.

### Footnotes
[^1]: This website is built with the R blogdown package. To integrate the python code, I used the reticulate package to perform the python commands. However, in order to display some of the plots and tables, python dataframes were saved as R dataframes. For this reason, some R commands are used to display output.
<!-- Older code and text in which joint plots wouldn't display properly-->

<!-- ```{python, plot1, fig.cap = 'Joint plot between Time on Website and Yearly Amount Spent.',} -->
<!-- sns.jointplot(x = 'Time on Website', y = 'Yearly Amount Spent', data = customers) -->
<!-- plt.close() -->


<!-- ``` -->

<!-- ```{r, include = FALSE,eval=FALSE} -->
<!-- sns <- import('seaborn') -->
<!-- plt <- import('matplotlib.pyplot') -->
<!-- pd <- import('pandas') -->
<!-- ``` -->

<!-- ```{r, include = FALSE, eval=FALSE} -->
<!-- sns$pairplot(r_to_py(df[,4:8])) -->
<!-- plt$show() -->
<!-- ``` -->


<!-- Next, we will take a look at a joint plot of Time on App and Yearly Amount Spen. There seems to be a stronger relationship here when compared to Time on Website and Yearly Amount Spent, but nothing that stands out. -->
<!-- ```{python, plot2, fig.cap = 'Joint plot between Time on App and Yearly Amount Spent.'} -->
<!-- sns.jointplot(x = "Time on App", y = "Yearly Amount Spent", data = customers) -->
<!-- plt.close() -->


<!-- ``` -->

<!-- In this plot, we examine Time on App and Length of Membership using the hex type of plot. Of all of the plots generated so far, these two variables seem to demonstrate the strongest relationship. -->
<!-- ```{python, plot3, fig.cap = 'Joint plot between Time on App and Length of Membership.'} -->
<!-- sns.jointplot(x = "Time on App", y = "Length of Membership", data = customers, kind = "hex") -->
<!-- plt.close() -->
<!-- ``` -->

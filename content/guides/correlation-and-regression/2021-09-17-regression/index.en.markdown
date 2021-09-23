---
title: Linear Regression Exercise-1 in Python
author: Carlos Rodriguez
date: '2021-09-17'
slug: regression
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
weight: 10
draft: False
---


The following describes a Python-based workflow for creating a linear regression model to examine e-commerce data from an online clothing store. The store is attempting to learn where to focus their efforts on to increase their yearly sales. All data are fabricated and were sourced from the Pierian Data Machine Learning course hosted on Udemy.com.



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
Before analysing and visualizing the data, we will take a look at the structure of the data and make note of all of different variables. As of this writing, pandas tables do not readily display well within R markdown. To get around this snag, I took advantage of R's reticulate and kableExtra packages to display the first few rows of the data frame.

```python
customers.head(3)
customers.describe()
customers.info()
```


```r
#R code to display pandas table
df <- reticulate::py$customers
kable(head(df, n=3))
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

### Exploratory Data Analysis with Seaborn
After taking a look the tabulated data, we can make some basic visualizations to show the relationships between all of the numerical variables such as Time on Website, Time on App, Length of Membership, and Yearly Amount Spent. We will begin by plotting Time on Website and Yearly Amount Spent with the Seaborn package. The jointplot shows a scatterplot of the two variables along with histograms around the edges to show their distributions.

```python
sns.jointplot(x = 'Time on Website', y = 'Yearly Amount Spent', data = customers)
```

```
## <seaborn.axisgrid.JointGrid object at 0x7fc056b7a7f0>
```

```python
plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-1.png" alt="Joint plot between Time on Website and Yearly Amount Spent." width="576" />
<p class="caption">Figure 1: Joint plot between Time on Website and Yearly Amount Spent.</p>
</div>


Next, we will take a look at a joint plot of Time on App and Yearly Amount Spen. There seems to be a stronger relationship here when compared to Time on Website and Yearly Amount Spent, but nothing that stands out.

```python
sns.jointplot(x = "Time on App", y = "Yearly Amount Spent", data = customers)
```

```
## <seaborn.axisgrid.JointGrid object at 0x7fc0573e05f8>
```

```python
plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-3.png" alt="Joint plot between Time on App and Yearly Amount Spent." width="576" />
<p class="caption">Figure 2: Joint plot between Time on App and Yearly Amount Spent.</p>
</div>

In this plot, we examine Time on App and Length of Membership using the hex type of plot. Of all of the plots generated so far, these two variables seem to demonstrate the strongest relationship.

```python
sns.jointplot(x = "Time on App", y = "Length of Membership", data = customers, kind = "hex")
```

```
## <seaborn.axisgrid.JointGrid object at 0x7fc056d79278>
```

```python
plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-5.png" alt="Joint plot between Time on App and Length of Membership." width="576" />
<p class="caption">Figure 3: Joint plot between Time on App and Length of Membership.</p>
</div>

Generating each of these plots can be time consuming. Luckily, the Seaborn package has a quick way to produce paired plots that can display the relationships between multiple quantitative variables.

```python
sns.pairplot(data = customers)
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/plot4-7.png" alt="Pairplot of all continous variables in the customers data frame." width="616" />
<p class="caption">Figure 4: Pairplot of all continous variables in the customers data frame.</p>
</div>

```python
plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-8.png" alt="Pairplot of all continous variables in the customers data frame." width="1200" />
<p class="caption">Figure 4: Pairplot of all continous variables in the customers data frame.</p>
</div>


After inspecting the paired plot, the relationship between Length of Membership and Yearly Amount Spent stands out. In the code chunk below, Seaborn was used to build a linear model plot of these two variables. In addition to displaying a scatterplot of the data, the lmplot displays a regression line that characterizes the relationship between the variables.

```python
sns.lmplot(x = "Length of Membership", y = "Yearly Amount Spent", data = customers)
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/plot5-11.png" alt="Linear model plot of the Length of Membership and Yearly Amount Spent." width="245" />
<p class="caption">Figure 5: Linear model plot of the Length of Membership and Yearly Amount Spent.</p>
</div>

```python
plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-12.png" alt="Linear model plot of the Length of Membership and Yearly Amount Spent." width="480" />
<p class="caption">Figure 5: Linear model plot of the Length of Membership and Yearly Amount Spent.</p>
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

```r
coeffs <- reticulate::py$coeff_df
kable(coeffs)
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

# plot the actual Y (from test set), with the predicted y.
#plt.scatter(y_test, predictions)
#plt.show()

sns.scatterplot(x = y_test, y = predictions, data = pd.DataFrame(y_test, predictions))
```

```
## <matplotlib.axes._subplots.AxesSubplot object at 0x7fc05a299898>
```

```python
plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-1.png" alt="Scatterplot of the predicted and actual y values from the testing data set." width="480" />
<p class="caption">Figure 6: Scatterplot of the predicted and actual y values from the testing data set.</p>
</div>


### Evaluate Linear Regression Model
There are a number of ways that one can evaluate regression models. These metrics include the mean absolute error, the mean squared error, the root mean squared error, and R squared (explained variance score). The R squared valued can obtained by the `metrics.explained_variance_score()` function of the scikit-learn package. Withou getting into the weeds, the R squared value is a measure of the proportion of variance in one variable that is explained by another. In this context, roughly 98% of the variance in Yearly Amount Spent can be explained by Length of Membership. As a reminder, these are fabricated data for illustrative purposes only and it would be rare to see such a relationship out in the wild.

```python
metrics.explained_variance_score(y_test, predictions)
```

```
## 0.9890771231889607
```



### Visually Inspect Residuals
Our last step is to plot the residuals which are the differences between the predicted Yearly Amount Spent values using the generted model and the actual values in the test data set.

```python
sns.histplot((y_test-predictions),bins=50, kde = True) # We want the difference between the testing Y and the predicted Y's
```

```
## <matplotlib.axes._subplots.AxesSubplot object at 0x7fc0592ea908>
```

```python
plt.show()
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-3.png" width="480" />

### Interpretation
According to the regression coefficients, for each unit increase in the predictor variables Avg. Session Length, Time on App, Time on Website, and Length of Membership, we would expect a respectivley 25.98, 38.59, 0.19, and 61.28 unit increase in the Yearly Amount Spent response variable. Thus, the clothing company may be best served by focusing their efforts on the Length of Membership and perhaps the Time on App measures. Perhaps there means within the company to maintain or improve membership services and application development. However, it should be noted that although these variables are related, it is beyond the scope of this analysis to determine causal relationships. We don't exactly know if Yearly Amount Spent or Length of Membership drives the other or if there is an additional intermediary variable that is presently unknown. However, for now we have atleast determined a strong relationship between these two variables.

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


The following describes a Python-based workflow for creating a linear regression model to examine ecommerce data from an online clothing store. The store is attempting to learn where to focus their efforts on to increase their yearly sales. All data are fabricated and were sourced from the Pierian Data Udemy course on Python and Machine Learning course.



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
Before analysing and visualizing the data, we will take a look at the structure of the data and make note of all of different variables. As of this writing, pandas tables do not readily display well within R markdown. To get around this, I have provided the R code to display some of the information and the un-evaluated python code as well.

```python
customers.head(3)
customers.describe()
customers.info()
```


```r
#R code to display pandas table
df <- reticulate::py$customers
head(df, n=3)
```

```
##                       Email
## 1 mstephenson@fernandez.com
## 2         hduke@hotmail.com
## 3          pallen@yahoo.com
##                                                      Address    Avatar
## 1               835 Frank Tunnel\nWrightmouth, MI 82180-9605    Violet
## 2             4547 Archer Common\nDiazchester, CA 06566-8576 DarkGreen
## 3 24645 Valerie Unions Suite 582\nCobbborough, DC 99414-7564    Bisque
##   Avg. Session Length Time on App Time on Website Length of Membership
## 1            34.49727    12.65565        39.57767             4.082621
## 2            31.92627    11.10946        37.26896             2.664034
## 3            33.00091    11.33028        37.11060             4.104543
##   Yearly Amount Spent
## 1            587.9511
## 2            392.2049
## 3            487.5475
```

### Exploratory Data Analysis with Seaborn
After taking a look the data, we can make some basic visualizations to show the relationships between some of the numerical variables such as Time on Website, Time on App, Length of Membership, and Yearly Amount Spent. We will begin by plotting Time on Website (ToW) and Yearly Amount Spent (YAS). After visualizing the data, no apparent trends are

```python
#%matplotlib inline
sns.jointplot(x = 'Time on Website', y = 'Yearly Amount Spent', data = customers)
```

```
## <seaborn.axisgrid.JointGrid object at 0x7fce5beba828>
```

```python
plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-1.png" alt="Joint plot between Time on Website and Yearly Amount Spent." width="576" />
<p class="caption">Figure 1: Joint plot between Time on Website and Yearly Amount Spent.</p>
</div>


Next, we will take a look at a joint plot of Time on App (ToA) and YAS. There seems to be a stronger relationship here when compared to ToW and YAS, but nothing that stands out.

```python
sns.jointplot(x = "Time on App", y = "Yearly Amount Spent", data = customers)
```

```
## <seaborn.axisgrid.JointGrid object at 0x7fce5dd932b0>
```

```python
plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-3.png" alt="Joint plot between Time on App and Yearly Amount Spent." width="576" />
<p class="caption">Figure 2: Joint plot between Time on App and Yearly Amount Spent.</p>
</div>

In this plot, we examine Time on App (ToA) and Length of Membership (LoM) using the hex type of plot. Of all of the plots generated so far, these two variables seem to demonstrate the strongest relationship.

```python
sns.jointplot(x = "Time on App", y = "Length of Membership", data = customers, kind = "hex")
```

```
## <seaborn.axisgrid.JointGrid object at 0x7fce599e3a20>
```

```python
plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-5.png" alt="Joint plot between Time on App and Length of Membership." width="576" />
<p class="caption">Figure 3: Joint plot between Time on App and Length of Membership.</p>
</div>


Generating each of these plots can be time consuming. Luckily, the Seaborn package has quick way to produce multiple exploratory data analysis plots with the pairplot function.

```python
sns.pairplot(data = customers)
#plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/plot4-7.png" alt="Pairplot of all continous variables in the customers data frame." width="616" />
<p class="caption">Figure 4: Pairplot of all continous variables in the customers data frame.</p>
</div>


After taking a look at the pairplot data, the relationship between LoM and YAS stands out. In the code chunk below, we can use Seaborn to make a linear model plot of these two variables. In addition to displaying a scatterplot of the data, the lmplot displays a line generated from a linear model of data.

```python
sns.lmplot(x = "Length of Membership", y = "Yearly Amount Spent", data = customers)
#plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/plot5-9.png" alt="Linear model plot of the Length of Membership and Yearly Amount Spent." width="245" />
<p class="caption">Figure 5: Linear model plot of the Length of Membership and Yearly Amount Spent.</p>
</div>

### Prepare Data for Linear Regression Modelling
After performing some exploratory data analysis, we can then move on to building a linear and begin by preparing the data by collecting the quantitative variables. I will create two new data frames, one consisting of the continuous variables that we can use to predict Yearly Amount Spent. Then the data will be split into separate training and testing data sets.

```python
# Preparing numeric data for training and testing a regression model ie cross validation
# customers.columns # Prints the columns that are then copied and pasted int X an y.
X = customers [['Avg. Session Length', 'Time on App', 'Time on Website', 'Length of Membership']]
y = customers["Yearly Amount Spent"]

# Splitting the data into test and train datasets, holding out 30% for testing
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=101)
```

### Train Linear Regression Model
After creating our training set data, a linear model can be fit. In this case we will build a model that will predict the Yearly Amount Spent from the remaining the continuous variables in the ecommerce data. Finally, we will create a df to print out the model coefficients.

```python
# Initialize an empty LinearyRegression() object
lm = LinearRegression()

# Fit the x and y variables. In other words, predict y (yearly amount spent), from all of the numerical X variables
lm.fit(X_train,y_train)

# print the coefficients to each "feature" in the dataset, feature would be interchangeable with variable
```

```
## LinearRegression()
```

```python
coeff_df = pd.DataFrame(lm.coef_,X.columns,columns=['Coefficient'])
coeff_df
```

```
##                       Coefficient
## Avg. Session Length     25.981550
## Time on App             38.590159
## Time on Website          0.190405
## Length of Membership    61.279097
```

### Test Linear Regression Model
After building a model with the training data, we can then use the testing data to make predictions about the Yearly Amount Spent. Then the predicted y variables can be plotted along with the actual y variables in the testing data set.


```python
# Predict y from the X variables in the test set with the linear model created in the train set
predictions = lm.predict(X_test) # Just the testing set features

# plot the actual Y (from test set), with the predicted y.
plt.scatter(y_test, predictions)
#plt.show()

#sns.scatterplot(x = y_test, y = predictions, data = pd.DataFrame(y_test, predictions))
#plt.show()
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-11.png" alt="Scatterplot of the predicted and actual y values from the testing data set." width="480" />
<p class="caption">Figure 6: Scatterplot of the predicted and actual y values from the testing data set.</p>
</div>


### Evaluate Linear Regression Model

```python
######### Print the model metrics   ################
print('MAE:', metrics.mean_absolute_error(y_test, predictions))
```

```
## MAE: 7.2281486534308295
```

```python
print('MSE:', metrics.mean_squared_error(y_test, predictions))
```

```
## MSE: 79.8130516509743
```

```python
print('RMSE:', np.sqrt(metrics.mean_squared_error(y_test, predictions)))


######### Calculate R Squared #########
```

```
## RMSE: 8.933815066978626
```

```python
metrics.explained_variance_score(y_test, predictions)
```

```
## 0.9890771231889607
```

### Visually Inspect Residuals

```python
sns.histplot((y_test-predictions),bins=50, kde = True) # We want the difference between the testing Y and the predicted Y's
```

```
## <matplotlib.axes._subplots.AxesSubplot object at 0x7fce60dbe4a8>
```

```python
plt.show()
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-1-13.png" width="480" />

### Interpretation
According to this data for each unit increase in the variable, there is a coefficient increase in yearly spending
the y variable. So for example, for each unit increase in the length of membership which could have been
measured in in weeks, months, or years, means that there was an increase of $61.23 in yearly spending.
So if I were to recommend something to the company, I would try to see what it would take to increase
the length of membership, because it seems like that would be a way to increase their yearly spending
Although, I would also want to look at whether that was correlated with salary, perhaps the longer they are
members the more time they have to amass salary increases which increases spending.

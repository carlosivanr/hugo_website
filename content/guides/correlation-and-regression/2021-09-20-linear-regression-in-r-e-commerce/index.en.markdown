---
title: Linear Regression Exercise-1 in R
author: Carlos Rodriguez
date: '2021-09-20'
slug: linear-regression-in-r-e-commerce
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-09-20T19:19:06-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 10
draft: True
---


### Load Packages


### Load and Inspect Data

```r
customers <- read_csv("Ecommerce Customers")
```

```
## Rows: 500 Columns: 8
```

```
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (3): Email, Address, Avatar
## dbl (5): Avg. Session Length, Time on App, Time on Website, Length of Member...
```

```
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
head(df)
```

```
##                                               
## 1 function (x, df1, df2, ncp, log = FALSE)    
## 2 {                                           
## 3     if (missing(ncp))                       
## 4         .Call(C_df, x, df1, df2, log)       
## 5     else .Call(C_dnf, x, df1, df2, ncp, log)
## 6 }
```



### Exploratory Data Analysis
The GGally package comes in handy for performing exploratory data analysis. Here we will pass through the numeric columns of customers to the `ggpairs()` function. The output isn't pretty, but, it does communicate quite a bit of information.

```r
ggpairs(customers[,c(4,5,6,7,8)])
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-2-1.png" width="672" />

After inspecting the output, it is evident that there is a strong relationship between Length of Membership and Yearly Amount Spent. We can then use ggplot2 to display this relationship in a new plot.

```r
# Use backticks when column names include spaces
ggplot(data = customers, aes(x = `Length of Membership`, y = `Yearly Amount Spent`)) +
  geom_point(alpha = 0.8, color = azl)
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-3-1.png" width="672" />



### Prepare Data for Linear Regression
In this step, we will split the data into training and testing sets. We will build a regression model with the training data, use the model to predict new values using the test data, and then compare the results of the predicted values with the actual values in the test data.

```r
# Set the seed for reproducibility
set.seed(101)

# Get the sample size for the train set
sample_size <- floor(0.7 * nrow(customers))

# Get an index for the training set rows
train_index <- sample(seq_len(nrow(customers)), size = sample_size)

# Set the train set
train = customers[train_index, ]

# Set the hold out test set
test = customers[-train_index, ]
```

### Linear Regression Modeling
After splitting our data, we can proceed to creating a linear regression model. We will predict Yearly Amount Spent (response variable), from the rest of the numerical predictor variables. Then we will take a look at the residuals which allow us to gauge the amount of error (how far off the data points are from the line generated in the model).

```r
model <- lm(`Yearly Amount Spent` ~ ., data = train[,c(4,5,6,7,8)]) # the . means everything else, -subtracts the columns from the call
summary(model)
```

```
## 
## Call:
## lm(formula = `Yearly Amount Spent` ~ ., data = train[, c(4, 5, 
##     6, 7, 8)])
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -30.1775  -6.5662   0.0617   6.8883  30.7863 
## 
## Coefficients:
##                          Estimate Std. Error t value Pr(>|t|)    
## (Intercept)            -1035.7741    28.3066 -36.591   <2e-16 ***
## `Avg. Session Length`     25.5342     0.5517  46.286   <2e-16 ***
## `Time on App`             38.5615     0.5525  69.797   <2e-16 ***
## `Time on Website`          0.2912     0.5554   0.524      0.6    
## `Length of Membership`    60.9903     0.5694 107.112   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 10.21 on 345 degrees of freedom
## Multiple R-squared:  0.9821,	Adjusted R-squared:  0.9819 
## F-statistic:  4739 on 4 and 345 DF,  p-value: < 2.2e-16
```

```r
res <- residuals(model)
res <- as.data.frame(res)
head(res)
```

```
##          res
## 1   2.024035
## 2 -13.167887
## 3  11.544038
## 4   5.111917
## 5   2.167771
## 6  24.046909
```

```r
ggplot(data = res, aes(res)) +
         geom_histogram(fill = azl, alpha = 0.8)
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-6-1.png" width="672" />

```r
#plot(model)
```

### Predictions
In this step, we will use the model created above, to make predictions using data that was not used in generating the model. Then we can plot the predicted values along with the actual values. We will see that our model performed quite well.

```r
predictions <- predict(model, test[,c(4,5,6,7,8)])
results <- cbind(predictions, test$`Yearly Amount Spent`)
colnames(results) <-  c('predicted', 'actual')
results <- as.data.frame(results)

# Plot the 
ggplot(results, aes(x = predicted, y = actual)) +
  geom_point(color = azl)
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-7-1.png" width="672" />


### Model Evaluation ???













---
title: Linear Regression Exercise-2 in R 
author: Carlos Rodriguez
date: '2021-09-20'
slug: linear-regression-in-r
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-09-20T15:42:12-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 15
draft: True
---

Bike sharing link: https://www.kaggle.com/c/bike-sharing-demand/data

Predict the total count of bikes rented during each hour covered by the test set, using only information available prior to the rental period.

### Load Packages


### Load Data

```r
df <- read_csv("bikeshare.csv")
head(df)
```

```
## # A tibble: 6 × 12
##   datetime            season holiday workingday weather  temp atemp humidity
##   <dttm>               <dbl>   <dbl>      <dbl>   <dbl> <dbl> <dbl>    <dbl>
## 1 2011-01-01 00:00:00      1       0          0       1  9.84  14.4       81
## 2 2011-01-01 01:00:00      1       0          0       1  9.02  13.6       80
## 3 2011-01-01 02:00:00      1       0          0       1  9.02  13.6       80
## 4 2011-01-01 03:00:00      1       0          0       1  9.84  14.4       75
## 5 2011-01-01 04:00:00      1       0          0       1  9.84  14.4       75
## 6 2011-01-01 05:00:00      1       0          0       2  9.84  12.9       75
## # … with 4 more variables: windspeed <dbl>, casual <dbl>, registered <dbl>,
## #   count <dbl>
```

### Exploratory Data Analysis

```r
ggplot(data = df, aes(x = temp, y = count)) +
  geom_point(alpha = 0.2, aes(color=temp))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-2-1.png" width="672" />


```r
# Ensure datetime is in POSIXct
# df$datetime <- as.POSIXct(df$datetime)

ggplot(data = df, aes(x = datetime, y = count)) +
  geom_point(alpha = 0.2, aes(color=temp)) +
  scale_color_continuous(low = "#2166AC", high = "#B2182B")
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-3-1.png" width="672" />
### Correlation between temparature and count

```r
cor(df$temp, df$count, method = "pearson")
```

```
## [1] 0.3944536
```

```r
# Alternatively
# cor(df[,c("temp","count")]) #produces a correlation matrix instead of a single value
```

### Boxplots of count by season

```r
ggplot(data = df, aes(x = as.factor(season), y = count)) +
  geom_boxplot(aes(color = as.factor(season))) +
  labs(x = "season") +
  guides(color=guide_legend("season")) +
  scale_color_discrete(labels = c("Spring", "Summer", "Fall", "Winter"))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-5-1.png" width="672" />




### Feature Engineering
Create a new column of hour from the datetime column.

```r
df$hour <- sapply(df$datetime, function(x){format(x, "%H")})
```



### Scatterplot with new data (working days)

```r
pal <- c("#2166AC", "#67A9CF",  "#D1E5F0", "#FDDBC7", "#EF8A62", "#B2182B")
df %>% filter(workingday == 1) %>%
  ggplot(., aes(x = hour, y = count, color = temp)) +
  geom_point(position = position_jitter(w=1, h=0)) +
  scale_color_gradientn(colors = pal)
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-7-1.png" width="672" />


### Scatterplot with new data (non-working days)

```r
df %>% filter(workingday != 1) %>%
  ggplot(., aes(x = hour, y = count, color = temp)) +
  geom_point(position = position_jitter(w=1, h=0)) +
  scale_color_gradientn(colors = pal)
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-8-1.png" width="672" />


### Build a model based on temperature

```r
temp_model <- lm(count ~ temp, data = df)
summary(temp_model)
```

```
## 
## Call:
## lm(formula = count ~ temp, data = df)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -293.32 -112.36  -33.36   78.98  741.44 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   6.0462     4.4394   1.362    0.173    
## temp          9.1705     0.2048  44.783   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 166.5 on 10884 degrees of freedom
## Multiple R-squared:  0.1556,	Adjusted R-squared:  0.1555 
## F-statistic:  2006 on 1 and 10884 DF,  p-value: < 2.2e-16
```
Can be interpreted as for every unit increase in temperature, one can expect a roughly 9 unit increase in bike rental counts.

### Using the temperature model to make predictions

```r
predict(temp_model, data.frame(temp=c(25)))
```

```
##        1 
## 235.3097
```

```r
# Alternatively, y = mx + b, where m = slope, b = intercept, x = temperature
# y = 9.1705 * 25 + 6.0462
```


### Building a more complex model

```r
df$hour <- sapply(df$hour, as.numeric)
model <- lm(count ~ . -casual -registered -datetime -atemp, data = df) # the . means everything else, -subtracts the columsn from the call
summary(model)
```

```
## 
## Call:
## lm(formula = count ~ . - casual - registered - datetime - atemp, 
##     data = df)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -324.61  -96.88  -31.01   55.27  688.83 
## 
## Coefficients:
##              Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  46.91369    8.45147   5.551 2.91e-08 ***
## season       21.70333    1.35409  16.028  < 2e-16 ***
## holiday     -10.29914    8.79069  -1.172    0.241    
## workingday   -0.71781    3.14463  -0.228    0.819    
## weather      -3.20909    2.49731  -1.285    0.199    
## temp          7.01953    0.19135  36.684  < 2e-16 ***
## humidity     -2.21174    0.09083 -24.349  < 2e-16 ***
## windspeed     0.20271    0.18639   1.088    0.277    
## hour          7.61283    0.21688  35.102  < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 147.8 on 10877 degrees of freedom
## Multiple R-squared:  0.3344,	Adjusted R-squared:  0.3339 
## F-statistic:   683 on 8 and 10877 DF,  p-value: < 2.2e-16
```

In this example, the linear model did not perform as well. We noticed from the plots, that the data did not quite express a linear relationships. This is partly due to the seasonality of the data and because bike rental counts fluctuate throughout the day. A model suited for handling these characteristics in these data is likely to perform better. 

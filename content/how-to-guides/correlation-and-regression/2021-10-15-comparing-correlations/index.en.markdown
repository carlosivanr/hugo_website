---
title: 'Correlation in R Pt. 2 - Comparing Correlation Coefficients'
author: Carlos Rodriguez
date: '2021-10-15'
slug: correlation-in-r-2
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-10-15T13:09:17-06:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
weight: 11
draft: true
---

In this second part, we continue to examing the Exam Anxiety data set to look at the separate correlations between the variables of interest in males and females. We then proceed to test if there is a difference between males and females.





### Plot by Gender
We can also visualize the data by grouping by Gender and then calculated separate correlations by Gender.

```r
colors = c( "#440154FF","#1565c0")
ggplot(data, aes(x = Anxiety, y = Exam, color = Gender)) +
  geom_point(alpha = 0.7) +
  scale_color_manual(values = colors) +
  geom_smooth(method = "lm", se = FALSE) +
  theme_minimal() +
  theme(axis.line = element_line(color = "grey70"))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-3-1.png" width="672" />

### Correlations by Gender

```r
correlations <- data %>% 
  group_by(Gender) %>%  
  cor_test(.,
           vars = c("Anxiety"),
           vars2 = c("Exam", "Revise"),
           method = "pearson",
           use = "pairwise.complete.obs")
kable(correlations)
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> Gender </th>
   <th style="text-align:left;"> var1 </th>
   <th style="text-align:left;"> var2 </th>
   <th style="text-align:right;"> cor </th>
   <th style="text-align:right;"> statistic </th>
   <th style="text-align:right;"> p </th>
   <th style="text-align:right;"> conf.low </th>
   <th style="text-align:right;"> conf.high </th>
   <th style="text-align:left;"> method </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Female </td>
   <td style="text-align:left;"> Anxiety </td>
   <td style="text-align:left;"> Exam </td>
   <td style="text-align:right;"> -0.38 </td>
   <td style="text-align:right;"> -2.887975 </td>
   <td style="text-align:right;"> 5.76e-03 </td>
   <td style="text-align:right;"> -0.5944857 </td>
   <td style="text-align:right;"> -0.1182269 </td>
   <td style="text-align:left;"> Pearson </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Male </td>
   <td style="text-align:left;"> Anxiety </td>
   <td style="text-align:left;"> Exam </td>
   <td style="text-align:right;"> -0.51 </td>
   <td style="text-align:right;"> -4.144756 </td>
   <td style="text-align:right;"> 1.31e-04 </td>
   <td style="text-align:right;"> -0.6841707 </td>
   <td style="text-align:right;"> -0.2700554 </td>
   <td style="text-align:left;"> Pearson </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Female </td>
   <td style="text-align:left;"> Anxiety </td>
   <td style="text-align:left;"> Revise </td>
   <td style="text-align:right;"> -0.82 </td>
   <td style="text-align:right;"> -10.079994 </td>
   <td style="text-align:right;"> 0.00e+00 </td>
   <td style="text-align:right;"> -0.8944820 </td>
   <td style="text-align:right;"> -0.7054746 </td>
   <td style="text-align:left;"> Pearson </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Male </td>
   <td style="text-align:left;"> Anxiety </td>
   <td style="text-align:left;"> Revise </td>
   <td style="text-align:right;"> -0.60 </td>
   <td style="text-align:right;"> -5.267088 </td>
   <td style="text-align:right;"> 2.90e-06 </td>
   <td style="text-align:right;"> -0.7482821 </td>
   <td style="text-align:right;"> -0.3876660 </td>
   <td style="text-align:left;"> Pearson </td>
  </tr>
</tbody>
</table>

### Compare correlation values between genders
Filed, Miles, and Field provide the code for a function to compare two correlation values[^2]. This function takes in as input the correlation and sample size for each group. Entering our values into the function results in a z-difference score of -.80 and p-value of .21 which suggests there is no statistical difference between the correlations between Anxiety and Exam scores in females and males.

```r
kable(data %>% count(Gender))
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> Gender </th>
   <th style="text-align:right;"> n </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Female </td>
   <td style="text-align:right;"> 51 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Male </td>
   <td style="text-align:right;"> 52 </td>
  </tr>
</tbody>
</table>

```r
zdifference <- function(r1, r2, n1, n2){
  zd <- (atanh(r1)-atanh(r2))/sqrt(1/(n1-3)+1/(n2-3))
	p <- 1 - pnorm(abs(zd))
	print(paste("Z Difference: ", zd))
	print(paste("One-Tailed P-Value: ", p))
	print(paste("Two-Tailed P-Value: ", (2*p)))
	}
	
zdifference(-0.51, -0.38, 52, 51)
```

```
## [1] "Z Difference:  -0.801014866962209"
## [1] "One-Tailed P-Value:  0.211561519563112"
## [1] "Two-Tailed P-Value:  0.423123039126223"
```

<!-- ### Assumptions -->
<!-- The rstatix package has a nice pipe friendly function to assess the normality of the Anxiety, Exam, and Revise variables.  -->
<!-- ```{r} -->
<!-- kable( -->
<!--   data %>% shapiro_test(Anxiety, Exam, Revise) -->
<!-- ) -->
<!-- ``` -->


### Footnotes
[^2]: Pearson correlation values can be converted to z-scores with the arc tangent function, `atanh()`. Converting correlations to z-scores before conducting other statistics is necessary because correlations values are bounded by -1 and +1. Conversely, 

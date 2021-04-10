---
title: "Study on Linear Regression in Galton Heights Data"
author: "Juan Lamilla"
date: "09/04/2021"
output:
  prettydoc::html_pretty:
    theme: cayman
    highlight: github
    keep_md: true
---

## Setup


```r
knitr::opts_chunk$set(echo = TRUE)

# Required Libraries
library(HistData)
library(tidyverse)
```

```
## -- Attaching packages --------------------------------------- tidyverse 1.3.0 --
```

```
## v ggplot2 3.3.3     v purrr   0.3.4
## v tibble  3.0.6     v dplyr   1.0.4
## v tidyr   1.1.2     v stringr 1.4.0
## v readr   1.4.0     v forcats 0.5.1
```

```
## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

```r
# Data used
data("GaltonFamilies")


# Setup variables
galton_heights <- GaltonFamilies %>%
  filter(gender == "male") %>%
  group_by(family) %>%
  sample_n(1) %>%
  ungroup() %>%
  select(father, childHeight) %>%
  rename(son = childHeight)
```

## Introduction

This is an R Markdown document used to organize and display the code I have written for my case study in the famous 1885 study of Francis Galton exploring the relationship between the heights of adult children and the heights of their parents for my class in linear regression. 


```r
# means and standard deviations
galton_heights %>%
  summarize(mean(father), sd(father), mean(son), sd(son))
```

```
## # A tibble: 1 x 4
##   `mean(father)` `sd(father)` `mean(son)` `sd(son)`
##            <dbl>        <dbl>       <dbl>     <dbl>
## 1           69.1         2.55        69.3      2.72
```


```r
# scatterplot of father and son heights
galton_heights %>%
  ggplot(aes(father, son)) +
  geom_point(alpha = 0.5)
```

![](galton_markdown_files/figure-html/heighs_scatter-1.png)<!-- -->

### Calculating Correlation Coefficient

Using the following examples, we may calculate the correlation coefficient to determine how related two variables are, conveying how they move together. The correlation coefficient is defined for a list of pairs $(x_1, y_1), \dots, (x_n,y_n)$ as the product of the standardized values, so the formula is as follows:

$$
\rho = \frac{1}{n} \sum_{i=1}^n \left( \frac{x_i-\mu_x}{\sigma_x} \right)\left( \frac{y_i-\mu_y}{\sigma_y} \right)
$$
Using this formula we can calculate the correlation coefficient between the father and sons height.


```r
# Calculating correlation coefficient
galton_heights %>% summarize(r = cor(father, son)) %>% pull(r)
```

```
## [1] 0.3732573
```

Since correlation is calculated based on a sample of data, we may further limit the sample size to see how the correlation may be warped.


```r
# compute sample correlation
R <- sample_n(galton_heights, 25, replace = TRUE) %>%
  summarize(r = cor(father, son))
R
```

```
## # A tibble: 1 x 1
##       r
##   <dbl>
## 1 0.248
```

And run a monte-carlo simulation of the sample correlation:


```r
# Monte Carlo simulation to show distribution of sample correlation
B <- 1000
N <- 25
R <- replicate(B, {
  sample_n(galton_heights, N, replace = TRUE) %>%
    summarize(r = cor(father, son)) %>%
    pull(r)
})
qplot(R, geom = "histogram", binwidth = 0.05, color = I("black"))
```

![](galton_markdown_files/figure-html/MonteCarlo-1.png)<!-- -->


```r
# expected value and standard error
mean(R)
```

```
## [1] 0.370806
```

```r
sd(R)
```

```
## [1] 0.1824997
```

Finally we can calculate if a sample size (N) is large enough by creating a QQ-plot


```r
# QQ-plot to evaluate whether N is large enough
data.frame(R) %>%
  ggplot(aes(sample = R)) +
  stat_qq() +
  geom_abline(intercept = mean(R), slope = sqrt((1-mean(R)^2)/(N-2)))
```

![](galton_markdown_files/figure-html/QQ-Plot-1.png)<!-- -->


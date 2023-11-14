---
title: "First R Project"

date: 2023-11-13T08:59:51-05:00
url: /First-R-project/
image: /images/2020-thumbs/First-R-project.jpg
categories:
  - Data Analysis
tags:
draft: true
---

This project was done the summer of 2022 and later added to the site.
<!--more-->
  
R Notebook


# clean up workspace environment

```r
rm(list = ls())
```
# all packages used for the assignment

```r
library(tidyverse)
```
## Chapter 12 Exercises

```r
library(dcData)
data(BabyNames)
```

## Chapter 12

```r
summary(BabyNames)
```
| name             | sex              | count           | year         |
|------------------|------------------|-----------------|--------------|
| Length:1792091   | Length:1792091   | Min.   :    5.0 | Min.   :1880 |
| Class :character | Class :character | 1st Qu.:    7.0 | 1st Qu.:1948 |
| Mode  :character | Mode  :character | Median :   12.0 | Median :1981 |
|                  |                  | Mean   :  186.1 | Mean   :1972 |
|                  |                  | 3rd Qu.:   32.0 | 3rd Qu.:2000 |
|                  |                  | Max.   :99674.0 | Max.   :2013 |
1-10 of 10 rows

## Problem 12.1

```r
BothSexes <-
  BabyNames %>%
  pivot_wider(names_from = sex, values_from = count) %>%
  filter(M > 5 | F > 5)
```
```r
BothSexes$math <- abs(log( BothSexes[[3]] / BothSexes[[4]] ))

Balance <- 
  BothSexes %>%
  arrange(math)
  
head(Balance, n=10)
```

| name      | year | F  | M  |
|-----------|------|----|----|
| Erie      | 1880 | 6  | 6  |
| Sammie    | 1880 | 6  | 6  |
| Theo      | 1881 | 8  | 8  |
| Bird      | 1881 | 6  | 6  |
| Augustine | 1882 | 8  | 8  |
| Tommie    | 1882 | 8  | 8  |
| Orrie     | 1883 | 7  | 7  |
| Verne     | 1884 | 8  | 8  |
| Jewel     | 1884 | 6  | 6  |
| Tracy     | 1885 | 12 | 12 |

```r
Balance2 <-
  Balance %>%
  filter(F > 100 | M > 100)

head(Balance2, n=10)
```
| name    | year | F    | M    |
|---------|------|------|------|
| Lavern  | 1953 | 103  | 103  |
| Marion  | 1977 | 229  | 229  |
| Dusty   | 1979 | 194  | 194  |
| Justice | 2003 | 665  | 665  |
| Baby    | 2003 | 245  | 245  |
| Tegan   | 2006 | 145  | 145  |
| Ryley   | 2007 | 186  | 186  |
| Rian    | 2007 | 128  | 128  |
| Jaylin  | 2008 | 524  | 524  |
| Leslie  | 1946 | 2139 | 2139 |
1-10 of 10 rows

## Problem 12.2

   1. Version One
   2. Version two is wider,

```r
BothSexes <-
  BabyNames %>%
  pivot_wider(names_from = sex, values_from = count) %>%
  filter(M > 5 | F > 5)
```

  3. Version 3 is wider

```r
BothSexes <-
  BabyNames %>%
  pivot_wider(names_from = year, values_from = count) %>%
  filter(M > 5 | F > 5)
```

    I would say that it is easier to start from version 2 as both the year and the sexes are already columns.

    I would go to version 2 as it is better suited to find the ratio of male to female as both are columns

## Problem 12.3

    A is wider than C . B is wider than C. And A is wider than B.
    Frame B would be the best to look at 2000 to 2001. I would subtract the count of both years
    Frame B again would be the best. I would just sum the values for each year.

## Problem 12.4

    You can’t easily compare before and after of a subject.
    I wouldn’t change the data table as to change it would make it too narrow.

### Chapter 13.2
## Problem 2

# Calculating Top 100 Counts
``` r 
Rankings <-
  BabyNames %>%
  group_by(year) %>%
  top_n(100, count) %>%
  summarise(total = sum(count))
Rankings$ranking <- "Top_100"
```

# Calculating Bottom Counts
```r 
Rankings2 <-
  BabyNames %>%
  group_by(year) %>%
  summarise(total = sum(count))
Rankings2$total <- Rankings2$total - Rankings$total
Rankings2$ranking <- "Below"
```
# Merging the Two Counts and Reordering
```r
Rankings <- rbind(Rankings, Rankings2)
Rankings <- 
  Rankings %>%
  arrange(year)
```

## Conclusion
This class used the tidy packages and although I am not a fan of it, it was required per the grading rubric. 





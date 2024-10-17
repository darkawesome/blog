---
date: '2024-10-17T11:12:18-04:00'
draft: false
title: 'Chapter12 Exercise'
---


## Assigned Exercises

    DC Ch 12 Exercises: 12.1 (part 1-4)
    DC Ch 13 Exercises: 13.2

## Front Matter

```r
    # clean up workspace environment
    rm(list = ls())

    # all packages used for the assignment
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

 name               sex                count              year     
 Length:1792091     Length:1792091     Min.   :    5.0   Min.   :1880  
 Class :character   Class :character   1st Qu.:    7.0   1st Qu.:1948  
 Mode  :character   Mode  :character   Median :   12.0   Median :1981  
                                       Mean   :  186.1   Mean   :1972  
                                       3rd Qu.:   32.0   3rd Qu.:2000  
                                       Max.   :99674.0   Max.   :2013  


### Problem 12.1

```r  
BothSexes <-
BabyNames %>%
pivot_wider(names_from = sex, values_from = count) %>%
filter(M > 5 | F > 5)
```

sex should replace ??? in the statement 

```r
BothSexes$math <- abs(log( BothSexes[[3]] / BothSexes[[4]] ))

Balance <- 
BothSexes %>%
arrange(math)

head(Balance, n=10)

```

```r
Balance2 <-
Balance %>%
filter(F > 100 | M > 100)

head(Balance2, n=10)
```

### Problem 12.2
1. Version One 
2. Version two is wider, 

```r

BothSexes <-
BabyNames %>%
pivot_wider(names_from = sex, values_from = count) %>%
filter(M > 5 | F > 5)

```

3, Version 3 is wider

```r

BothSexes <-
BabyNames %>%
pivot_wider(names_from = year, values_from = count) %>%
filter(M > 5 | F > 5)
```

4. I would say that it is easier to start from version 2 as both the year and the sexes are already columns.

5. I would go to version 2 as it is better suited to find the ratio of male to female as both are columns

### Problem 12.3

1. A is wider than C . B is wider than C. And A is wider than B.
2. Frame B would be the best to look at 2000 to 2001. I would subtract the count of both years
3. Frame B again would be the best. I would just sum the values for each year.

### Problem 12.4

1. You can't easily compare before and after of a subject.
2. I wouldn't change the data table as to change it would make it too narrow.

### Chapter 13.2

### Problem 2

```r
# Calculating Top 100 Counts
Rankings <-
BabyNames %>%
group_by(year) %>%
top_n(100, count) %>%
summarise(total = sum(count))
Rankings$ranking <- "Top_100"

# Calculating Bottom Counts
Rankings2 <-
BabyNames %>%
group_by(year) %>%
summarise(total = sum(count))
Rankings2$total <- Rankings2$total - Rankings$total
Rankings2$ranking <- "Below"

# Merging the Two Counts and Reordering
Rankings <- rbind(Rankings, Rankings2)
Rankings <- 
Rankings %>%
arrange(year)
```

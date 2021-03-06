---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
author: "Huilong"
date: "April 15, 2018"
---

```r
knitr::opts_chunk$set(echo = TRUE)
library(knitr)
library(dplyr)
```


## Loading and preprocessing the data



```r
setwd("C:/Users/huilong/Documents/DataScience/DS5")
path=getwd()
url <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
if (!file.exists(path)) {dir.create(path)}
download.file(url, file.path(path, "Data.zip"))

unzip(zipfile = "Data.zip")

act<-read.csv("activity.csv", header=T)

```


## What is mean total number of steps taken per day?

```r
act2 <- aggregate(steps ~ date, data = act, sum)

hist(act2$steps, breaks=10, col="red", main="Total steps taken per day", xlab="#Steps per day" )
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

```r
mean(act2$steps)
```

```
## [1] 10766.19
```

```r
median(act2$steps)
```

```
## [1] 10765
```
## What is the average daily activity pattern?

```r
act3 <- aggregate(steps ~ interval, data = act, sum)

plot(act3$interval, act3$steps, type="l", col="red", main="Average steps per period", 
     xlab="Interval", ylab="Avg. steps")

max_interval<-act3[which.max(act3$steps),1]

abline(v=835, lwd=2, col="blue")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

The 5-minute interval containing the maximum number of steps is 835.

## Imputing missing values

```r
sum(is.na(act$steps))
```

```
## [1] 2304
```

```r
act_input<- transform(act, steps = ifelse(is.na(act$steps), act3$steps[match(act$interval, act3$interval)], act$steps))

sum(is.na(act_input$steps))
```

```
## [1] 0
```

```r
act_input2 <- aggregate(steps ~ date, data = act_input, sum)

hist(act_input2$steps, col="red", main="Total steps taken per day - Inputed", xlab="#Steps per day" )
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

```r
mean(act_input2$steps)
```

```
## [1] 84188.07
```

```r
median(act_input2$steps)
```

```
## [1] 11458
```

```r
mean(act_input2$steps)-mean(act2$steps)
```

```
## [1] 73421.88
```

```r
median(act_input2$steps)-median(act2$steps)
```

```
## [1] 693
```
## Are there differences in activity patterns between weekdays and weekends?

```r
act_input[,2]<-as.Date(act_input$date)
act_wk<- mutate(act_input, day = ifelse(weekdays(act_input$date) == "Saturday" | weekdays(act_input$date) == "Sunday", "weekend", "weekday"))
act_wk$day<-as.factor(act_wk$day)
str(act_wk)
```

```
## 'data.frame':	17568 obs. of  4 variables:
##  $ steps   : int  91 18 7 8 4 111 28 46 0 78 ...
##  $ date    : Date, format: "2012-10-01" "2012-10-01" ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
##  $ day     : Factor w/ 2 levels "weekday","weekend": 1 1 1 1 1 1 1 1 1 1 ...
```

```r
act_wk2 <- aggregate(steps ~ interval + day, act_wk, mean)

library(lattice)

xyplot(steps ~ interval | day, data=act_wk2, layout=c(1,2), type="l", col="red", main="Average steps per period by weekend/weekday", 
     xlab="Interval", ylab="Avg. steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->





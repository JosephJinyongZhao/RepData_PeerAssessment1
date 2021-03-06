# Reproducible Research: Peer Assessment 1

---
title: "Reproducible Research: Peer Assessment 1"
output: PA1_template.md
  html_document: PA1_template.html
    keep_md: true


```r
wd <- "C:\\data\\RepData_PeerAssessment1"
setwd(wd)
getwd()
```

```
## [1] "C:/data/RepData_PeerAssessment1"
```

```r
Sys.setlocale("LC_TIME", "English")
```

```
## [1] "English_United States.1252"
```

```r
library(ggplot2)
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
## 
## The following object is masked from 'package:stats':
## 
##     filter
## 
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

## Loading and preprocessing the data


```r
file <- "C:\\data\\RepData_PeerAssessment1\\activity.csv"
dat <- read.table(file, header = TRUE, sep = ",", na.strings = "NA", stringsAsFactors = FALSE)
dat2 <- tapply(dat$steps, dat$date, sum)
dat3 <- as.data.frame(dat2)

dat4 <- tapply(dat$steps, dat$interval, mean, na.rm = TRUE)
dat4 <- as.data.frame(dat4)
names(dat4) <- c("steps")
dat4$interval <- as.numeric(rownames(dat4))

dat$date <- as.Date(dat$date)
```
## What is mean total number of steps taken per day?


```r
names(dat3) <- c("steps")
mean <- mean(dat3$steps, na.rm = TRUE)
median <- median(dat3$steps, na.rm = TRUE)
qplot(dat3$steps, main = "Total Number of Steps Taken Each Day", xlab = "Steps Taken Each Day")
```

```
## stat_bin: binwidth defaulted to range/30. Use 'binwidth = x' to adjust this.
```

![plot of chunk unnamed-chunk-3](./PA1_template_files/figure-html/unnamed-chunk-3.png) 


The mean and median total number of steps taken per day are 1.0766 &times; 10<sup>4</sup> and 10765 seperately.

## What is the average daily activity pattern?


```r
qplot(interval, steps, data = dat4, geom = c("line"))
```

![plot of chunk unnamed-chunk-4](./PA1_template_files/figure-html/unnamed-chunk-4.png) 

```r
interval <- dat$interval[which(dat4$steps == max(dat4$steps))]
```

The 835 5-minute interval contains the maximum number of steps.

## Imputing missing values


```r
na <- sum(is.na(dat$steps))
dat$steps[is.na(dat$steps)] <- mean(dat$steps, na.rm = TRUE)
dat5 <- tapply(dat$steps, dat$date, sum)
dat5 <- as.data.frame(dat5)
names(dat5) <- c("steps")
str(dat5)
```

```
## 'data.frame':	61 obs. of  1 variable:
##  $ steps: num [1:61(1d)] 10766 126 11352 12116 13294 ...
##   ..- attr(*, "dimnames")=List of 1
##   .. ..$ : chr  "2012-10-01" "2012-10-02" "2012-10-03" "2012-10-04" ...
```

```r
qplot(steps, data = dat5)
```

```
## stat_bin: binwidth defaulted to range/30. Use 'binwidth = x' to adjust this.
```

![plot of chunk unnamed-chunk-5](./PA1_template_files/figure-html/unnamed-chunk-5.png) 

```r
meanfill <- mean(dat5$steps)
medianfill <- median(dat5$steps)
```

There are 2304 NAs in the dataset.
I replace all of the NAs with the mean of 5 minute interval. 
The mean and median total number of steps taken perday are 1.0766 &times; 10<sup>4</sup> and 1.0766 &times; 10<sup>4</sup> seperate, which are the same with the estimated from the first part. The impact of imputing missing data is average out in my strategy where using mean of 5 minute interval to fill in NAs. 

## Are there differences in activity patterns between weekdays and weekends?


```r
dat$Date <- weekdays(as.Date(dat$date))
dat$weekend <- as.factor(ifelse(dat$Date == "Saturday" | dat$Date =="Sunday", dat$weekend <- c("weekend"), dat$weekend <- c("weekday")))
pattern <- tbl_df(dat)
pattern2 <- group_by(pattern, weekend, interval)
pattern3 <- summarize(pattern2, steps = mean(steps))
qplot(interval, steps, data = pattern3, geom = c("line"), facets = weekend ~ .)
```

![plot of chunk unnamed-chunk-6](./PA1_template_files/figure-html/unnamed-chunk-6.png) 

We could notice the pattern between weekday and weekend are different. the pattern in weekend is more active than the one in weekdays.

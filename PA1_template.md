---
title: 'Assignment 1: Activity Monitoring'
author: "IB"
date: "June 1, 2015"
output: html_document
---

```r
require (plyr)
require (ggplot2)
```

#I. Loading and preprocessing the data.

```r
data <- read.csv("activity.csv", header = TRUE)
head(data)
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
## 4    NA 2012-10-01       15
## 5    NA 2012-10-01       20
## 6    NA 2012-10-01       25
```

```r
summary(data)
```

```
##      steps                date          interval     
##  Min.   :  0.00   2012-10-01:  288   Min.   :   0.0  
##  1st Qu.:  0.00   2012-10-02:  288   1st Qu.: 588.8  
##  Median :  0.00   2012-10-03:  288   Median :1177.5  
##  Mean   : 37.38   2012-10-04:  288   Mean   :1177.5  
##  3rd Qu.: 12.00   2012-10-05:  288   3rd Qu.:1766.2  
##  Max.   :806.00   2012-10-06:  288   Max.   :2355.0  
##  NA's   :2304     (Other)   :15840
```

#II. What is mean total number of steps taken per day?

###1. Calculate the total number of steps taken per day.

```r
steps.day <- ddply(data, "date", summarise, steps = sum(steps))
head(steps.day)
```

```
##         date steps
## 1 2012-10-01    NA
## 2 2012-10-02   126
## 3 2012-10-03 11352
## 4 2012-10-04 12116
## 5 2012-10-05 13294
## 6 2012-10-06 15420
```

###2. Make a histogram of the total number of steps taken each day.

```r
ggplot(steps.day, aes(x=steps)) + geom_histogram(colour = "black", 
                                                 fill = "skyblue", 
                                                 binwidth = 1000) + 
        xlab("Total Steps per Day") +
        ylab("Frequency")+
        theme_classic()
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png) 

###3. Calculate and report the mean and median of the total number of steps taken per day.

```r
mean.steps <- round(mean(steps.day$steps,na.rm=TRUE ), digits=0)
mean.steps
```

```
## [1] 10766
```

```r
median.steps <- round(median(steps.day$steps, na.rm=TRUE), digits=0)
median.steps
```

```
## [1] 10765
```
####The mean of the total number of steps taken per day is 10766. 
####The median of the total number of steps taken per day is 10765.

#III. What is the average daily activity pattern?

###1.Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
interval.day <- ddply(data, "interval", summarise, steps = mean(na.omit(steps)))
head(interval.day)
```

```
##   interval     steps
## 1        0 1.7169811
## 2        5 0.3396226
## 3       10 0.1320755
## 4       15 0.1509434
## 5       20 0.0754717
## 6       25 2.0943396
```


```r
ggplot(interval.day, aes(x = interval, y = steps)) + 
    geom_line(size=1, colour = "red") + theme_bw()+
        xlab("Interval") +
        ylab("Average Number of Steps")
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7-1.png) 

###2.Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
step.max = round(interval.day[(which.max(interval.day$steps)),], digits=0)
step.max
```

```
##     interval steps
## 104      835   206
```

#####The 835th interval contains the average maximum number of 206 steps. 
####Same results reported as a table:

Interval | Steps
-------- | -----
835      | 206  

 Table: __Interval with Average Maximum Steps__ 

#III. Imputing missing values.
 
###1.Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
total.NA <- sum(is.na(data))
total.NA
```

```
## [1] 2304
```
####The total number of rows with NAs in the dataset is 2304.

###2.Devise a strategy for filling in all of the missing values in the dataset.Use the mean for that 5-minute interval.

```r
newdata <- merge(data, interval.day, by = "interval")

for (i in 1:nrow(newdata)){
 if (is.na(newdata$steps.x[i])) {
   newdata$steps.x[i] <- newdata$steps.y[i]
 }}
```

###3.Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
newdata$steps.y=NULL
names(newdata)[2]<-"steps"
newdata <- newdata[order(newdata$date, newdata$interval),] 
newdata <- newdata[c(2,3,1)]
head(newdata)
```

```
##         steps       date interval
## 1   1.7169811 2012-10-01        0
## 63  0.3396226 2012-10-01        5
## 128 0.1320755 2012-10-01       10
## 205 0.1509434 2012-10-01       15
## 264 0.0754717 2012-10-01       20
## 327 2.0943396 2012-10-01       25
```

###4.Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. 


```r
newsteps.day <- ddply(newdata, "date", summarise, steps = sum(steps))

ggplot(newsteps.day, aes(x=steps)) + geom_histogram(colour = "black", 
                                                 fill = "cornsilk", 
                                                 binwidth = 1000) + 
        xlab("Total Steps per Day") +
        ylab("Frequency")+
        theme_classic()
```

![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12-1.png) 


```r
mean.steps2 <- round(mean(newsteps.day$steps,na.rm=TRUE ), digits=0)
mean.steps2
```

```
## [1] 10766
```

```r
median.steps2 <- round(median(newsteps.day$steps, na.rm=TRUE), digits=0)
median.steps2
```

```
## [1] 10766
```
####The mean of the total number of steps taken per day is 10766. 
####The median of the total number of steps taken per day is 10766.

###Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

The mean is the same as in the first part: 10,766. The median increased from 10,765 to 10,766.

The new histogram is very similar to the histogram from the first part. However, the highest frequency increased and moved to the right from 10,000 to 11,000 steps per day.

#IV. Are there differences in activity patterns between weekdays and weekends?

###1.Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
newdata$day <- ifelse(weekdays(as.Date(newdata$date)) %in% c("Saturday", "Sunday"), "weekend", "weekday")
head(newdata)
```

```
##         steps       date interval     day
## 1   1.7169811 2012-10-01        0 weekday
## 63  0.3396226 2012-10-01        5 weekday
## 128 0.1320755 2012-10-01       10 weekday
## 205 0.1509434 2012-10-01       15 weekday
## 264 0.0754717 2012-10-01       20 weekday
## 327 2.0943396 2012-10-01       25 weekday
```

###2.Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). 


```r
daydata <- ddply(newdata, .(interval, day), summarise, steps = mean(steps))
head(daydata)
```

```
##   interval     day      steps
## 1        0 weekday 2.25115304
## 2        0 weekend 0.21462264
## 3        5 weekday 0.44528302
## 4        5 weekend 0.04245283
## 5       10 weekday 0.17316562
## 6       10 weekend 0.01650943
```

```r
tsplot <- ggplot(daydata, aes(x = interval, y = steps, colour = day)) + 
    geom_line(size=1) + facet_grid(day ~ .) + theme_bw() 

tsplot
```

![plot of chunk unnamed-chunk-15](figure/unnamed-chunk-15-1.png) 


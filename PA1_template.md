---
output:
  html_document:
    keep_md: yes
---
# Reproducible Research: Peer Assessment 1

## Loading and preprocessing the data
>Show any code that is needed to

 1. Load the data (i.e. read.csv())
 2. Process/transform the data (if necessary) into a format suitable for your analysis
 

```r
Data <- read.csv("./data/activity.csv")
```

## What is mean total number of steps taken per day?
>For this part of the assignment, you can ignore the missing values in the dataset.

 1. Make a histogram of the total number of steps taken each day
 

```r
Dailystep <- aggregate(steps ~ date, data=Data, FUN=sum)
barplot(Dailystep$steps, names.arg=Dailystep$date, xlab="Date", ylab="Steps")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2-1.png) 

 2. Calculate and report the mean and median total number of steps taken per day


```r
mean(Dailystep$steps)
```

```
## [1] 10766.19
```

```r
median(Dailystep$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?
>What is the average daily activity pattern?

 1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
 

```r
Intervalstep <- aggregate(steps ~ interval, data=Data, FUN=mean)
plot(Intervalstep, type = "l")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png) 

 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
Intervalstep$interval[which.max(Intervalstep$steps)]
```

```
## [1] 835
```

## Imputing missing values
>Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.
 
 1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
sum(is.na(Data$steps))
```

```
## [1] 2304
```

 2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
 

```r
Newdata <- Data
suppressWarnings(Newdata$steps[!complete.cases(Data)] <- Intervalstep[match(Newdata$interval, Intervalstep$interval), "steps"])
```

 3. Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
Dailystep <- aggregate(steps ~ date, data=Newdata, FUN=sum)
```

 4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?
 

```r
barplot(Dailystep$steps, names.arg=Dailystep$date, xlab="Date", ylab="Steps")
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9-1.png) 

```r
mean(Dailystep$steps)
```

```
## [1] 10766.19
```

```r
median(Dailystep$steps)
```

```
## [1] 10766.19
```
 
## Are there differences in activity patterns between weekdays and weekends?
>For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.
 
 1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.
 

```r
Daytype <- function(date) {
  if (weekdays(as.Date(date)) %in% c("Saturday", "Sunday")) { "Weekend" }
  else { "Weekday" }
}
Newdata$Daytype <- as.factor(sapply(Newdata$date, Daytype))
```
 
 2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was creating using simulated data:
 

```r
for (type in c("Weekend", "Weekday")) {
  Dailystep <- aggregate(steps ~ interval, data=Newdata, subset=Newdata$Daytype==type, FUN=mean)
  plot(Dailystep, type="l", main=type)
}
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11-1.png) ![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11-2.png) 
 
 
 


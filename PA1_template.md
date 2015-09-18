# Reproducible Research: Peer Assessment 1
Calvin Chin  


## Loading and preprocessing the data

```r
library(ggplot2)

mydata <- read.csv("activity.csv")
```


## What is mean total number of steps taken per day?
1. Make a histogram of the total number of steps taken each day

```r
summary_total <- aggregate(
                    x = list(total = mydata$steps), 
                    by = list(date = mydata$date), 
                    FUN = "sum", na.rm = TRUE)

qplot(x = total, 
      data = summary_total,
      geom = "histogram", binwidth = 1000,
      main = "Total steps taken each day", 
      xlab = "Date", ylab = "Total Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

2. Calculate and report the mean and median total number of steps taken per day

```r
mean(summary_total$total)
```

```
## [1] 9354.23
```

```r
median(summary_total$total)
```

```
## [1] 10395
```


## What is the average daily activity pattern?
1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
summary_average <- aggregate(
                    x = list(average = mydata$steps), 
                    by = list(interval = mydata$interval), 
                    FUN = "mean", na.rm = TRUE)

qplot(x = interval, 
      y = average,
      data = summary_average,
      geom = "line", 
      main = "Average steps taken for each 5-minute interval", 
      xlab = "Interval", ylab = "Average")
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png) 

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
summary_average[which.max(summary_average$average), ]
```

```
##     interval  average
## 104      835 206.1698
```

## Imputing missing values
1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
sum(!complete.cases(mydata$steps))
```

```
## [1] 2304
```

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

```r
summary_average_by_interval <- aggregate(
                    x = list(average = mydata$steps), 
                    by = list(interval = mydata$interval), 
                    FUN = "mean", na.rm = TRUE)

mydata_merge <- merge(
                  x = mydata, 
                  y = summary_average_by_interval, 
                  by = "interval"
                  )
```

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
mydata_new <- mydata

mydata_new$steps[is.na(mydata$steps)] <- mydata_merge$average[is.na(mydata$steps)]
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
summary_total_new <- aggregate(
                    x = list(total = mydata_new$steps), 
                    by = list(date = mydata_new$date), 
                    FUN = "sum")

qplot(x = total,
      data = summary_total_new,
      geom = "histogram", binwidth = 1000,
      main = "Total steps taken each day", 
      xlab = "Date", ylab = "Total Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png) 

```r
mean(summary_total_new$total)
```

```
## [1] 10889.8
```

```r
median(summary_total_new$total)
```

```
## [1] 11015
```


## Are there differences in activity patterns between weekdays and weekends?
1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
mydata_new$week <- as.factor(ifelse(weekdays(as.Date(mydata_new$date)) %in% c("Saturday","Sunday"), "Weekend", "Weekday"))
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

```r
summary_average_by_week <- aggregate(
                    x = list(average = mydata_new$steps), 
                    by = list(
                            interval = mydata_new$interval, 
                            week = mydata_new$week), 
                    FUN = "mean")

qplot(x = interval, 
      y = average,
      data = summary_average_by_week,
      geom = "line", 
      facets = week ~ .,
      main = "Average steps taken for each 5-minute interval", 
      xlab = "Interval", ylab = "Average")
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png) 


# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

* Load the data (i.e. read.csv())


```r
activity=read.csv("activity.csv")
```


## What is mean total number of steps taken per day?

* Calculate the total number of steps taken per day

```r
steps_to_Day <- aggregate(steps ~ date, data = activity, FUN=sum, na.rm=TRUE)
```

*If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day


```r
hist(steps_to_Day$steps)
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

* Calculate and report the mean and median of the total number of steps taken per day

```r
mean(steps_to_Day$steps)
```

```
## [1] 10766.19
```

```r
median(steps_to_Day$steps)
```

```
## [1] 10765
```


## What is the average daily activity pattern?

* Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png) 

* Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```
## [1] 835
```


## Imputing missing values

* Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```
## [1] 2304
```


* Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

Will use mean of the intervals to fill missing values:


```r
activity2 <- activity
```

* Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
for (i in 1:nrow(activity)){
  if(is.na(activity2[i,]$steps)){
        activity2[i,]$steps<-steps_to_Interval[steps_to_Interval$interval==activity2[i,]$interval,]$steps
  }
}
head(activity2)
```

```
##       steps       date interval
## 1 1.7169811 2012-10-01        0
## 2 0.3396226 2012-10-01        5
## 3 0.1320755 2012-10-01       10
## 4 0.1509434 2012-10-01       15
## 5 0.0754717 2012-10-01       20
## 6 2.0943396 2012-10-01       25
```


* Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
steps_to_Day2<-aggregate(steps~date,data=activity2,sum)
hist(steps_to_Day2$steps)
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png) 

```r
mean(steps_to_Day2$steps)
```

```
## [1] 10766.19
```

```r
median(steps_to_Day2$steps)
```

```
## [1] 10766.19
```

There is no significant different before and after missing values are filled.


## Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.


* Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.


```r
activity2$isWday=ifelse(as.POSIXlt(as.Date(activity2$date))$wday < 6, "weekend","weekday")
activity2$isWday=factor(activity2$isWday,levels=c("weekday","weekend"))
```


* Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
steps_to_Interval2=aggregate(steps~interval+isWday,activity2,mean)
library(lattice)
xyplot(steps~interval|factor(isWday),data=steps_to_Interval2,aspect=1/2,type="l")
```

![](PA1_template_files/figure-html/unnamed-chunk-12-1.png) 

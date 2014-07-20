Reproductive Research Assignment 1
========================================================

## Load the Data##

```r
    activity <- read.csv('activity.csv')
```

## Calculate the mean and median of total steps per day##

```r
    steps_by_date <- tapply(activity$steps, activity$date, sum, na.rm = TRUE)
    barplot(steps_by_date, xlab = 'Date', ylab = 'Steps', main = 'Total number of steps taken each day')
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 

```r
    mean_date <- mean(steps_by_date)
    median_date <- median(steps_by_date)
```
The mean of total steps per day is 9354.2295; The median of total steps per day is 10395.

## Daily Activity Pattern##

```r
    steps_by_interval <- tapply(activity$steps, activity$interval, mean, na.rm = TRUE)
    plot(names(steps_by_interval), steps_by_interval, type = 'l', col = 'blue', xlab = 'Interval', ylab = 'Average steps', main = 'Average number of steps in each interval')
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3.png) 

```r
    max_interval <- names(which.max(steps_by_interval))
```
The interval containing the maximum number of steps is 835

## Inputing missing values##
The filling methods is using the mean of the 5-minute inteval.

```r
    na_count <- sum(is.na(activity$steps))
    na_place <- which(is.na(activity$steps))
    activity_new <- activity
    na_to_replace_with <- steps_by_interval[as.character(activity_new$interval[na_place])]
    activity_new$steps[is.na(activity_new$steps)] <- na_to_replace_with
```
## Calculate the mean and median of total steps per day after filling in missing values##

```r
    steps_by_date_new <- tapply(activity_new$steps, activity_new$date, sum)
    barplot(steps_by_date_new, xlab = 'Date', ylab = 'Steps', main = 'Total number of steps taken each day')
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5.png) 

```r
    mean_date_new <- mean(steps_by_date_new)
    median_date_new <- median(steps_by_date_new)
```
The mean of total steps per day is 1.0766 &times; 10<sup>4</sup>; The median of total steps per day is 10395.
After filling in missing values, the mean of total steps per day increases by 1411.9592. The median does not change.


## Activity patterns of weekdays and weekends

### Create new factor variable###

```r
library('timeDate')
```

```
## Warning: package 'timeDate' was built under R version 3.1.1
```

```r
weekday <- as.factor(isWeekday(activity_new$date))
levels(weekday) <- c('weekend', 'weekday')
activity_new$weekday <- weekday
```

### Calculate and make the plot###

```r
    steps_by_interval_weekdays <- aggregate(activity_new$steps, list(activity_new$interval, activity_new$weekday), mean)
    colnames(steps_by_interval_weekdays) <- c('interval', 'weekday', 'mean')
    steps_by_interval_weekdays <- split(steps_by_interval_weekdays, steps_by_interval_weekdays$weekday)
    library(ggplot2)
    p1 <- ggplot(data = steps_by_interval_weekdays[[1]], aes(x = interval, y = mean)) + geom_line(colour = 'blue') + xlab('Interval') + ylab('Number of Steps') + ggtitle('Weekend')
    p2 <- ggplot(data = steps_by_interval_weekdays[[2]], aes(x = interval, y = mean)) + geom_line(colour = 'blue') + xlab('Interval') + ylab('Number of Steps') + ggtitle('Weekday')
    library('gridExtra')
```

```
## Warning: package 'gridExtra' was built under R version 3.1.1
```

```
## Loading required package: grid
```

```r
    grid.arrange(p1, p2)
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7.png) 







# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data


```r
data <- read.csv("activity.csv")
attach(data)
```

## What is mean total number of steps taken per day?

```r
steps_per_day <- aggregate(steps ~ date, data, sum)$steps
hist(steps_per_day)
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 


Mean:

```r
mean(steps_per_day)
```

```
## [1] 10766
```


Median:

```r
median(steps_per_day)
```

```
## [1] 10765
```


## What is the average daily activity pattern?


```r
steps_by_interval <- aggregate(steps ~ interval, data, mean)
plot(steps_by_interval$steps, type = "l")
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5.png) 

Row of interval with highest mean steps


```r
max_interval = which.max(steps_by_interval$steps)
max_interval
```

```
## [1] 104
```


Original "interval" value of this row:

```r
interval[max_interval]
```

```
## [1] 835
```



## Imputing missing values

Number of missing rows in total

```r
sum(is.na(steps))
```

```
## [1] 2304
```



Unsophisticated strategy: Fill in zeros for every na value.

```r
steps[is.na(steps)] <- 0
```


Create new dataframe with imputed steps

```r
imputed <- data.frame(date = date, steps = steps, interval = interval)
attach(imputed)
```

```
## Das folgende Objekt ist maskiert _by_ .GlobalEnv:
## 
##     steps
## Das folgende Objekt ist maskiert from data:
## 
##     date, interval, steps
```

```r
imputed_per_day <- aggregate(steps ~ interval, imputed, sum)
hist(imputed_per_day$steps)
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10.png) 



New mean

```r
mean(imputed_per_day$steps)
```

```
## [1] 1981
```


New median

```r
median(imputed_per_day$steps)
```

```
## [1] 1808
```


My imputation strategy shifts the distribution markedly towards lower values.

## Are there differences in activity patterns between weekdays and weekends?
The weekdays function outputs weekdays as a function of the current locale, which for me unluckily isn't English. For reproducibility I found a better way to do this  on the interwebs.


```r
weekdays <- c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", 
    "Saturday")[as.POSIXlt(date)$wday + 1]
```

Now aggregate by weekendedness:

```r
weekend <- weekdays == "Sunday" | weekdays == "Saturday"
imputed$weekend = weekend
weekday_activities <- aggregate(steps ~ weekend + interval, imputed, mean)
```


Activity on weekends:

```r
plot(weekday_activities[weekday_activities$weekend == T, ]$steps, t = "l")
```

![plot of chunk unnamed-chunk-15](figure/unnamed-chunk-15.png) 


Activity on weekdays:

```r
plot(weekday_activities[weekday_activities$weekend == F, ]$steps, t = "l")
```

![plot of chunk unnamed-chunk-16](figure/unnamed-chunk-16.png) 


The subject seems to walk more on the weekend and in the evening, while his way to work might involve more walking than wherever he is going on the weekend. Also the sharper increase in activity on weekdays might indicate a more relaxed attitude toward early rising on the weekend.

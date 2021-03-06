#Loading and preprocessing the data


```r
data1 <- read.csv("activity.csv")
library(dplyr)
```

#What is mean total number of steps taken per day?

**Storing the no.of missing values, total sum,avg and median no. of steps**

```r
a = data1 %>% group_by(date) %>% summarise_at(vars(steps),funs(missing = sum(is.na(.)),
                                                               Total = sum(.,na.rm = T),Average = mean(.,na.rm = T),
                                                               Median = median(.,na.rm = T)))

View(a)
```

**Drawing the histogram for total number of steps taken each day!**

```r
hist(a$Total,col = "blue",main = "Histogram",xlab = "Total no. of steps")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png)


#What is the average daily activity pattern?


**Calculating mean for various intervals**

```r
b = data1 %>% group_by(interval) %>% summarise_at(vars(steps),funs(Average = mean(.,na.rm = T)))
View(b)
```

**Plotting the time series of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)**

```r
plot(b$interval,b$Average,xlab="Interval",type="l", 
     ylab="Average number of Steps",main="Time Series Plot of Interval vs Average Number of Steps Taken")
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6-1.png)

**Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?**

```r
ans <- b$interval[b$Average == max(b$Average)]
```

#Imputing missing values
**Total number of missing values**

```r
sum(a$missing)
```

```
## [1] 2304
```

**Imputing missing values in a new dataset**

```r
data2 = data1
for(i in 1:nrow(data2))
{
  if(is.na(data2$steps[i]) == TRUE)
  {
    int =   data2$interval[i]
  
data2$steps[i] = mean(data2$steps[data2$interval == int],na.rm = T)
}
}
```

**Mean and median after imputing the values**

```r
c = data2 %>% group_by(date) %>% summarise_at(vars(steps),funs(missing = sum(is.na(.)),
                                                               Total = sum(.,na.rm = T),Average = mean(.,na.rm = T),
                                                               Median = median(.,na.rm = T)))

View(c)
```

**Histogram of total no. of steps after imputing the missing values.**

```r
hist(c$Total,col = "pink",main = "Histogram",xlab = "Total no. of steps")
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11-1.png)


#Are there differences in activity patterns between weekdays and weekends?

**Creating a new factor variable denoting weekdays and weekends**


```r
data2$day =weekdays(as.Date(data2$date))

for(i in 1:nrow(data2)){
  if(data2$day[i] == "Saturday" || data2$day[i] == "Sunday")
    data2$day[i] = "Weekend"
  else
    data2$day[i] = "Weekday"
}
head(data2)
```

```
##       steps       date interval     day
## 1 1.7169811 2012-10-01        0 Weekday
## 2 0.3396226 2012-10-01        5 Weekday
## 3 0.1320755 2012-10-01       10 Weekday
## 4 0.1509434 2012-10-01       15 Weekday
## 5 0.0754717 2012-10-01       20 Weekday
## 6 2.0943396 2012-10-01       25 Weekday
```

**Creating panel plots**

```r
par(mfrow=c(1,2),mar = c(4,4,4,4))

weekday<- data2 %>% filter(day == "Weekday")
weekend<- data2 %>% filter(day == "Weekend")

avg_weekday = weekday %>% group_by(interval) %>% summarise_at(vars(steps),funs(Average = mean(.,na.rm = T)))

plot(avg_weekday$interval,avg_weekday$Average,xlab="Interval",type="l", 
     ylab="Average number of Steps",main="Weekdays")


avg_weekend = weekend %>% group_by(interval) %>% summarise_at(vars(steps),funs(Average = mean(.,na.rm = T)))

plot(avg_weekend$interval,avg_weekend$Average,xlab="Interval",type="l", 
     ylab="Average number of Steps",main="Weekends")
```

![plot of chunk unnamed-chunk-13](figure/unnamed-chunk-13-1.png)

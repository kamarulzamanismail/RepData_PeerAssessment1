## Loading and preprocessing the data
setwd("D:/Activity")
getwd()
activity_data <- read.csv("activity.csv")
activity_data$date <- as.Date(activity_data$date, "%Y-%m-%d")
activity_data <- as.data.frame(activity_data)


## What is mean total number of steps taken per day?
steps <- with(activity_data, tapply(steps, date, sum, na.rm = TRUE))
hist(steps, main = ("Total Steps By Day"), col = "green", xlab = "Number of Steps")

## mean(steps)
## [1] 9354.23
## median(steps)
## [1] 10395

## What is the average daily activity pattern?
day_avg <- with(na.omit(activity_data), tapply(steps, interval, mean))

## head(day_avg)
##        0         5        10        15        20        25 
## 1.7169811 0.3396226 0.1320755 0.1509434 0.0754717 2.0943396 

plot(day_avg, x = rownames(day_avg), type = "l", col = "blue", xlab = "5 Minute Interval", ylab = "Average Number of Steps", main = "Average Number Of Steps Taken")

## rownames(day_avg)[which.max(day_avg)]
## [1] "835"

## Imputing missing values
## sum(is.na(activity_data))
## [1] 2304

## Mean of the 5 minute interval used to replace the missing values in the dataset. 'activity_data_imputed' is a new dataset with filled the missing data, in exchange to the original one.

activity_data_imputed <- activity_data

for (i in 1:nrow(activity_data_imputed)) {
     if (is.na(activity_data_imputed$steps[i])) {
         activity_data_imputed$steps[i] <- day_avg[rownames(day_avg) == 
             activity_data_imputed$interval[i]]
     }
 }
 
 Steps_PerDay <- tapply(activity_data_imputed$steps, activity_data_imputed$date, sum, na.rm = TRUE)

hist(Steps_PerDay, xlab = "Steps", main = "Number of Steps Taken Each Day", col = "cyan")

## mean(Steps_PerDay)
## [1] 10766.19

## median(Steps_PerDay)
## [1] 10766.19

## Are there differences in activity patterns between weekdays and weekends?

library(ggplot2)
activity_data_imputed$dateType <-  ifelse(as.POSIXlt(activity_data_imputed$date)$wday %in% c(0,6), 'weekend', 'weekday')

Activity_data_imputed_avg <- aggregate(steps ~ interval + dateType, data=activity_data_imputed, mean)

ggplot(Activity_data_imputed_avg, aes(interval, steps)) +
 geom_line() +
 facet_grid(dateType ~ .) +
 xlab("interval") +
 ylab("number of steps")
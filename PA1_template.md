Reproducible Research: Peer Assessment 1
========================================

This summary shows various analyses of a dataset that contains the
number of steps taken by a single user who's wearing a tracking device.
Steps are measured every 5 minutes, over a 2 month period.

Loading and preprocessing the data
----------------------------------

This step:  
- loads the ggplot2 package  
- reads in the dataset  
- converts the date field into a date format

    library(ggplot2)
    data <- read.csv("activity.csv")
    data$date <- as.Date(data$date)

What is mean total number of steps taken per day?
-------------------------------------------------

Here is a histogram showing the frequency of how many steps are taken in
a day.

    histdata <- aggregate(data$steps, list(date = data$date), sum)
    colnames(histdata)[2] <- "steps"
    hist(histdata$steps, breaks = 20, main = "Steps Per Day", xlab = "Steps", col = "green")

![](figure/unnamed-chunk-3-1.png)

The mean and median number of steps taken in a day is:

    mean(histdata$steps, na.rm=TRUE)

    ## [1] 10766.19

    median(histdata$steps, na.rm=TRUE)

    ## [1] 10765

What is the average daily activity pattern?
-------------------------------------------

Here is a time series plot showing the average number of steps taken in
each 5 minute interval throughout the day:

    timedata <- aggregate(data$steps, list(Interval = data$interval), mean, na.rm=TRUE)
    colnames(timedata)[2] <- "steps"
    plot(timedata, type="l", main = "Steps Taken Throughout Day", ylab = "Steps")

![](figure/unnamed-chunk-5-1.png)

The 5 minute interval with the highest average of steps taken throughout
the day (and the mean number of steps in that interval) is:

    timedata[which.max(timedata$steps),]

    ##     Interval    steps
    ## 104      835 206.1698

Imputing missing values
-----------------------

The number of observations in the data are:

    nrow(data)

    ## [1] 17568

The number of observations that don't have a step count are:

    sum(is.na(data))

    ## [1] 2304

This step creates a 2nd dataset where each of the missing step counts
are imputed with the average number of steps in that interval.

    data2 <- data
    data2$steps[is.na(data2$steps)] = timedata$steps

Here is a histogram showing the frequency of steps taken in a day (same
as previous histogram, but with missing values imputed):

    histdata2 <- aggregate(data2$steps, list(date = data$date), sum)
    colnames(histdata2)[2] <- "steps"
    hist(histdata2$steps, breaks = 20, main = "Steps Per Day", xlab = "Steps", col = "green")

![](figure/unnamed-chunk-10-1.png)

And the mean and median of the step count per day (using the dataset
with imputed data) are:

    mean(histdata2$steps)

    ## [1] 10766.19

    median(histdata2$steps)

    ## [1] 10766.19

Are there differences in activity patterns between weekdays and weekends?
-------------------------------------------------------------------------

We first need to create a new variable in the dataset that indicates if
a date is a weekday or weekend.

    data2$weekday <- weekdays(data2$date)
    data2$daytype <- ifelse(data2$weekday %in% c("Saturday","Sunday"),"Weekend", "Weekday")

Here is a panel plot that shows the average step count in each 5 minute
interval throughout the day, separating weekday and weekend data in
separate panels.

    timedata2 <- aggregate(data2$steps, list(data2$interval, data2$daytype), mean)
    colnames(timedata2) <- c("interval", "daytype","steps")
    plot <- ggplot(data=timedata2, aes(x=interval, y=steps, group=daytype, color=daytype)) 
    plot + geom_line() + facet_grid(daytype~.)

![](figure/unnamed-chunk-13-1.png)

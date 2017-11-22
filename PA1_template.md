================================================================

R Markdown
----------

Downloading the zipfile and unzip
=================================

### Reading the Source file

    ## Warning: package 'data.table' was built under R version 3.4.2

<b>Reading File</b>

    library("data.table")
    new_activity <- data.table::fread(input = "./repduce_p1/activity.csv")

<b>Calculate the total number of steps taken per day</b>

    Total_Steps <- new_activity[, c(lapply(.SD, sum,na.rm = FALSE)), .SDcols = c("steps"), by = .(date)] 

<b>Make a histogram of the total number of steps taken each day</b>

    hist(Total_Steps$steps ,xlab="Steps",main="Histogram of total number of steps taken per day",col="yellow")

![](Course1_files/figure-markdown_strict/histogram_plot-1.png)

<b>Calculation of mean and median</b>

    Total_Steps[, .(Mean_Steps = mean(steps, na.rm = TRUE), Median_Steps = median(steps, na.rm = TRUE))]

    ##    Mean_Steps Median_Steps
    ## 1:   10766.19        10765

<b>Time Series Plot</b>

    IntervalDT <- new_activity[, c(lapply(.SD, mean, na.rm = TRUE)), .SDcols = c("steps"), by = .(interval)] 

    plot(IntervalDT$interval,IntervalDT$steps ,type = "l",xlab="INTERVAL",ylab="AVERAGE STEPS",main="Average number of Steps per interval",col="red")

![](Course1_files/figure-markdown_strict/time_series-1.png)

<b>Which 5-minute interval, on average across all the days in the
dataset, contains the maximum number of steps?</b>

    IntervalDT[steps == max(steps), .(max_interval = interval)]

    ##    max_interval
    ## 1:          835

<b>Calculate and report the total number of missing values in the
dataset</b>

    nrow(new_activity[is.na(steps), ])

    ## [1] 2304

<p1> Devise a strategy for filling in all of the missing values in the
dataset. The strategy does not need to be sophisticated. For example,
you could use the mean/median for that day, or the mean for that
5-minute interval, etc.</p1>

    new_activity[is.na(steps), "steps"] <- new_activity[, c(lapply(.SD, median, na.rm = TRUE)),  .SDcols = c("steps")]

<b>Create a new dataset that is equal to the original dataset but with
the missing data filled in.</b>

    data.table::fwrite(x = new_activity, file = "./repduce_p1/tidyData.csv", quote = FALSE)

<b>Total number of steps taken per day</b>

    Total_Steps <- new_activity[, c(lapply(.SD, sum)), .SDcols = c("steps"), by = .(date)] 

<b> mean and median total number of steps taken per day</b>

    Total_Steps[, .(Mean_Steps = mean(steps), Median_Steps = median(steps))]

    ##    Mean_Steps Median_Steps
    ## 1:    9354.23        10395

<b>Plot to setup total number of steps</b>

    library(ggplot2)

    ## Warning: package 'ggplot2' was built under R version 3.4.2

    ggplot(Total_Steps, aes(x = steps)) + geom_histogram(fill = "blue") + labs(title = "Daily Steps", x = "Steps", y = "Frequency")

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](Course1_files/figure-markdown_strict/total_steps_plot-1.png)

<b>new factor variable in the dataset with two levels - "weekday" and
"weekend"</b>

    new_activity <- data.table::fread(input = "./repduce_p1/activity.csv")
    new_activity[, date := as.POSIXct(date, format = "%Y-%m-%d")]
    new_activity[, `Day of Week`:= weekdays(x = date)]
    new_activity[grepl(pattern = "Monday|Tuesday|Wednesday|Thursday|Friday", x = `Day of Week`), "weekday or weekend"] <- "weekday"
    new_activity[grepl(pattern = "Saturday|Sunday", x = `Day of Week`), "weekday or weekend"] <- "weekend"
    new_activity[, `weekday or weekend` := as.factor(`weekday or weekend`)]
    head(new_activity,10)

    ##     steps       date interval Day of Week weekday or weekend
    ##  1:    NA 2012-10-01        0      Monday            weekday
    ##  2:    NA 2012-10-01        5      Monday            weekday
    ##  3:    NA 2012-10-01       10      Monday            weekday
    ##  4:    NA 2012-10-01       15      Monday            weekday
    ##  5:    NA 2012-10-01       20      Monday            weekday
    ##  6:    NA 2012-10-01       25      Monday            weekday
    ##  7:    NA 2012-10-01       30      Monday            weekday
    ##  8:    NA 2012-10-01       35      Monday            weekday
    ##  9:    NA 2012-10-01       40      Monday            weekday
    ## 10:    NA 2012-10-01       45      Monday            weekday

<b>Time series plot</b>

    new_activity[is.na(steps), "steps"] <- new_activity[, c(lapply(.SD, median, na.rm = TRUE)), .SDcols = c("steps")]
    IntervalDT <- new_activity[, c(lapply(.SD, mean, na.rm = TRUE)), .SDcols = c("steps"), by = .(interval, `weekday or weekend`)] 

    ggplot(IntervalDT , aes(x = interval , y = steps, color=`weekday or weekend`)) + geom_line() + labs(title = "Avg. Daily Steps by Weektype", x = "Interval", y = "No. of Steps") + facet_wrap(~`weekday or weekend` , ncol = 1, nrow=2)

![](Course1_files/figure-markdown_strict/time_series_plot_weekday_weekend-1.png)

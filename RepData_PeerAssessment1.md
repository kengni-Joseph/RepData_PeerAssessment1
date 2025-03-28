RepData\_PeerAssessment1
================

## GitHub Documents

This is an R Markdown format used for publishing markdown documents to
GitHub. When you click the **Knit** button all R code chunks are run and
a markdown file (.md) suitable for publishing to GitHub is generated.

## Including Code

You can include R code in the document as follows:

``` r
library(readr)
activity <- read_csv("Reproducible Research/week2/activity.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   steps = col_double(),
    ##   date = col_date(format = ""),
    ##   interval = col_double()
    ## )

``` r
#View(activity)
```

## preprocessing the data

``` r
# Check for missing values
sum(is.na(activity$steps))
```

    ## [1] 2304

``` r
# Replace missing values if necessary (using mean for the interval)
activity_clean <- activity
activity_clean$steps[is.na(activity_clean$steps)] <- 
  ave(activity_clean$steps, activity_clean$interval, FUN = function(x) mean(x, na.rm = TRUE))
```

    ## Warning in activity_clean$steps[is.na(activity_clean$steps)] <-
    ## ave(activity_clean$steps, : number of items to replace is not a multiple of
    ## replacement length

## calculating total steps per day

``` r
total_steps_per_day <- aggregate(steps ~ date, data = activity_clean, sum)
```

## Including Plots

You can also embed plots, for example:

``` r
hist(total_steps_per_day$steps, 
     main = "Total Steps per Day", 
     xlab = "Steps", 
     col = "blue")
```

![](RepData_PeerAssessment1_files/figure-gfm/Hactivity_clean-1.png)<!-- -->
\#\# calculate mean and median steps per day

``` r
mean_steps <- mean(total_steps_per_day$steps)
median_steps <- median(total_steps_per_day$steps)
```

## average daily activity pattern

``` r
avg_steps_interval <- aggregate(steps ~ interval, data = activity_clean, mean)

# Plot the time series
plot(avg_steps_interval$interval, avg_steps_interval$steps, type = "l", 
     main = "Average Daily Activity Pattern", xlab = "Interval", ylab = "Average Steps")
```

![](RepData_PeerAssessment1_files/figure-gfm/activity_clean-1.png)<!-- -->
\#\# Differences Between Weekdays and Weekends

``` r
activity_clean$day_type <- ifelse(weekdays(as.Date(activity_clean$date)) %in% c("Saturday", "Sunday"),   "Weekend", "Weekday")
```

## Analyze activity patterns for weekdays vs. weekends

``` r
library(ggplot2)
avg_steps_day_type <- aggregate(steps ~ interval + day_type, data = activity_clean, mean)

# Plot using ggplot2
ggplot(avg_steps_day_type, aes(x = interval, y = steps, color = day_type)) +
  geom_line() +
  labs(title = "Activity Patterns: Weekdays vs Weekends", x = "Interval", y = "Average Steps") +
  theme_minimal()
```

![](RepData_PeerAssessment1_files/figure-gfm/Wactivity_clean-1.png)<!-- -->
Note that the `echo = FALSE` parameter was added to the code chunk to
prevent printing of the R code that generated the plot.

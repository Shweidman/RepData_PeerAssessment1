Reproducible Research - Peer Assessment 1
========================================================

### 1. Loading and processing the data


#### 1.1. Set working directory

```{r}
setwd("C:/Users/weidse51/Documents/Coursera/Data Science/Reproducible Research/Peer Assessment 1/")
```

#### 1.2. Read in the activity data

```{r}
activity <- read.csv("activity.csv",stringsAsFactors=FALSE)
```

#### 1.3. Create a factor variable called "day" for dates

```{r}
activity$day <- as.factor(activity$date)
```

#### 1.4. Change date variable to class POSIXlt

```{r}
activity$date<-strptime(activity$date,"%Y-%m-%d")
```

#### 1.5. Add "day of week" variable to activity dataset, as a factor variable

```{r}
activity$weekday <- as.factor(weekdays((activity$date)))
```

#### 1.6. Create a version of the activity dataset called "activityData" instead of "activity" that excludes the NAs, eliminate factor levels for days that had all NA values

```{r}
activityData <- activity[!is.na(activity$steps),]
activityData$day <- factor(activityData$day)
```

## 2. What is the mean number of steps taken per day?

#### 2.1. Plot a histogram of the total number of steps taken each day, ignoring NAs

```{r}
numIntervals <- length(levels(as.factor(activity$interval)))
activitySplit <- split(activity,activity$day)
dayMeans <- sapply(activitySplit, function(x) mean(x$steps)*numIntervals)
hist(dayMeans,
     main = "Histogram of total steps taken per day",
     xlab = "Steps per day")
```

#### 2.2. Calculate the mean and median number of steps taken each day:

```{r}
mean(dayMeans,na.rm=TRUE)
median(dayMeans,na.rm=TRUE)
```

## 3. What is the average daily activity pattern?

#### 3.1. Average the number of steps taken by interval, plot a histogram, ignoring NAs

```{r}
activitySplit = split(activityData,activityData$interval)
intervalMeans <- sapply(activitySplit, function(x) mean(x$steps))
plot(intervalMeans,
     type="l",
     main = "Steps taken by time of day",
     xlab = "Time of day",
     ylab = "Steps")
```

#### 3.2. Find the five minute interval with the maximum number of steps

```{r}
names(which.max(intervalMeans))
```

## 4. Imputing missing values

#### 4.1. Get the number of missing values

```{r}
nrow(activity) - nrow(activityData)
```

#### 4.2. Impute the missing values as the mean number of steps taken in that interval 
```{r}
for (i in 1:nrow(activity)) {
  if (is.na(activity$steps[i])) {
    interval <- as.character(activity$interval[i])
    activity$steps[i] <- intervalMeans[[interval]]
  }
}
```

#### 4.3. Make a new histogram of the total of steps taken each day

```{r}
numIntervals <- length(levels(as.factor(activity$interval)))
activitySplit <- split(activity,activity$day)
dayMeans <- sapply(activitySplit, function(x) mean(x$steps)*numIntervals)
hist(dayMeans,
     main = "Histogram of total steps taken per day",
     xlab = "Steps per day")
```

#### 4.4.Find the new mean and median values by day 

```{r}
mean(dayMeans,na.rm=TRUE)
median(dayMeans,na.rm=TRUE)
```

## 5. Are there differences in activity patterns between weekdays and weekends?

#### 5.1. Create the weekday and weekend variables

```{r}
for (i in 1:nrow(activity)) {
  if (activity$weekday[i]=="Monday"|activity$weekday[i]=="Tuesday"|activity$weekday[i]=="Wednesday"|activity$weekday[i]=="Thursday"|activity$weekday[i]=="Friday") {
    activity$dayType[i] <- "Weekday"
  }
  if (activity$weekday[i]=="Saturday"|activity$weekday[i]=="Sunday")
   {
    activity$dayType[i] <- "Weekend"
  }
}
```

#### 5.2. Create weekend and weekday specific datasets.

```{r}
activityWeekday = activity[activity$dayType=="Weekday",]
activityWeekend = activity[activity$dayType=="Weekend",]
```

#### 5.3. Calculate the means for each time interval, separated for weekdays and weekends.  

```{r}
activityWeekdaySplit = split(activityWeekday,activityWeekday$interval)
intervalWeekdayMeans <- sapply(activityWeekdaySplit, function(x) mean(x$steps))
activityWeekendSplit = split(activityWeekend,activityWeekend$interval)
intervalWeekendMeans <- sapply(activityWeekendSplit, function(x) mean(x$steps))
```

#### 5.4. Create the plots of steps taken by interval for weekdays and weekends. The y-axis has been adjusted so both plots are on the same scale.

```{r}
par("mar"=c(4,2,2,1))
par(mfrow=c(2,1))
plot(intervalWeekdayMeans,type="l",
     ylim=c(0,max(max(intervalWeekdayMeans),max(intervalWeekendMeans))),
     main = "Steps taken by time of day, on weekdays",
     xlab = "Time of day",
     ylab = "Steps")
plot(intervalWeekendMeans,type="l",
     ylim=c(0,max(max(intervalWeekdayMeans),max(intervalWeekendMeans))),
     main = "Steps taken by time of day, on weekdays",
     xlab = "Time of day",
     ylab = "Steps")
```

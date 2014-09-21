---
title: "CodeBook"
output: html_document
---

Variable list and descriptions
------------------------------

Variable name    | Description
-----------------|------------
subject          | ID the subject who performed the activity for each window sample. Its range is from 1 to 30.
activity         | Activity name
featDomain       | Feature: Time domain signal or frequency domain signal (Time or Freq)
featInstrument   | Feature: Measuring instrument (Accelerometer or Gyroscope)
featAcceleration | Feature: Acceleration signal (Body or Gravity)
featVariable     | Feature: Variable (Mean or SD)
featJerk         | Feature: Jerk signal
featMagnitude    | Feature: Magnitude of the signals calculated using the Euclidean norm
featAxis         | Feature: 3-axial signals in the X, Y and Z directions (X, Y, or Z)
featCount        | Feature: Count of data points used to compute `average`
featAverage      | Feature: Average of each variable for each activity and each subject

DataSet Structure

```r
str(DataTidy)
```

```
## Classes 'grouped_df', 'tbl_df', 'tbl' and 'data.frame':	11880 obs. of  11 variables:
##  $ subject         : int  1 1 1 1 1 1 1 1 1 1 ...
##  $ activity        : Factor w/ 6 levels "LAYING","SITTING",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ featDomain      : Factor w/ 2 levels "Time","Freq": 1 1 1 1 1 1 1 1 1 1 ...
##  $ featAcceleration: Factor w/ 3 levels NA,"Body","Gravity": 1 1 1 1 1 1 1 1 1 1 ...
##  $ featInstrument  : Factor w/ 2 levels "Accelerometer",..: 2 2 2 2 2 2 2 2 2 2 ...
##  $ featJerk        : Factor w/ 2 levels NA,"Jerk": 1 1 1 1 1 1 1 1 2 2 ...
##  $ featMagnitude   : Factor w/ 2 levels NA,"Magnitude": 1 1 1 1 1 1 2 2 1 1 ...
##  $ featVariable    : Factor w/ 2 levels "Mean","SD": 1 1 1 2 2 2 1 2 1 1 ...
##  $ featAxis        : Factor w/ 4 levels NA,"X","Y","Z": 2 3 4 2 3 4 1 1 2 3 ...
##  $ count           : int  50 50 50 50 50 50 50 50 50 50 ...
##  $ average         : num  -0.0166 -0.0645 0.1487 -0.8735 -0.9511 ...
##  - attr(*, "vars")=List of 8
##   ..$ : symbol subject
##   ..$ : symbol activity
##   ..$ : symbol featDomain
##   ..$ : symbol featAcceleration
##   ..$ : symbol featInstrument
##   ..$ : symbol featJerk
##   ..$ : symbol featMagnitude
##   ..$ : symbol featVariable
##  - attr(*, "drop")= logi TRUE
```
List variables in Data  

```r
colnames(DataTidy)
```

```
##  [1] "subject"          "activity"         "featDomain"      
##  [4] "featAcceleration" "featInstrument"   "featJerk"        
##  [7] "featMagnitude"    "featVariable"     "featAxis"        
## [10] "count"            "average"
```
Show a few rows of dataset

```r
DataTidy
```

```
## Source: local data frame [11,880 x 11]
## Groups: subject, activity, featDomain, featAcceleration, featInstrument, featJerk, featMagnitude, featVariable
## 
##    subject activity featDomain featAcceleration featInstrument featJerk
## 1        1   LAYING       Time               NA      Gyroscope       NA
## 2        1   LAYING       Time               NA      Gyroscope       NA
## 3        1   LAYING       Time               NA      Gyroscope       NA
## 4        1   LAYING       Time               NA      Gyroscope       NA
## 5        1   LAYING       Time               NA      Gyroscope       NA
## 6        1   LAYING       Time               NA      Gyroscope       NA
## 7        1   LAYING       Time               NA      Gyroscope       NA
## 8        1   LAYING       Time               NA      Gyroscope       NA
## 9        1   LAYING       Time               NA      Gyroscope     Jerk
## 10       1   LAYING       Time               NA      Gyroscope     Jerk
## ..     ...      ...        ...              ...            ...      ...
## Variables not shown: featMagnitude (fctr), featVariable (fctr), featAxis
##   (fctr), count (int), average (dbl)
```
Summary of Variables

```r
summary(DataTidy)
```

```
##     subject                   activity    featDomain  featAcceleration
##  Min.   : 1.0   LAYING            :1980   Time:7200   NA     :4680    
##  1st Qu.: 8.0   SITTING           :1980   Freq:4680   Body   :5760    
##  Median :15.5   STANDING          :1980               Gravity:1440    
##  Mean   :15.5   WALKING           :1980                               
##  3rd Qu.:23.0   WALKING_DOWNSTAIRS:1980                               
##  Max.   :30.0   WALKING_UPSTAIRS  :1980                               
##        featInstrument featJerk      featMagnitude  featVariable featAxis 
##  Accelerometer:7200   NA  :7200   NA       :8640   Mean:5940    NA:3240  
##  Gyroscope    :4680   Jerk:4680   Magnitude:3240   SD  :5940    X :2880  
##                                                                 Y :2880  
##                                                                 Z :2880  
##                                                                          
##                                                                          
##      count         average       
##  Min.   :36.0   Min.   :-0.9977  
##  1st Qu.:49.0   1st Qu.:-0.9621  
##  Median :54.5   Median :-0.4699  
##  Mean   :57.2   Mean   :-0.4844  
##  3rd Qu.:63.2   3rd Qu.:-0.0784  
##  Max.   :95.0   Max.   : 0.9745
```

Save to File

```r
f <- file.path(path, "result.txt")
write.table(DataTidy,f, row.name=FALSE)
```

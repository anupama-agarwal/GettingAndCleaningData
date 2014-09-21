---
title: "Analysis"
output: html_document
---
Instructions For Project

The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. You will be graded by your peers on a series of yes/no questions related to the project. You will be required to submit: 1) a tidy data set as described below, 2) a link to a Github repository with your script for performing the analysis, and 3) a code book that describes the variables, the data, and any transformations or work that you performed to clean up the data called CodeBook.md. You should also include a README.md in the repo with your scripts. This repo explains how all of the scripts work and how they are connected.

    One of the most exciting areas in all of data science right now is wearable computing - see for example this article . Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site where the data was obtained:

    http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

    Here are the data for the project:

    https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

    You should create one R script called run_analysis.R that does the following.

        DONE Merges the training and the test sets to create one data set.
        DONE Extracts only the measurements on the mean and standard deviation for each measurement.
        DONE Uses descriptive activity names to name the activities in the data set.
        DONE Appropriately labels the data set with descriptive activity names.
        DONE Creates a second, independent tidy data set with the average of each variable for each activity and each subject.

    Good luck!

Preliminaries

```r
library(dplyr)
library(tidyr)
library(reshape2)
require(knitr)
require(markdown)
path <- getwd()
path
```

```
## [1] "/Users/anupama.agarwal"
```

Get the Data

Download the file

```r
url <- "http://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
zip_file <- "Dataset.zip"
download.file(url, file.path(path, zip_file))
```
Unzip the file

```r
system(paste("tar -xzvf", file.path(path, zip_file)))
```
Files are unzipped in folder UCI HAR Dataset

```r
input_path <- file.path(path, "UCI HAR Dataset")
list.files(input_path, recursive=TRUE)
```

```
##  [1] "activity_labels.txt"                         
##  [2] "features_info.txt"                           
##  [3] "features.txt"                                
##  [4] "README.txt"                                  
##  [5] "test/Inertial Signals/body_acc_x_test.txt"   
##  [6] "test/Inertial Signals/body_acc_y_test.txt"   
##  [7] "test/Inertial Signals/body_acc_z_test.txt"   
##  [8] "test/Inertial Signals/body_gyro_x_test.txt"  
##  [9] "test/Inertial Signals/body_gyro_y_test.txt"  
## [10] "test/Inertial Signals/body_gyro_z_test.txt"  
## [11] "test/Inertial Signals/total_acc_x_test.txt"  
## [12] "test/Inertial Signals/total_acc_y_test.txt"  
## [13] "test/Inertial Signals/total_acc_z_test.txt"  
## [14] "test/subject_test.txt"                       
## [15] "test/X_test.txt"                             
## [16] "test/y_test.txt"                             
## [17] "train/Inertial Signals/body_acc_x_train.txt" 
## [18] "train/Inertial Signals/body_acc_y_train.txt" 
## [19] "train/Inertial Signals/body_acc_z_train.txt" 
## [20] "train/Inertial Signals/body_gyro_x_train.txt"
## [21] "train/Inertial Signals/body_gyro_y_train.txt"
## [22] "train/Inertial Signals/body_gyro_z_train.txt"
## [23] "train/Inertial Signals/total_acc_x_train.txt"
## [24] "train/Inertial Signals/total_acc_y_train.txt"
## [25] "train/Inertial Signals/total_acc_z_train.txt"
## [26] "train/subject_train.txt"                     
## [27] "train/X_train.txt"                           
## [28] "train/y_train.txt"
```

We can ignore Inertia Signals Folder for this project.

Read Files
Reading Subject Files

```r
SubjectTrain <- read.csv(file.path(input_path,"train","subject_train.txt"),header=FALSE)
SubjectTest <- read.csv(file.path(input_path, "test", "subject_test.txt"),header=FALSE)
```
Reading Activity Files

```r
ActivityTrain <- read.csv(file.path(input_path, "train", "Y_train.txt"),header=FALSE)
ActivityTest <- read.csv(file.path(input_path, "test", "Y_test.txt"),header=FALSE)
```
Reading Data Files

```r
dataTrain <- as.data.frame(read.table(file.path(input_path, "train", "X_train.txt")))
dataTest <- as.data.frame(read.table(file.path(input_path, "test", "X_test.txt")))
```

Merge Training and Test Data Set
merge rows

```r
colnames(SubjectTrain) <- c("subject")
colnames(SubjectTest) <- c("subject")
Subject <- rbind(SubjectTrain, SubjectTest)

colnames(ActivityTrain) <- c("activityNum")
colnames(ActivityTest) <- c("activityNum")
Activity <- rbind(ActivityTrain, ActivityTest)
data <- rbind(dataTrain, dataTest)
```

Merge Columns

```r
Subject <- cbind(Subject, Activity)
data <- cbind(data, Subject)
data_df <- tbl_df(data)
data_df <- data_df %>% arrange(subject, activityNum)
```

Extracts only the measurements on the mean and standard deviation for each measurement. 

read features.txt file. It will give list of features.

```r
features <- as.data.frame(read.table(file.path(input_path,"features.txt")))
colnames(features) <- c("featureNum","featureName")
features_df <- tbl_df(features)
```
subset only measurements for mean and standard deviation

```r
features_df <- features_df %>% filter(grepl("mean\\(\\)|std\\(\\)", featureName))
```
converting featureNum to feature Code

```r
features_df <- features_df %>% mutate(featureCode=paste0("V",featureNum))
head(features_df)
```

```
## Source: local data frame [6 x 3]
## 
##   featureNum       featureName featureCode
## 1          1 tBodyAcc-mean()-X          V1
## 2          2 tBodyAcc-mean()-Y          V2
## 3          3 tBodyAcc-mean()-Z          V3
## 4          4  tBodyAcc-std()-X          V4
## 5          5  tBodyAcc-std()-Y          V5
## 6          6  tBodyAcc-std()-Z          V6
```
selecting these columns from data

```r
data_df <- data_df %>% select(subject,activityNum, match(features_df$featureCode, names(data_df)))
```

Use Descriptive activity names

```r
ActivityNames <- as.data.frame(read.table(file.path(input_path, "activity_labels.txt")))
colnames(ActivityNames) <- c("activityNum","activityName")
```

Label with Descriptive Activity Names

```r
data_df <- merge(data_df, ActivityNames, by="activityNum",all.x=TRUE)
#melt table to reshape it from short-wide format
data_df <- tbl_df(melt(data_df, c("subject","activityNum","activityName"), variable.name="featureCode"))
#merge feature name
data_df <- merge(data_df, features_df, by="featureCode",all.x=TRUE)
head(data_df)
```

```
##   featureCode subject activityNum activityName  value featureNum
## 1          V1       1           1      WALKING 0.2820          1
## 2          V1       1           1      WALKING 0.2558          1
## 3          V1       1           1      WALKING 0.2549          1
## 4          V1       1           1      WALKING 0.3434          1
## 5          V1       1           1      WALKING 0.2762          1
## 6          V1       1           1      WALKING 0.2555          1
##         featureName
## 1 tBodyAcc-mean()-X
## 2 tBodyAcc-mean()-X
## 3 tBodyAcc-mean()-X
## 4 tBodyAcc-mean()-X
## 5 tBodyAcc-mean()-X
## 6 tBodyAcc-mean()-X
```

```r
#adding activity and feature as factor variables
data_df <- data_df %>% mutate(activity=factor(activityName), feature=factor(featureName))
levels(data_df$feature)
```

```
##  [1] "fBodyAcc-mean()-X"           "fBodyAcc-mean()-Y"          
##  [3] "fBodyAcc-mean()-Z"           "fBodyAcc-std()-X"           
##  [5] "fBodyAcc-std()-Y"            "fBodyAcc-std()-Z"           
##  [7] "fBodyAccJerk-mean()-X"       "fBodyAccJerk-mean()-Y"      
##  [9] "fBodyAccJerk-mean()-Z"       "fBodyAccJerk-std()-X"       
## [11] "fBodyAccJerk-std()-Y"        "fBodyAccJerk-std()-Z"       
## [13] "fBodyAccMag-mean()"          "fBodyAccMag-std()"          
## [15] "fBodyBodyAccJerkMag-mean()"  "fBodyBodyAccJerkMag-std()"  
## [17] "fBodyBodyGyroJerkMag-mean()" "fBodyBodyGyroJerkMag-std()" 
## [19] "fBodyBodyGyroMag-mean()"     "fBodyBodyGyroMag-std()"     
## [21] "fBodyGyro-mean()-X"          "fBodyGyro-mean()-Y"         
## [23] "fBodyGyro-mean()-Z"          "fBodyGyro-std()-X"          
## [25] "fBodyGyro-std()-Y"           "fBodyGyro-std()-Z"          
## [27] "tBodyAcc-mean()-X"           "tBodyAcc-mean()-Y"          
## [29] "tBodyAcc-mean()-Z"           "tBodyAcc-std()-X"           
## [31] "tBodyAcc-std()-Y"            "tBodyAcc-std()-Z"           
## [33] "tBodyAccJerk-mean()-X"       "tBodyAccJerk-mean()-Y"      
## [35] "tBodyAccJerk-mean()-Z"       "tBodyAccJerk-std()-X"       
## [37] "tBodyAccJerk-std()-Y"        "tBodyAccJerk-std()-Z"       
## [39] "tBodyAccJerkMag-mean()"      "tBodyAccJerkMag-std()"      
## [41] "tBodyAccMag-mean()"          "tBodyAccMag-std()"          
## [43] "tBodyGyro-mean()-X"          "tBodyGyro-mean()-Y"         
## [45] "tBodyGyro-mean()-Z"          "tBodyGyro-std()-X"          
## [47] "tBodyGyro-std()-Y"           "tBodyGyro-std()-Z"          
## [49] "tBodyGyroJerk-mean()-X"      "tBodyGyroJerk-mean()-Y"     
## [51] "tBodyGyroJerk-mean()-Z"      "tBodyGyroJerk-std()-X"      
## [53] "tBodyGyroJerk-std()-Y"       "tBodyGyroJerk-std()-Z"      
## [55] "tBodyGyroJerkMag-mean()"     "tBodyGyroJerkMag-std()"     
## [57] "tBodyGyroMag-mean()"         "tBodyGyroMag-std()"         
## [59] "tGravityAcc-mean()-X"        "tGravityAcc-mean()-Y"       
## [61] "tGravityAcc-mean()-Z"        "tGravityAcc-std()-X"        
## [63] "tGravityAcc-std()-Y"         "tGravityAcc-std()-Z"        
## [65] "tGravityAccMag-mean()"       "tGravityAccMag-std()"
```
Separate features from feature Names

```r
grepthis <- function (regex) {
  grepl(regex, data_df$feature)
}
#features with 2 categories
n <-2 
y <- matrix(seq(1, n), nrow=n)
x <- matrix(c(grepthis("^t"), grepthis("^f")), ncol=nrow(y))
data_df$featDomain <- factor(x %*% y, labels=c("Time", "Freq"))
x <- matrix(c(grepthis("Acc"), grepthis("Gyro")), ncol=nrow(y))
data_df$featInstrument <- factor(x %*% y, labels=c("Accelerometer", "Gyroscope"))
x <- matrix(c(grepthis("BodyAcc"), grepthis("GravityAcc")), ncol=nrow(y))
data_df$featAcceleration <- factor(x %*% y, labels=c(NA, "Body", "Gravity"))
x <- matrix(c(grepthis("mean()"), grepthis("std()")), ncol=nrow(y))
data_df$featVariable <- factor(x %*% y, labels=c("Mean", "SD"))
## Features with 1 category
data_df$featJerk <- factor(grepthis("Jerk"), labels=c(NA, "Jerk"))
data_df$featMagnitude <- factor(grepthis("Mag"), labels=c(NA, "Magnitude"))
## Features with 3 categories
n <- 3
y <- matrix(seq(1, n), nrow=n)
x <- matrix(c(grepthis("-X"), grepthis("-Y"), grepthis("-Z")), ncol=nrow(y))
data_df$featAxis <- factor(x %*% y, labels=c(NA, "X", "Y", "Z"))
head(data_df)
```

```
##   featureCode subject activityNum activityName  value featureNum
## 1          V1       1           1      WALKING 0.2820          1
## 2          V1       1           1      WALKING 0.2558          1
## 3          V1       1           1      WALKING 0.2549          1
## 4          V1       1           1      WALKING 0.3434          1
## 5          V1       1           1      WALKING 0.2762          1
## 6          V1       1           1      WALKING 0.2555          1
##         featureName activity           feature featDomain featInstrument
## 1 tBodyAcc-mean()-X  WALKING tBodyAcc-mean()-X       Time  Accelerometer
## 2 tBodyAcc-mean()-X  WALKING tBodyAcc-mean()-X       Time  Accelerometer
## 3 tBodyAcc-mean()-X  WALKING tBodyAcc-mean()-X       Time  Accelerometer
## 4 tBodyAcc-mean()-X  WALKING tBodyAcc-mean()-X       Time  Accelerometer
## 5 tBodyAcc-mean()-X  WALKING tBodyAcc-mean()-X       Time  Accelerometer
## 6 tBodyAcc-mean()-X  WALKING tBodyAcc-mean()-X       Time  Accelerometer
##   featAcceleration featVariable featJerk featMagnitude featAxis
## 1             Body         Mean     <NA>          <NA>        X
## 2             Body         Mean     <NA>          <NA>        X
## 3             Body         Mean     <NA>          <NA>        X
## 4             Body         Mean     <NA>          <NA>        X
## 5             Body         Mean     <NA>          <NA>        X
## 6             Body         Mean     <NA>          <NA>        X
```

Create a tidy dataset

```r
DataTidy <- data_df %>% group_by( subject, activity, featDomain, featAcceleration, featInstrument, featJerk, featMagnitude, featVariable, featAxis) %>% summarize(count=n(),average=mean(value))
head(DataTidy)
```

```
## Source: local data frame [6 x 11]
## Groups: subject, activity, featDomain, featAcceleration, featInstrument, featJerk, featMagnitude, featVariable
## 
##   subject activity featDomain featAcceleration featInstrument featJerk
## 1       1   LAYING       Time               NA      Gyroscope       NA
## 2       1   LAYING       Time               NA      Gyroscope       NA
## 3       1   LAYING       Time               NA      Gyroscope       NA
## 4       1   LAYING       Time               NA      Gyroscope       NA
## 5       1   LAYING       Time               NA      Gyroscope       NA
## 6       1   LAYING       Time               NA      Gyroscope       NA
## Variables not shown: featMagnitude (fctr), featVariable (fctr), featAxis
##   (fctr), count (int), average (dbl)
```

Creating CodeBook
![plot of chunk unnamed-chunk-18](figure/unnamed-chunk-18.png) 

```
## [1] "codebook.md"
```

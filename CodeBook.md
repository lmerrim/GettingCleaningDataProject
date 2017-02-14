---
Getting and Cleaning Data
Mon Feb 13 22:19:12 2017
#Loading Packages
library(reshape2)

# Downloading Data
if(!file.exists("./data")){dir.create("./data")}
fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileUrl,destfile="./Dataset.zip")

## Unzipping Downloaded File
unzip(zipfile="./Dataset.zip",exdir="./data")

## Getting File from UCI HAR Folder
path_rf <- file.path("./data" , "UCI HAR Dataset")
files<-list.files(path_rf, recursive=TRUE)
files
  [1] "activity_labels.txt"                         
  [2] "features.txt"                                
  [3] "features_info.txt"                           
  [4] "README.txt"                                  
  [5] "test/Inertial Signals/body_acc_x_test.txt"   
  [6] "test/Inertial Signals/body_acc_y_test.txt"   
  [7] "test/Inertial Signals/body_acc_z_test.txt"   
  [8] "test/Inertial Signals/body_gyro_x_test.txt"  
  [9] "test/Inertial Signals/body_gyro_y_test.txt"  
 [10] "test/Inertial Signals/body_gyro_z_test.txt"  
 [11] "test/Inertial Signals/total_acc_x_test.txt"  
 [12] "test/Inertial Signals/total_acc_y_test.txt"  
 [13] "test/Inertial Signals/total_acc_z_test.txt"  
 [14] "test/subject_test.txt"                       
 [15] "test/X_test.txt"                             
 [16] "test/y_test.txt"                             
 [17] "train/Inertial Signals/body_acc_x_train.txt"
 [18] "train/Inertial Signals/body_acc_y_train.txt"
 [19] "train/Inertial Signals/body_acc_z_train.txt"
 [20] "train/Inertial Signals/body_gyro_x_train.txt"
 [21] "train/Inertial Signals/body_gyro_y_train.txt"
 [22] "train/Inertial Signals/body_gyro_z_train.txt"
 [23] "train/Inertial Signals/total_acc_x_train.txt"
 [24] "train/Inertial Signals/total_acc_y_train.txt"
 [25] "train/Inertial Signals/total_acc_z_train.txt"
 [26] "train/subject_train.txt"
 [27] "train/X_train.txt"
 [28] "train/y_train.txt"

# Part 1: Merging Files ####
## Read Activity Files
ActivityTest  <- read.table(file.path(path_rf, "test" , "Y_test.txt" ),
                            header = FALSE)
ActivityTrain <- read.table(file.path(path_rf, "train", "Y_train.txt"),
                            header = FALSE)

## Read Subject Files
SubjectTrain <- read.table(file.path(path_rf, "train", "subject_train.txt"),
                           header = FALSE)
SubjectTest  <- read.table(file.path(path_rf, "test" , "subject_test.txt"),
                           header = FALSE)

## Read Feature Files
FeaturesTest  <- read.table(file.path(path_rf, "test" , "X_test.txt" ),
                            header = FALSE)
FeaturesTrain <- read.table(file.path(path_rf, "train", "X_train.txt"),
                            header = FALSE)

## Examining Datasets 
str(ActivityTest)
 'data.frame':    2947 obs. of  1 variable:
  $ V1: int  5 5 5 5 5 5 5 5 5 5 ...
str(ActivityTrain)
 'data.frame':    7352 obs. of  1 variable:
  $ V1: int  5 5 5 5 5 5 5 5 5 5 ...
str(SubjectTest)
 'data.frame':    2947 obs. of  1 variable:
  $ V1: int  2 2 2 2 2 2 2 2 2 2 ...
str(SubjectTrain)
 'data.frame':    7352 obs. of  1 variable:
  $ V1: int  1 1 1 1 1 1 1 1 1 1 ...
str(FeaturesTest)
 'data.frame':    2947 obs. of  561 variables:
  $ V1  : num  0.257 0.286 0.275 0.27 0.275 ...
  $ V2  : num  -0.0233 -0.0132 -0.0261 -0.0326 -0.0278 ...
  $ V3  : num  -0.0147 -0.1191 -0.1182 -0.1175 -0.1295 ...
  $ V4  : num  -0.938 -0.975 -0.994 -0.995 -0.994 ...
  $ V5  : num  -0.92 -0.967 -0.97 -0.973 -0.967 ...
  $ V6  : num  -0.668 -0.945 -0.963 -0.967 -0.978 ...
  $ V7  : num  -0.953 -0.987 -0.994 -0.995 -0.994 ...
  $ V8  : num  -0.925 -0.968 -0.971 -0.974 -0.966 ...
  $ V9  : num  -0.674 -0.946 -0.963 -0.969 -0.977 ...
  $ V10 : num  -0.894 -0.894 -0.939 -0.939 -0.939 ...
  $ V11 : num  -0.555 -0.555 -0.569 -0.569 -0.561 ...
  $ V12 : num  -0.466 -0.806 -0.799 -0.799 -0.826 ...
  $ V13 : num  0.717 0.768 0.848 0.848 0.849 ...
  $ V14 : num  0.636 0.684 0.668 0.668 0.671 ...
  $ V15 : num  0.789 0.797 0.822 0.822 0.83 ...
  $ V16 : num  -0.878 -0.969 -0.977 -0.974 -0.975 ...
  $ V17 : num  -0.998 -1 -1 -1 -1 ...
  $ V18 : num  -0.998 -1 -1 -0.999 -0.999 ...
  $ V19 : num  -0.934 -0.998 -0.999 -0.999 -0.999 ...
  $ V20 : num  -0.976 -0.994 -0.993 -0.995 -0.993 ...
  $ V21 : num  -0.95 -0.974 -0.974 -0.979 -0.967 ...
  $ V22 : num  -0.83 -0.951 -0.965 -0.97 -0.976 ...
  $ V23 : num  -0.168 -0.302 -0.618 -0.75 -0.591 ...
  $ V24 : num  -0.379 -0.348 -0.695 -0.899 -0.74 ...
  $ V25 : num  0.246 -0.405 -0.537 -0.554 -0.799 ...
  $ V26 : num  0.521 0.507 0.242 0.175 0.116 ...
  $ V27 : num  -0.4878 -0.1565 -0.115 -0.0513 -0.0289 ...
  $ V28 : num  0.4823 0.0407 0.0327 0.0342 -0.0328 ...
  $ V29 : num  -0.0455 0.273 0.1924 0.1536 0.2943 ...
  $ V30 : num  0.21196 0.19757 -0.01194 0.03077 0.00063 ...
  $ V31 : num  -0.1349 -0.1946 -0.0634 -0.1293 -0.0453 ...
  $ V32 : num  0.131 0.411 0.471 0.446 0.168 ...
  $ V33 : num  -0.0142 -0.3405 -0.5074 -0.4195 -0.0682 ...
  $ V34 : num  -0.106 0.0776 0.1885 0.2715 0.0744 ...
  $ V35 : num  0.0735 -0.084 -0.2316 -0.2258 0.0271 ...
  $ V36 : num  -0.1715 0.0353 0.6321 0.4164 -0.1459 ...
  $ V37 : num  0.0401 -0.0101 -0.5507 -0.2864 -0.0502 ...
  $ V38 : num  0.077 -0.105 0.3057 -0.0638 0.2352 ...
  $ V39 : num  -0.491 -0.429 -0.324 -0.167 0.29 ...
  $ V40 : num  -0.709 0.399 0.28 0.545 0.458 ...
  $ V41 : num  0.936 0.927 0.93 0.929 0.927 ...
  $ V42 : num  -0.283 -0.289 -0.288 -0.293 -0.303 ...
  $ V43 : num  0.115 0.153 0.146 0.143 0.138 ...
  $ V44 : num  -0.925 -0.989 -0.996 -0.993 -0.996 ...
  $ V45 : num  -0.937 -0.984 -0.988 -0.97 -0.971 ...
  $ V46 : num  -0.564 -0.965 -0.982 -0.992 -0.968 ...
  $ V47 : num  -0.93 -0.989 -0.996 -0.993 -0.996 ...
  $ V48 : num  -0.938 -0.983 -0.989 -0.971 -0.971 ...
  $ V49 : num  -0.606 -0.965 -0.98 -0.993 -0.969 ...
  $ V50 : num  0.906 0.856 0.856 0.856 0.854 ...
  $ V51 : num  -0.279 -0.305 -0.305 -0.305 -0.313 ...
  $ V52 : num  0.153 0.153 0.139 0.136 0.134 ...
  $ V53 : num  0.944 0.944 0.949 0.947 0.946 ...
  $ V54 : num  -0.262 -0.262 -0.262 -0.273 -0.279 ...
  $ V55 : num  -0.0762 0.149 0.145 0.1421 0.1309 ...
  $ V56 : num  -0.0178 0.0577 0.0406 0.0461 0.0554 ...
  $ V57 : num  0.829 0.806 0.812 0.809 0.804 ...
  $ V58 : num  -0.865 -0.858 -0.86 -0.854 -0.843 ...
  $ V59 : num  -0.968 -0.957 -0.961 -0.963 -0.965 ...
  $ V60 : num  -0.95 -0.988 -0.996 -0.992 -0.996 ...
  $ V61 : num  -0.946 -0.982 -0.99 -0.973 -0.972 ...
  $ V62 : num  -0.76 -0.971 -0.979 -0.996 -0.969 ...
  $ V63 : num  -0.425 -0.729 -0.823 -0.823 -0.83 ...
  $ V64 : num  -1 -1 -1 -1 -1 -1 -1 -1 -1 -1 ...
  $ V65 : num  0.219 -0.465 -0.53 -0.7 -0.302 ...
  $ V66 : num  -0.43 -0.51 -0.295 -0.343 -0.482 ...
  $ V67 : num  0.431 0.525 0.305 0.359 0.539 ...
  $ V68 : num  -0.432 -0.54 -0.315 -0.375 -0.596 ...
  $ V69 : num  0.433 0.554 0.326 0.392 0.655 ...
  $ V70 : num  -0.795 -0.746 -0.232 -0.233 -0.493 ...
  $ V71 : num  0.781 0.733 0.169 0.176 0.463 ...
  $ V72 : num  -0.78 -0.737 -0.155 -0.169 -0.465 ...
  $ V73 : num  0.785 0.749 0.164 0.185 0.483 ...
  $ V74 : num  -0.984 -0.845 -0.429 -0.297 -0.536 ...
  $ V75 : num  0.987 0.869 0.44 0.304 0.544 ...
  $ V76 : num  -0.989 -0.893 -0.451 -0.311 -0.553 ...
  $ V77 : num  0.988 0.913 0.458 0.315 0.559 ...
  $ V78 : num  0.981 0.945 0.548 0.986 0.998 ...
  $ V79 : num  -0.996 -0.911 -0.335 0.653 0.916 ...
  $ V80 : num  -0.96 -0.739 0.59 0.747 0.929 ...
  $ V81 : num  0.072 0.0702 0.0694 0.0749 0.0784 ...
  $ V82 : num  0.04575 -0.01788 -0.00491 0.03227 0.02228 ...
  $ V83 : num  -0.10604 -0.00172 -0.01367 0.01214 0.00275 ...
  $ V84 : num  -0.907 -0.949 -0.991 -0.991 -0.992 ...
  $ V85 : num  -0.938 -0.973 -0.971 -0.973 -0.979 ...
  $ V86 : num  -0.936 -0.978 -0.973 -0.976 -0.987 ...
  $ V87 : num  -0.916 -0.969 -0.991 -0.99 -0.991 ...
  $ V88 : num  -0.937 -0.974 -0.973 -0.973 -0.977 ...
  $ V89 : num  -0.949 -0.979 -0.975 -0.978 -0.985 ...
  $ V90 : num  -0.903 -0.915 -0.992 -0.992 -0.994 ...
  $ V91 : num  -0.95 -0.981 -0.975 -0.975 -0.986 ...
  $ V92 : num  -0.891 -0.978 -0.962 -0.962 -0.986 ...
  $ V93 : num  0.898 0.898 0.994 0.994 0.994 ...
  $ V94 : num  0.95 0.968 0.976 0.976 0.98 ...
  $ V95 : num  0.946 0.966 0.966 0.97 0.985 ...
  $ V96 : num  -0.931 -0.974 -0.982 -0.983 -0.987 ...
  $ V97 : num  -0.995 -0.998 -1 -1 -1 ...
  $ V98 : num  -0.997 -0.999 -0.999 -0.999 -1 ...
  $ V99 : num  -0.997 -0.999 -0.999 -0.999 -1 ...
   [list output truncated]


## Merges data frames by rows
Subject <- rbind(SubjectTrain, SubjectTest)
Activity<- rbind(ActivityTrain, ActivityTest)
Features<- rbind(FeaturesTrain, FeaturesTest)

## Assign Variable Names
names(Subject) <- c("subject")
names(Activity)<- c("activity")
FeaturesNames <- read.table(file.path(path_rf, "features.txt"),head=FALSE)
names(Features)<- FeaturesNames$V2

## Merge data columns into one data frame
Combine <- cbind(Subject, Activity)
Data <- cbind(Features, Combine)

# Part 2: Finding mean and standard deviation ####

## Subsetting Features with "Mean" and "Std"
subFeaturesNames<-FeaturesNames$V2[grep("mean\\(\\)|std\\(\\)", 
                                        FeaturesNames$V2)]

## Subsetting into Subject and Activity 
selectedNames<-c(as.character(subFeaturesNames), "subject", "activity" )
Data<-subset(Data, select=selectedNames)
str(Data)
 'data.frame':    10299 obs. of  68 variables:
  $ tBodyAcc-mean()-X          : num  0.289 0.278 0.28 0.279 0.277 ...
  $ tBodyAcc-mean()-Y          : num  -0.0203 -0.0164 -0.0195 -0.0262 -0.0166 ...
  $ tBodyAcc-mean()-Z          : num  -0.133 -0.124 -0.113 -0.123 -0.115 ...
  $ tBodyAcc-std()-X           : num  -0.995 -0.998 -0.995 -0.996 -0.998 ...
  $ tBodyAcc-std()-Y           : num  -0.983 -0.975 -0.967 -0.983 -0.981 ...
  $ tBodyAcc-std()-Z           : num  -0.914 -0.96 -0.979 -0.991 -0.99 ...
  $ tGravityAcc-mean()-X       : num  0.963 0.967 0.967 0.968 0.968 ...
  $ tGravityAcc-mean()-Y       : num  -0.141 -0.142 -0.142 -0.144 -0.149 ...
  $ tGravityAcc-mean()-Z       : num  0.1154 0.1094 0.1019 0.0999 0.0945 ...
  $ tGravityAcc-std()-X        : num  -0.985 -0.997 -1 -0.997 -0.998 ...
  $ tGravityAcc-std()-Y        : num  -0.982 -0.989 -0.993 -0.981 -0.988 ...
  $ tGravityAcc-std()-Z        : num  -0.878 -0.932 -0.993 -0.978 -0.979 ...
  $ tBodyAccJerk-mean()-X      : num  0.078 0.074 0.0736 0.0773 0.0734 ...
  $ tBodyAccJerk-mean()-Y      : num  0.005 0.00577 0.0031 0.02006 0.01912 ...
  $ tBodyAccJerk-mean()-Z      : num  -0.06783 0.02938 -0.00905 -0.00986 0.01678 ...
  $ tBodyAccJerk-std()-X       : num  -0.994 -0.996 -0.991 -0.993 -0.996 ...
  $ tBodyAccJerk-std()-Y       : num  -0.988 -0.981 -0.981 -0.988 -0.988 ...
  $ tBodyAccJerk-std()-Z       : num  -0.994 -0.992 -0.99 -0.993 -0.992 ...
  $ tBodyGyro-mean()-X         : num  -0.0061 -0.0161 -0.0317 -0.0434 -0.034 ...
  $ tBodyGyro-mean()-Y         : num  -0.0314 -0.0839 -0.1023 -0.0914 -0.0747 ...
  $ tBodyGyro-mean()-Z         : num  0.1077 0.1006 0.0961 0.0855 0.0774 ...
  $ tBodyGyro-std()-X          : num  -0.985 -0.983 -0.976 -0.991 -0.985 ...
  $ tBodyGyro-std()-Y          : num  -0.977 -0.989 -0.994 -0.992 -0.992 ...
  $ tBodyGyro-std()-Z          : num  -0.992 -0.989 -0.986 -0.988 -0.987 ...
  $ tBodyGyroJerk-mean()-X     : num  -0.0992 -0.1105 -0.1085 -0.0912 -0.0908 ...
  $ tBodyGyroJerk-mean()-Y     : num  -0.0555 -0.0448 -0.0424 -0.0363 -0.0376 ...
  $ tBodyGyroJerk-mean()-Z     : num  -0.062 -0.0592 -0.0558 -0.0605 -0.0583 ...
  $ tBodyGyroJerk-std()-X      : num  -0.992 -0.99 -0.988 -0.991 -0.991 ...
  $ tBodyGyroJerk-std()-Y      : num  -0.993 -0.997 -0.996 -0.997 -0.996 ...
  $ tBodyGyroJerk-std()-Z      : num  -0.992 -0.994 -0.992 -0.993 -0.995 ...
  $ tBodyAccMag-mean()         : num  -0.959 -0.979 -0.984 -0.987 -0.993 ...
  $ tBodyAccMag-std()          : num  -0.951 -0.976 -0.988 -0.986 -0.991 ...
  $ tGravityAccMag-mean()      : num  -0.959 -0.979 -0.984 -0.987 -0.993 ...
  $ tGravityAccMag-std()       : num  -0.951 -0.976 -0.988 -0.986 -0.991 ...
  $ tBodyAccJerkMag-mean()     : num  -0.993 -0.991 -0.989 -0.993 -0.993 ...
  $ tBodyAccJerkMag-std()      : num  -0.994 -0.992 -0.99 -0.993 -0.996 ...
  $ tBodyGyroMag-mean()        : num  -0.969 -0.981 -0.976 -0.982 -0.985 ...
  $ tBodyGyroMag-std()         : num  -0.964 -0.984 -0.986 -0.987 -0.989 ...
  $ tBodyGyroJerkMag-mean()    : num  -0.994 -0.995 -0.993 -0.996 -0.996 ...
  $ tBodyGyroJerkMag-std()     : num  -0.991 -0.996 -0.995 -0.995 -0.995 ...
  $ fBodyAcc-mean()-X          : num  -0.995 -0.997 -0.994 -0.995 -0.997 ...
  $ fBodyAcc-mean()-Y          : num  -0.983 -0.977 -0.973 -0.984 -0.982 ...
  $ fBodyAcc-mean()-Z          : num  -0.939 -0.974 -0.983 -0.991 -0.988 ...
  $ fBodyAcc-std()-X           : num  -0.995 -0.999 -0.996 -0.996 -0.999 ...
  $ fBodyAcc-std()-Y           : num  -0.983 -0.975 -0.966 -0.983 -0.98 ...
  $ fBodyAcc-std()-Z           : num  -0.906 -0.955 -0.977 -0.99 -0.992 ...
  $ fBodyAccJerk-mean()-X      : num  -0.992 -0.995 -0.991 -0.994 -0.996 ...
  $ fBodyAccJerk-mean()-Y      : num  -0.987 -0.981 -0.982 -0.989 -0.989 ...
  $ fBodyAccJerk-mean()-Z      : num  -0.99 -0.99 -0.988 -0.991 -0.991 ...
  $ fBodyAccJerk-std()-X       : num  -0.996 -0.997 -0.991 -0.991 -0.997 ...
  $ fBodyAccJerk-std()-Y       : num  -0.991 -0.982 -0.981 -0.987 -0.989 ...
  $ fBodyAccJerk-std()-Z       : num  -0.997 -0.993 -0.99 -0.994 -0.993 ...
  $ fBodyGyro-mean()-X         : num  -0.987 -0.977 -0.975 -0.987 -0.982 ...
  $ fBodyGyro-mean()-Y         : num  -0.982 -0.993 -0.994 -0.994 -0.993 ...
  $ fBodyGyro-mean()-Z         : num  -0.99 -0.99 -0.987 -0.987 -0.989 ...
  $ fBodyGyro-std()-X          : num  -0.985 -0.985 -0.977 -0.993 -0.986 ...
  $ fBodyGyro-std()-Y          : num  -0.974 -0.987 -0.993 -0.992 -0.992 ...
  $ fBodyGyro-std()-Z          : num  -0.994 -0.99 -0.987 -0.989 -0.988 ...
  $ fBodyAccMag-mean()         : num  -0.952 -0.981 -0.988 -0.988 -0.994 ...
  $ fBodyAccMag-std()          : num  -0.956 -0.976 -0.989 -0.987 -0.99 ...
  $ fBodyBodyAccJerkMag-mean() : num  -0.994 -0.99 -0.989 -0.993 -0.996 ...
  $ fBodyBodyAccJerkMag-std()  : num  -0.994 -0.992 -0.991 -0.992 -0.994 ...
  $ fBodyBodyGyroMag-mean()    : num  -0.98 -0.988 -0.989 -0.989 -0.991 ...
  $ fBodyBodyGyroMag-std()     : num  -0.961 -0.983 -0.986 -0.988 -0.989 ...
  $ fBodyBodyGyroJerkMag-mean(): num  -0.992 -0.996 -0.995 -0.995 -0.995 ...
  $ fBodyBodyGyroJerkMag-std() : num  -0.991 -0.996 -0.995 -0.995 -0.995 ...
  $ subject                    : int  1 1 1 1 1 1 1 1 1 1 ...
  $ activity                   : int  5 5 5 5 5 5 5 5 5 5 ...

# Part 3: Rename the activities in the data set ####
activityLabels <- read.table(file.path(path_rf, "activity_labels.txt"),
                             header = FALSE)

## Reclass activity names as factors
Data$activity <- factor(Data$activity)
Data$activity <- factor(Data$activity,
                        labels=as.character(activityLabels$V2))

head(Data$activity,20)
  [1] STANDING STANDING STANDING STANDING STANDING STANDING STANDING
  [8] STANDING STANDING STANDING STANDING STANDING STANDING STANDING
 [15] STANDING STANDING STANDING STANDING STANDING STANDING
 6 Levels: WALKING WALKING_UPSTAIRS WALKING_DOWNSTAIRS ... LAYING

# Part 4: Give descriptive names to activities ####
names(Data)<-gsub("^t", "time", names(Data))
names(Data)<-gsub("^f", "frequency", names(Data))
names(Data)<-gsub("Acc", "Accelerometer", names(Data))
names(Data)<-gsub("Gyro", "Gyroscope", names(Data))
names(Data)<-gsub("Mag", "Magnitude", names(Data))
names(Data)<-gsub("BodyBody", "Body", names(Data))

# Part 5: Creates tidy datasets with average of each variable for each activity
# and each subject ####

library(plyr);
DataTidy <- aggregate(. ~subject + activity, Data, mean)
DataTidy <- DataTidy[order(DataTidy$subject, DataTidy$activity),]
write.table(DataTidy, file = "DataTidy.txt", row.name=FALSE)

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
str(ActivityTrain)
str(SubjectTest)
str(SubjectTrain)
str(FeaturesTest)
str(FeaturesTrain)

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

# Part 3: Rename the activities in the data set ####
activityLabels <- read.table(file.path(path_rf, "activity_labels.txt"),
                             header = FALSE)

## Reclass activity names as factors
Data$activity <- factor(Data$activity)
Data$activity <- factor(Data$activity,
                        labels=as.character(activityLabels$V2))

head(Data$activity,20)

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
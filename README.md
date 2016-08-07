#Data Science Getting and Cleaning Data Course Project

##Purpose

The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. You will be graded by your peers on a series of yes/no questions related to the project. You will be required to submit: 1) a tidy data set as described below, 2) a link to a Github repository with your script for performing the analysis, and 3) a code book that describes the variables, the data, and any transformations or work that you performed to clean up the data called CodeBook.md. You should also include a README.md in the repo with your scripts. This repo explains how all of the scripts work and how they are connected.

One of the most exciting areas in all of data science right now is wearable computing - see for example this article . Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site where the data was obtained:

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

Here are the data for the project:

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

You should create one R script called run_analysis.R that does the following.

Merges the training and the test sets to create one data set.
Extracts only the measurements on the mean and standard deviation for each measurement.
Uses descriptive activity names to name the activities in the data set
Appropriately labels the data set with descriptive variable names.
From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

Good luck!
 


##Data Analysis Explanation

###For 1st tiny data set:

* Read data sets and combine them
* Read subjects and combine them
* Read data labels and combine them
* Read features list
* Subset only only std and mean features from list
* Perform same subset on data set
* Rename features to be more human readable
* Read activity list
* Rename activities to be more human readable
* Rename data labels with activity name
* Merge data, subjects, and labels to single tiny data set
* Write tiny data set to file

###For 2nd tiny data set: average of measurement for activity and subject

* Prepare empty data set of appropriate length for
* Loop through subjects, then subloop through activities
* For each activity in a subject, get the full list of measurements
* Calculate the mean of each of these activities
* Place the means in subsequent columns of the subject/activity row
* Write second tiny data set to file



## Ths script used

``` R
# set working directoyr
setwd("C:/arun/coursera/DataScience/Getting_and_Cleaning_Data/UCI HAR Dataset")
library(plyr)
library(data.table)

# read training data
subjectTrain = read.table('./train/subject_train.txt',header=FALSE)
xTrain = read.table('./train/x_train.txt',header=FALSE)
yTrain = read.table('./train/y_train.txt',header=FALSE)

#read testing data
subjectTest = read.table('./test/subject_test.txt',header=FALSE)
xTest = read.table('./test/x_test.txt',header=FALSE)
yTest = read.table('./test/y_test.txt',header=FALSE)


#1 Merges the training and the test sets to create one data set.
#combine the data set
xDataSet <- rbind(xTrain, xTest)
yDataSet <- rbind(yTrain, yTest)
subjectDataSet <- rbind(subjectTrain, subjectTest)

dim(xDataSet)
dim(yDataSet)
dim(subjectDataSet)


#2 Extracts only the measurements on the mean and standard deviation for each measurement. 
xDataSet_mean_std <- xDataSet[, grep("-(mean|std)\\(\\)", read.table("features.txt")[, 2])]
names(xDataSet_mean_std) <- read.table("features.txt")[grep("-(mean|std)\\(\\)", read.table("features.txt")[, 2]), 2] 
View(xDataSet_mean_std)
dim(xDataSet_mean_std)


#3 Uses descriptive activity names to name the activities in the data set
yDataSet[, 1] <- read.table("activity_labels.txt")[yDataSet[, 1], 2]
names(yDataSet) <- "Activity"
View(yDataSet)


#4 Appropriately labels the data set with descriptive variable names. 
names(subjectDataSet) <- "Subject"
summary(subjectDataSet)




singleDataSet <- cbind(xDataSet_mean_std, yDataSet, subjectDataSet)

# Using descriptive names for all the variables.

names(singleDataSet) <- make.names(names(singleDataSet))
names(singleDataSet) <- gsub('Acc',"LinearAcceleration",names(singleDataSet))
names(singleDataSet) <- gsub('GyroJerk',"AngularAcceleration",names(singleDataSet))
names(singleDataSet) <- gsub('Gyro',"AngularSpeed",names(singleDataSet))
names(singleDataSet) <- gsub('Mag',"Magnitude",names(singleDataSet))
names(singleDataSet) <- gsub('^t',"TimeDomain.",names(singleDataSet))
names(singleDataSet) <- gsub('^f',"FrequencyDomain.",names(singleDataSet))
names(singleDataSet) <- gsub('\\.mean',".Mean",names(singleDataSet))
names(singleDataSet) <- gsub('\\.std',".StandardDeviation",names(singleDataSet))
names(singleDataSet) <- gsub('Freq\\.',"Frequency.",names(singleDataSet))
names(singleDataSet) <- gsub('Freq$',"Frequency",names(singleDataSet))

View(singleDataSet)


# From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
Data2<-aggregate(. ~Subject + Activity, singleDataSet, mean)
Data2<-Data2[order(Data2$Subject,Data2$Activity),]
write.table(Data2, file = "tidydata.txt",row.name=FALSE)


```

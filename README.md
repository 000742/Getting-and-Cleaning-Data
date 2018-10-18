# Getting-and-Cleaning-Data
# Week 4 Assignment
# A. Download and unzip the dataset:
fileURL <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileURL, getdata_dataset.zip, method="curl")
unzip(getdata_dataset.zip) 
#
library(dplyr)
#
# B. Read train and test data
Xtrain <- read.table("./UCI HAR Dataset/train/X_train.txt")
Ytrain <- read.table("./UCI HAR Dataset/train/Y_train.txt")
Subjtrain <- read.table("./UCI HAR Dataset/train/subject_train.txt")
#
Xtest <- read.table("./UCI HAR Dataset/test/X_test.txt")
Ytest <- read.table("./UCI HAR Dataset/test/Y_test.txt")
Subjtest <- read.table("./UCI HAR Dataset/test/subject_test.txt")
#
# C. read data description and activity labels
features <- read.table("./UCI HAR Dataset/features.txt")
activitylabels <- read.table("./UCI HAR Dataset/activity_labels.txt")
#
# 1. Merges the training and the test sets to create one data set
Xtotal <- rbind(Xtrain, Xtest)
Ytotal <- rbind(Ytrain, Ytest)
Subjtotal <- rbind(Subjtrain, Subjtest)
# 
# 2. Extracts only the measurements on the mean and standard deviation for each measurement.
selected_var <- features[grep("mean\\(\\)|std\\(\\)",features[,2]),]
Xtotal <- Xtotal[,selected_var[,1]]
#
# 3. Uses descriptive activity names to name the activities in the data set
colnames(Ytotal) <- "activity"
Ytotal$activitylabel <- factor(Ytotal$activity, labels = as.character(activitylabels[,2]))
activitylabel <- Ytotal[,-1]
#
# 4. Appropriately labels the data set with descriptive variable names.
colnames(Xtotal) <- features[selected_var[,1],2]
#
# 5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
colnames(Subjtotal) <- "subject"
total <- cbind(Xtotal, activitylabel, Subjtotal)
total_mean <- total %>% group_by(activitylabel, subject) %>% summarize_each(funs(mean))
write.table(total_mean, file = "./UCI HAR Dataset/tidydata.txt", row.names = FALSE, col.names = TRUE)


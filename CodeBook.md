# Code book describing variables, data and any transformations

# Set working directory
setwd("~/Google Drive/Coursera")

# Include required libraries
library(data.table)

# read features file
features <- read.table("features.txt")[-1]

# read "test" files
# apply column headings from features file
# read "subject" and "activity" files
# create the "test" file
x_test <- read.table("X_test.txt", header=FALSE)
names(x_test) <- features[,1]
y_test <- read.table("y_test.txt", header=FALSE)
colnames(y_test) <- c("V562")
subject_test <- read.table("subject_test.txt", header=FALSE)
colnames(subject_test) <- c("V563")
test <- cbind(x_test, y_test, subject_test)

# read "train" files
# apply column headings from features file
# read "subject" and "activity" files
# create the "train" file
x_train <- read.table("X_train.txt", header=FALSE)
names(x_train) <- features[,1]
y_train <- read.table("y_train.txt", header=FALSE)
colnames(y_train) <- c("V562")
subject_train <- read.table("subject_train.txt", header=FALSE)
colnames(subject_train) <- c("V563")
train <- cbind(x_train, y_train, subject_train)

# Merge test and train data sets
data <- rbind(test,train)

# read activity labels file
activity_labels <- read.table("activity_labels.txt", header=FALSE)

# merge data set with activity labels
data <- merge(data,activity_labels, by.x = c("V562"), by.y = c("V1"), all=TRUE)
names(data)[names(data) == 'V2'] <- 'V564'

# remove temporary data files from memory
rm(features,subject_test, subject_train, test, train,x_test, x_train, y_test, y_train, activity_labels)

# extract only the measurements on the mean and standard deviation for each measurement.
dataExtract <- cbind(data[,563:564],data[ ,grep("mean|std", names(data))])
names(dataExtract)[names(dataExtract) == 'V563'] <- 'Subject'
names(dataExtract)[names(dataExtract) == 'V564'] <- 'Activity'
dataExtract <- data.table(dataExtract)

# Create Independent Tidy Data Set
tidyData <- dataExtract[, lapply(.SD, mean), by=c("Subject","Activity")]

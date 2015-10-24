
## A script to create a tidy data set


#        **R E A D M E**


**run_analysis.R** is a script that creates a tidy data set from "Human Activity Recognition Using Smartphones Data Set". Written in R version 3.2.2 (2015-08-14) on Linux Xubuntu 14.04


The original data source for the tidy dataset is "Human Activity Recognition Using Smartphones Data Set". The zip file with the source data can be retrieved at the following address:
https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

To replicate the script for constructing the tidy dataset, the zip file needs to be downloaded and unpacked in the working directory of R. After that R script can be started.

The script starts by reading 6 original data files (the training and test sets together with respective subjects and activities) into R:
```{r, eval = FALSE}
X_test <- read.table("./test/X_test.txt")
y_test <- read.table("./test/y_test.txt")
subject_test <- read.table("./test/subject_test.txt")
X_train <- read.table("./train/X_train.txt")
y_train <- read.table("./train/y_train.txt")
subject_train <- read.table("./train/subject_train.txt")
```

# merging the training and test sets 
merged_X <- rbind(X_test, X_train)

# reading the file with feature names and labeling data with these names 
feature_names <- read.table("./features.txt")
colnames(merged_X) <- feature_names$V2

# Extracting only the measurements on the mean and standard deviation
extracted <- merged_X[, grep("(mean|std)\\(", colnames(merged_X))]

# merging subjects of train with subjects of test and activities of train with activities of test; labeling them
merged_y <- rbind(y_test, y_train)
merged_subject <- rbind(subject_test, subject_train)
colnames(merged_y) <- "Activity"
colnames(merged_subject) <- "Subject"

# merging data set with subjects and activities
merged_all <- cbind(merged_subject, merged_y, extracted)

# reading the file with activity names and using these names to name the activities in the data set 
activity_labels <- read.table("./activity_labels.txt")
merged_all$Activity <- activity_labels$V2[merged_y$Activity]

# creating a tidy data set with the average of each variable for each activity and each subject
library(reshape2)
melted_set <- melt(merged_all, id = c("Subject", "Activity"), measure.vars = names(merged_all)[3:68])
tidy_dataset <- dcast(melted_set, Subject + Activity ~ variable, mean)

# writing the created tidy data set into a text file
write.table(tidy_dataset, "./tidy_dataset.txt")



```{r, eval = FALSE}
data <- read.table(file_path, header = TRUE) 
View(data)
```



I was able to follow the README in the directory that explained what the analysis files did. 

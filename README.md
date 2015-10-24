
## A script to create a tidy data set


#        **R E A D M E**


**run_analysis.R** is a script that creates a tidy data set from "Human Activity Recognition Using Smartphones Data Set". Written in R version 3.2.2 (2015-08-14) on Linux Xubuntu 14.04


The original data source for the tidy dataset is "Human Activity Recognition Using Smartphones Data Set". The zip file with the source data can be retrieved at the following address:  
https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

To replicate the script for constructing the tidy dataset, the zip file needs to be downloaded and unpacked in the working directory of R. After that R script can be started.

The script starts by reading 6 original data files (the training and test sets together with respective subjects and activities) into R:
```{r, eval = FALSE}
X_test <- read.table("./test/X_test.txt")
X_train <- read.table("./train/X_train.txt")
y_test <- read.table("./test/y_test.txt")
y_train <- read.table("./train/y_train.txt")
subject_test <- read.table("./test/subject_test.txt")
subject_train <- read.table("./train/subject_train.txt")
```

Dimensions of the data are as follows:  
X_test [2947 : 561]  
X_train [7352 : 561]  
y_test [2947 : 1]  
y_train [7352 : 1]  
subject_test [2947 : 1]  
subject_train [7352 : 1]  

It makes sense to first merge training and test sets. Which is being done, then the file with feature names is read and the variables are labeled with these names:
```{r, eval = FALSE}
merged_X <- rbind(X_test, X_train)
feature_names <- read.table("./features.txt")
colnames(merged_X) <- feature_names$V2
```

After that, only the measurements on the mean and standard deviation are extracted (66 out of 561 variables):
```{r, eval = FALSE}
extracted <- merged_X[, grep("(mean|std)\\(", colnames(merged_X))]
```

The labeling is performed before extracting a subset of the data for simplicity (no need to extract a subset of feature names).

Next step - merging subjects of train with subjects of test and activities of train with activities of test; labeling them appropriatly:
```{r, eval = FALSE}
merged_y <- rbind(y_test, y_train)
merged_subject <- rbind(subject_test, subject_train)
colnames(merged_y) <- "Activity"
colnames(merged_subject) <- "Subject"
```

Now the extracted data set is merged with their according subjects and activities:
```{r, eval = FALSE}
merged_all <- cbind(merged_subject, merged_y, extracted)
```
The result is a data frame with 10299 rows and 68 columns

The last "touch" - reading the file with activity names and using these names to name the activities in the data set (converting a numerical column "Activities" into appropriate strings):
```{r, eval = FALSE}
activity_labels <- read.table("./activity_labels.txt")
merged_all$Activity <- activity_labels$V2[merged_y$Activity]
```
At this point, a tidy data set is created:
* each variable is in a separate column
* each row contains a different observation
* The variables are labeled with names from the original data. They are sufficiently readable, plus they are described and explained in the codebook.


The last step is to create another tidy data set with the average of each variable (3-68) for each activity and each subject. For that a package "reshape2" is used.
```{r, eval = FALSE}
library(reshape2)
melted_set <- melt(merged_all, id = c("Subject", "Activity"), measure.vars = names(merged_all)[3:68])
tidy_dataset <- dcast(melted_set, Subject + Activity ~ variable, mean)
```
The resulting tidy data set is outputted into a text file, and uploaded to Coursera submission webpage.
```{r, eval = FALSE}
write.table(tidy_dataset, "./tidy_dataset.txt")
```

The best way to view the the final tidy set is to read it back into R:
```{r, eval = FALSE}
address <- "https://s3.amazonaws.com/coursera-uploads/user-98713875ad60962294962533/975117/asst-3/1a1206807a4111e5bfa091dfd115a3ca.txt"
address <- sub("^https", "http", address)
data <- read.table(url(address), header = TRUE) 
View(data)
```


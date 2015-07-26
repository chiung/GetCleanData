# Getting and Cleaning Data Assignment   #

----------

## Preamble ##

This is the required documentation and codebook to describe the code used for completing the getting and cleaning data assignment. For brevity and clarity's sake, not every line of code will be commented on, as the reader is assumed to have working knowledge of R.
## Package installation ##
For this assignment, I made use of the data.table package which claims to support the following:
> Fast aggregation of large data (e.g. 100GB in RAM), fast ordered joins, fast add/modify/delete of columns by group using no copies at all, list columns and a fast file reader (fread). Offers a natural and flexible syntax, for faster development.

This needs to be installed and loaded into through the R script (refer to the run_analysis.R script in this repo)

    library(data.table)

## Downloading the data into a temp folder, unzip and assign content of text files to variables  ##
    YTest <- read.table(unzip(temp, "UCI HAR Dataset/test/y_test.txt"))
    XTest <- read.table(unzip(temp, "UCI HAR Dataset/test/X_test.txt"))
    SubjectTest <- read.table(unzip(temp, "UCI HAR Dataset/test/subject_test.txt"))
    YTrain <- read.table(unzip(temp, "UCI HAR Dataset/train/y_train.txt"))
    XTrain <- read.table(unzip(temp, "UCI HAR Dataset/train/X_train.txt"))
    SubjectTrain <- read.table(unzip(temp, "UCI HAR Dataset/train/subject_train.txt"))
    Features <- read.table(unzip(temp, "UCI HAR Dataset/features.txt"))
    unlink(temp) # For housekeeping, this will remove the temp directory in use

## Reassign column names to new  labels according to the features.txt file  ##

Here, we attempt to align the X and Y Training set.

    colnames(XTrain) <- t(Features[2])#reshape the read table via a transpose
    colnames(XTest) <- t(Features[2])# reshape the read table via a transpose
    XTrain$activities <- YTrain[, 1]
    XTrain$participants <- SubjectTrain[, 1]
    XTest$activities <- YTest[, 1]
    XTest$participants <- SubjectTest[, 1]

## Step 1: Merge training and testing data sets ##

We use th r bind fuction to combine the training and testing data sets, and removing duplicated data

    Master <- rbind(XTrain, XTest)
    duplicated(colnames(Master))
    Master <- Master[, !duplicated(colnames(Master))]

## Step 2: Extract mean and standard deviation ##

In this step, we extract only the mean and standard deviation of the measurement

    Mean <- grep("mean()", names(Master), value = FALSE, fixed = TRUE)
    Mean <- append(Mean, 471:477)
    InstrumentMeanMatrix <- Master[Mean]
    STD <- grep("std()", names(Master), value = FALSE)
    InstrumentSTDMatrix <- Master[STD]

## Step 3: Apply descriptive values onto the activities ##

The class of activities is changed to the character class so it's compatible with the string assignment, upon completion it's reverted to factor class for easier manipulation

    Master$activities <- as.character(Master$activities)
    Master$activities[Master$activities == 1] <- "Walking"
    Master$activities[Master$activities == 2] <- "Walking Upstairs"
    Master$activities[Master$activities == 3] <- "Walking Downstairs"
    Master$activities[Master$activities == 4] <- "Sitting"
    Master$activities[Master$activities == 5] <- "Standing"
    Master$activities[Master$activities == 6] <- "Laying"
    Master$activities <- as.factor(Master$activities)

## Step 4: Label the data set with descriptive variable names.  ##

The goal is to remove ambiguity and make clear what each variable holds. This is done through the code below

    names(Master)
    names(Master) <- gsub("Acc", "Accelerator", names(Master))
    names(Master) <- gsub("Mag", "Magnitude", names(Master))
    names(Master) <- gsub("Gyro", "Gyroscope", names(Master))
    names(Master) <- gsub("^t", "Time", names(Master))
    names(Master) <- gsub("^f", "Frequency", names(Master))
    Master$participants <- as.character(Master$participants)
    Master$participants[Master$participants == 1] <- "Participant 1"
    Master$participants[Master$participants == 2] <- "Participant 2"
    Master$participants[Master$participants == 3] <- "Participant 3"
    Master$participants[Master$participants == 4] <- "Participant 4"
    Master$participants[Master$participants == 5] <- "Participant 5"
    Master$participants[Master$participants == 6] <- "Participant 6"
    Master$participants[Master$participants == 7] <- "Participant 7"
    Master$participants[Master$participants == 8] <- "Participant 8"
    Master$participants[Master$participants == 9] <- "Participant 9"
    Master$participants[Master$participants == 10] <- "Participant 10"
    Master$participants[Master$participants == 11] <- "Participant 11"
    Master$participants[Master$participants == 12] <- "Participant 12"
    Master$participants[Master$participants == 13] <- "Participant 13"
    Master$participants[Master$participants == 14] <- "Participant 14"
    Master$participants[Master$participants == 15] <- "Participant 15"
    Master$participants[Master$participants == 16] <- "Participant 16"
    Master$participants[Master$participants == 17] <- "Participant 17"
    Master$participants[Master$participants == 18] <- "Participant 18"
    Master$participants[Master$participants == 19] <- "Participant 19"
    Master$participants[Master$participants == 20] <- "Participant 20"
    Master$participants[Master$participants == 21] <- "Participant 21"
    Master$participants[Master$participants == 22] <- "Participant 22"
    Master$participants[Master$participants == 23] <- "Participant 23"
    Master$participants[Master$participants == 24] <- "Participant 24"
    Master$participants[Master$participants == 25] <- "Participant 25"
    Master$participants[Master$participants == 26] <- "Participant 26"
    Master$participants[Master$participants == 27] <- "Participant 27"
    Master$participants[Master$participants == 28] <- "Participant 28"
    Master$participants[Master$participants == 29] <- "Participant 29"
    Master$participants[Master$participants == 30] <- "Participant 30"
    Master$participants <- as.factor(Master$participants)

## Step 5: Creating the tidy data set ##

This is done by using the stipulated write.table() function

    Master.dt <- data.table(Master)
    TidyData <- Master.dt[, lapply(.SD, mean), by = 'participants,activities']
    write.table(TidyData, file = "Tidy.txt", row.names = FALSE)
Getting and Cleaning Data Course Project

Introduction
============

This submission is part of the Getting and Cleaning Data Course Project. This ReadMe file explains how the Rscript, data sets, and codebook work to fulfil the requirements of the project. 
The purpose of this project is to demonstrate the student ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. Files required to submit: 1) a tidy data set as described below, 2) a link to a Github repository with the script for performing the analysis, and 3) a code book that describes the variables, the data, and any transformations or work performed to clean up the data called CodeBook.md. 


Data for the project
============
The data used for this submission can be downloaded from the UCI Machine Learning Repository (below link) and presents the Human Activity Recognition Using Smartphones Data Set. This is an experiments carried out with a group of 30 volunteers within an age bracket of 19-48 years. Each person performed six activities (WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, LAYING) wearing a smartphone (Samsung Galaxy S II) on the waist. Using its embedded accelerometer and gyroscope, we captured 3-axial linear acceleration and 3-axial angular velocity at a constant rate of 50Hz. The experiments have been video-recorded to label the data manually. The obtained dataset has been randomly partitioned into two sets, where 70% of the volunteers was selected for generating the training data and 30% the test data.

Download: http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

The zip file includes:
1.	“README.txt”
2.	“features_info.txt”: Shows information about the variables used on the feature vector.
3.	“features.txt”: List of all features.
4.	“activity_labels.txt”: Links the class labels with their activity name.
5.	“train/X_train.txt”: Training set.
6.	“train/y_train.txt”: Training labels.
7.	”test/X_test.txt”: Test set.
8.	”test/y_test.txt”: Test labels.


R Scrip short introduction 
============
In the following pages of this README file it is explain step by step how the script attached in this repository works as required for the curse project, but no in the same order. Additional remarks were placed to facilitate the reader to find which of the points, as described in the assignment, is being developed. The each of following steps are divided as “Part” (1,2,3… etc.) and can be equally found in the Rscrip file attached.  






PART 1
============
Part one consist only in getting ready with extra columns, names, labels, and activities by creating objects which can be call later to shape correctly the data sets provided before merging completely. I am sure this can be done individually but I think this way saves a lot of time. 

PART 1 (A) ==

# Loading Psych and Plyr packages 
-	Plyr: Tools for splitting, applying, and combining data
o	http://cran.r-project.org/web/packages/plyr/plyr.pdf
-	Psych: Procedures for Psychological, Psychometric, and Personality Research
o	http://cran.r-project.org/web/packages/psych/psych.pdf

# Set working directories
Necessaire for the short version (“..\\”) of working directory. The actual directory has been erased from the script submitted. The reader most run setgw with its own directory to get the script work. 

PART 1 (B) ==

-- It is quite useful to begin reading into r the “features” file and get it ready for later usage. I used as.character () to create an object with the “character” characteristics. 
# Names from features
features <- read.table(".\\features.txt")
featureNames <- as.character(features[,2])
View(features)

-- Second, call only the variables which use “mean” and “standard deviation” as required in (Step 2) and place it in an object call “featuresmeanstddv”. The “grepl” function (http://stat.ethz.ch/R-manual/R-devel/library/base/html/grep.html) to search in featuresNames object only this variables. 

# STEP 2 (Exract only mean and std dev)
featuresmeanstddv<- featureNames[grepl("mean[(][)]|std[(][)]", featureNames)]

--Third, create an object to change the short names for full names using gsub function (http://rfunction.com/archives/2354). 

# sub short names for fully names
features_renames <- gsub("()", "", 
gsub("Freq", "Frequency", 
gsub("-X", "XAxis",
gsub("-Y", "YAxis",
gsub("-Z", "ZAxis",… etc. 

--Forth, call into r the activity labels and assign a readable name for each one. 
### STEP 3 (a) (as requested)
#Uses descriptive activity names to name the activities in the data set
# Ready the activity labels.
activity_labels <- read.table(".\\activity_labels.txt")
names(activity_labels)[1] <- "activity_label"
names(activity_labels)[2] <- "activity"
View(activity_labels)


PART 2
============
Loading data sets (training and test) transform them to readable form and shape. 

### PART 2 (A)
Load test data set and change to the correct variable names. I found useful to use dim() and View() functions to check how the data base is being modeled. 
# Load the test data.
x_test <- read.table(file=".\\test\\X_test.txt")
names(x_test) <- featureNames
dim(x_test)
View(x_test)

--Second, we don’t need all the data base just the variables which meet the “mean” and “std dev” (Step 2) criteria. 
# STEP 2 (Exract only mean and std dev)
# Just keep the mean() and std() values.
# from "featuresmeanstddv <- featureNames[grepl("mean[(][)]|std[(][)]", featureNames)]" 
# Search only required mean and standard deviation from x_test
x_test <- x_test[,featuresmeanstddv]
dim(x_test)
View(x_test)

--Third, use the “features_renames” object to correctly name the variables. I am not sure still if this step is totally necessary but I found it useful to make tidy data set with nice names. 
# STEP 4 (A)
# Rename with descriptive names
# From "featureNames <- as.character(features[,2])"
names(x_test) <- features_renames
dim(x_test)
View(x_test)

--Fourth, call the activity for “test” data set and rename. 
# Load the activity.
y_test <- read.table(file=".\\test\\y_test.txt")
names(y_test)[1] <- "activity_label"

--Fifth, call the subject for the test data set. 
# Load what subject performed the record.
subject_test <- read.table(file=".\\test\\subject_test.txt")
names(subject_test)[1] <- "subject"




-- Finally, combine the objects to have a full data set with it corresponding names and number of variables. 
# STEP 4 (A)
# Add descriptive version of the activity.
test <- merge(test, activity_labels, by=c("activity_label"))
dim(test)
View(test)

# Add the data.
test <- cbind(test, x_test)
dim(test)
View(test)


### PART 2 (B)
The same exactly process as Part 2 A is perform in this B. 

### PART 3
Finally, step one (as requested in the course), combine the two data sets which are already shortened with mean and std dev criteria and with the full names. Rbind() function is used to combine the data sets (http://stat.ethz.ch/R-manual/R-patched/library/base/html/cbind.html) 

### PART 3(A)
# STEP ONE
## Merges the training and the test sets to create one data set
hartot <- rbind(train, test)
dim(hartot)
View(hartot)

### PART 3 (B)
This part address Step 5 (Creates a second, independent tidy data set with the average of each variable for each activity and each subject.) by summarize each variable and create a data set (“tidy”) with the mean for each activity and subject. The function ddply () is used for Split data frame, apply function, and return results in a data frame (http://cran.r-project.org/web/packages/plyr/plyr.pdf).  
tidy <- ddply(hartot, .(subject, activity), summarize, 
              TimeDomainBodyAccelerationMeanXAxis=mean(TimeDomainBodyAccelerationMeanXAxis),
              TimeDomainBodyAccelerationMeanYAxis=mean(TimeDomainBodyAccelerationMeanYAxis), … etc. 

The final step is to export the “tidy” data set created. 
# STEP 5
# 5. Creates a second, independent tidy data set with the average of each variable for each activity and each subject.
# Exporting tidy data set
write.table(x=tidy, file="tidy.txt", row.name=FALSE)
tidyview <- read.table(file=".\\tidy.txt")
View(tidyview)



REFERENCES
- R grepl Function (http://www.endmemo.com/program/R/grepl.php)
- grep()Pattern Matching and Replacement (http://stat.ethz.ch/R-manual/R-devel/library/base/html/grep.html)
- UCI HAR dataset (http://rpubs.com/wangwf/19016)
- sefakilic GitHub coursera-getdata (https://github.com/sefakilic/coursera-getdata/blob/master/run_analysis.R)
- Analysis of UCI HAR Dataset (http://rstudio-pubs-static.s3.amazonaws.com/10696_c676703d98c84553b9e3510b095153b9.html)
- Samsung Phone Data Analysis Project (http://www.r-bloggers.com/samsung-phone-data-analysis-project/)
- scott schreckengaust GitHub (https://raw.githubusercontent.com/scottschreckengaust/getdata-004/master/run_analysis.R)
- ddply for sum by group in R (http://stackoverflow.com/questions/14035872/ddply-for-sum-by-group-in-r)
- Transforming subsets of data in R with by, ddply and data.table (http://www.r-bloggers.com/transforming-subsets-of-data-in-r-with-by-ddply-and-data-table/)
- To find each specific functions (http://cran.r-project.org/) 
- Data Science, Wearable Computing and the Battle for the Throne as World’s Top Sports Brand (http://www.insideactivitytracking.com/data-science-activity-tracking-and-the-battle-for-the-worlds-top-sports-brand/)
- David's Course Project FAQ (https://class.coursera.org/getdata-006/forum/thread?thread_id=43) 


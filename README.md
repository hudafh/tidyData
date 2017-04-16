# README

tidyData<- function() {
 
  
  trainData <- read.table("./train/X_train.txt", header = FALSE, sep = "")
 
  measures <- readLines("./features.txt")
 
  colnames(trainData) <- measures
 
  trainActivities <- readLines("./train/y_train.txt")
 
  trainSubjects <-readLines("./train/subject_train.txt")
 
 
  trainData <- mutate(trainData, subject = trainSubjects, activity = trainActivities)
 
  
  testData <- read.table("./test/X_test.txt", header = FALSE, sep = "")
 
  
  testActivities <- readLines("./test/y_test.txt")
  
  testSubjects <-readLines("./test/subject_test.txt")
 
  colnames(testData) <- measures
 
  testData <- mutate(testData, subject = testSubjects, activity = testActivities)
 
  jointData <- rbind(trainData, testData)
 
  jointData <- jointData[,c(562:563,1:561)]  # Changing the order of columns so that "subject" and "activity" are the first columns
 
  jointData <- arrange(jointData,as.numeric(subject),as.numeric(activity)) # sort by subject and then by activity
 
  vars <- grep("mean\\(\\)|std\\(\\)",colnames(jointData))  # get the columns that measure the mean and standard deviation
 
  cleanSet <- jointData[,c(1:2,vars)]  # Dataset with only the measures related to the mean and standard deviation
 
  cleanSet <- cleanSet %>% group_by(subject,activity) %>% summarise_each(funs_(c("mean","sd")))  # Summarize columns by mean and standard deviation (less rows)
 
   cleanSet <- arrange(cleanSet,as.numeric(subject),as.numeric(activity)) # sort "clean" dataset by subject and activity
 
   cleanSet$activity[cleanSet$activity ==1] <- "WALKING"  # renaming activities into more understandable labels/names
   cleanSet$activity[cleanSet$activity ==2] <- "WALKING_UPSTAIRS"
   cleanSet$activity[cleanSet$activity ==3] <- "WALKING_DOWNSTAIRS"
   cleanSet$activity[cleanSet$activity ==4] <- "SITTING"
   cleanSet$activity[cleanSet$activity ==5] <- "STANDING"
   cleanSet$activity[cleanSet$activity ==6] <- "LAYING"
 
  
  
  cleanSet
 
 
 
  
}
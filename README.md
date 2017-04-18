# README

The script works as follows:

1. It reads the file with the data from the train portion of the experiment into a variable called trainData
2. Read file with the measures of that data, i.e., the names of the columns where the above data falls on
3. Name the columns in trainData with the measures read in the previous step
4. Read the Activities(walking, sitting, etc...), located in .../train/y_train, performed in the Train portion of the experiment
5. Read the subjects, located in .../train/subject_train.txt, involved the train portion of the experiment
6. Mutate the trainData table(the one with the data from the Train portion of the experiment), to include the subjects and the activities ( a column for the subjects and one for the activities)
7. Do steps 1-6 for the Test portion of the data, resulting in a dataframe named testData, with the same number of columns and column names as trainData
8. Join trainData and testData, resulting in a dataframe called jointData
9. 



tidyData<- function() {
 
  
  trainData <- read.table("./train/X_train.txt", header = FALSE, sep = "")  #reads train data
 
  measures <- readLines("./features.txt")  #reads features as measures
 
  colnames(trainData) <- measures  #names dataframe columns using the measures given
 
  trainActivities <- readLines("./train/y_train.txt")  #reading the train activities
 
  trainSubjects <-readLines("./train/subject_train.txt")  #dataframes with the subjects who participated in the train portion
 
  trainData <- mutate(trainData, subject = trainSubjects, activity = trainActivities)  #combining the data from the above lines of code
 
  
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

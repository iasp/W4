# 1. Downloaded the zip file and used read.table() to load data into R:

## Reading data set
X_Test <- read.table("/Users/andreaaraujo/Documents/Exploratory Data Analysis in R/Coursera/UCI HAR Dataset/test/X_test.txt")
Y_Test <- read.table("/Users/andreaaraujo/Documents/Exploratory Data Analysis in R/Coursera/UCI HAR Dataset/test/Y_test.txt")
Subject_Test <- read.table("/Users/andreaaraujo/Documents/Exploratory Data Analysis in R/Coursera/UCI HAR Dataset/test/subject_test.txt")

## Reading labels
VariableNames <- read.table("/Users/andreaaraujo/Documents/Exploratory Data Analysis in R/Coursera/UCI HAR Dataset/features.txt")
ActivityLabels <- read.table("/Users/andreaaraujo/Documents/Exploratory Data Analysis in R/Coursera/UCI HAR Dataset/activity_labels.txt")

## Reading train set
X_Train <- read.table("/Users/andreaaraujo/Documents/Exploratory Data Analysis in R/Coursera/UCI HAR Dataset/train/X_train.txt")
Y_Train <- read.table("/Users/andreaaraujo/Documents/Exploratory Data Analysis in R/Coursera/UCI HAR Dataset/train/y_train.txt")
Subject_Train <- read.table("/Users/andreaaraujo/Documents/Exploratory Data Analysis in R/Coursera/UCI HAR Dataset/train/subject_train.txt")

# 2. Created VariableName vector and used it to change column names for both Test & Train Sets using names()
# 3. Binded X & Y for both sets using dplyr's bind_cols()
# 4. Used grep() to filter mean and standard deviation variables

VariableNameVector <- VariableNames$V2
names(X_Test) <- VariableNameVector
names(X_Train) <- VariableNameVector

XY_Test <- bind_cols(X_Test, Y_Test)
XY_Train <- bind_cols(X_Train, Y_Train)

MeanVariables <- grep(".*[Mm]ean", names(XY_Test), value=TRUE)
StdVariables <- grep(".*[Ss]td", names(XY_Test), value=TRUE)

# 5. Subsetted X & Y sets according to the mean and standard deviation variables

XY_Test_Means <- XY_Test[MeanVariables]
XY_Test_Stds <- XY_Test[StdVariables]

# 6. Binded X & Y sets to its respective subjects and renamed column names

XY_Test_Stds <- bind_cols(Subject_Test, XY_Test_Stds)
XY_Test_Stds <- rename(XY_Test_Stds, SubjectID = V1)
XY_Test_Stds <- bind_cols(Y_Test, XY_Test_Stds)
XY_Test_Stds <- rename(XY_Test_Stds, ActivityID = V1)
XY_Test_Complete <- bind_cols(XY_Test_Stds, XY_Test_Means)


XY_Train_Complete <- bind_cols(X_Train, Y_Train)
XY_Train_MeanVariables <- XY_Train_Complete[MeanVariables]
XY_Train_Stds <- XY_Train_Complete[StdVariables]
XY_Train_Final <- bind_cols(XY_Train_MeanVariables, XY_Train_Stds)
XY_Train_Final <- bind_cols(Subject_Train, XY_Train_Final)
XY_Train_Final <- rename(XY_Train_Final, SubjectID = V1)
XY_Train_Final <- bind_cols(Y_Train, XY_Train_Final)
XY_Train_Final <- rename(XY_Train_Final, ActivityID = V1)


# 7. Checked if I had 30 unique subjects using NROW and unique

NROW(unique(XY_Test_Complete$SubjectID)) + NROW(unique(XY_Train_Final$SubjectID))

# 8. Used dplyr's group_by() and summarise(across(everything(), list(mean)) on each set

Test_01 <- XY_Test_Complete %>% group_by(ActivityID, SubjectID) %>%
        summarise(across(everything(), list(mean)))

Train_01 <- XY_Train_Final %>% group_by(ActivityID, SubjectID) %>%
        summarise(across(everything(), list(mean)))

# 9. Merged the two sets and arranged it by subject

TwoSets <- bind_rows(Test_01, Train_01)
TwoSets <- arrange(TwoSets, TwoSets$SubjectID)

# 10. Assigned each of the 6 activities to its corresponding activity

VectorActivityLabel <- ActivityLabels$V2
VectorActivityID <- TwoSets$ActivityID
SortedActivityLabels <- VectorActivityLabel[VectorActivityID]
TwoSets$ActivityID <- SortedActivityLabels

# 11. Changed variable names using gsub 

names(TwoSets) <- gsub("^t", "Time", names(TwoSets))
names(TwoSets) <- gsub("^f", "Frequency", names(TwoSets))
names(TwoSets) <- gsub("Acc", "Accelerator", names(TwoSets))
names(TwoSets) <- gsub("Gyro", "Gyroscope", names(TwoSets))
names(TwoSets) <- gsub("Mag", "Magnitue", names(TwoSets))
names(TwoSets) <- gsub("BodyBody", "Body", names(TwoSets))
names(TwoSets) <- gsub("-std()", "Std", names(TwoSets))
names(TwoSets) <- gsub("-mean()", "Mean", names(TwoSets))
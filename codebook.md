##unzip(zipfile="./project/dataset.zip",exdir ="./project")
##path <- file.path("./project","UCI HAR Dataset")
##files <- list.files(path,recursive=TRUE)

#reading train files
xtrain <- read.table(file.path(path,"train","X_train.txt"),header = FALSE)
ytrain <- read.table(file.path(path,"train","Y_train.txt"),header = FALSE)
subject_train <- read.table(file.path(path,"train","subject_train.txt"),header = FALSE)

#reading test files
xtest <- read.table(file.path(path,"test","X_test.txt"),header = FALSE)
ytest <- read.table(file.path(path,"test","Y_test.txt"),header = FALSE)
subject_test <- read.table(file.path(path,"test","subject_test.txt"),header = FALSE)

#reading feature data
feature <- read.table(file.path(path,"features.txt"),header = FALSE)

#reading activity data
activity_labels <- read.table(file.path(path,"activity_labels.txt"),header = FALSE)

#colnames train
colnames(xtrain) = feature[,2]
colnames(ytrain) = "activityId"
colnames(subject_train) = "subjectId"
#colnames test
colnames(xtest) = feature[,2]
colnames(ytest) = "activityId"
colnames(subject_test) = "subjectId"
#colnames activity labels
colnames(activity_labels) <- c('activityId','activityType')

#merge files
test_total <- cbind(ytest,subject_test,xtest)
train_total <- cbind(ytrain,subject_train,xtrain)

#consolidated file
consolidated_data <- rbind(test_total,train_total) # output 1

#select mean and std
columns <- colnames(consolidated_data)
data_mean_std <- (grepl("activityId",columns)   | 
                  grepl("subjectId",columns)    | 
                  grepl("[Mm]ean..",columns)    |
                  grepl("[Ss]td..",columns))
cons_mean_std <- consolidated_data[,data_mean_std == TRUE]
data_cons_activitynames<- merge(cons_mean_std,activity_labels,by="activityId",all.x = TRUE) #output 2

#tidy datasets
dataset_tidy <- aggregate(. ~ subjectId + activityId,data_cons_activitynames,mean)
dataset_tidy_final <- dataset_tidy[order(dataset_tidy$subjectId,dataset_tidy$activityId),]

#export dataset
write.table(dataset_tidy_final,"./project/tidydataset.txt",row.names = FALSE)

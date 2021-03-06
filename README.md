# PracticalMachineLearning
## Preparation
### Load libraries and data-sets for the work.
library(caret)
training <- read.csv("pml-training.csv", na.strings=c("NA","#DIV/0!",""))
testing <- read.csv("pml-testing.csv", na.strings=c("NA","#DIV/0!",""))
## Exploring Data
dim(training)
table(training$classe)
### There are 19622 observation in traning dataset, 
### including 160 variables. The last column is the target variable classe.
### The most abundant class is A. There are some variables having 
### a lot of missing values, for simplicity, I have removed all the variables 
### containing NA values. And also, several variables are not direcly 
### related to the target variable classe, I also removed those varialbes, 
### those variables are "x", "user_name", and all the time related variables, 
### such as "raw_timestamp_part_1" etc.
NA_Count = sapply(1:dim(training)[2],function(x)sum(is.na(training[,x])))
NA_list = which(NA_Count>0)
colnames(training[,c(1:7)])
training = training[,-NA_list]
training = training[,-c(1:7)]
training$classe = factor(training$classe)
testing = testing[,-NA_list]
testing = testing[,-c(1:7)]
## Cross Validation
### The problem presenting here is a classification problem, I tried to use the classification 
### method in caret package: classification tree algorithm and random force. I also carried out 
### 3-fold validation using the trainControl function.
set.seed(1234)
cv3 = trainControl(method="cv",number=3,allowParallel=TRUE,verboseIter=TRUE)
modrf = train(classe~., data=training, method="rf",trControl=cv3)
modtree = train(classe~.,data=training,method="rpart",trControl=cv3)
prf=predict(modrf,training)
ptree=predict(modtree,training)
table(prf,training$classe)
table(ptree,training$classe)
### For the testing dataset.
prf=predict(modrf,testing)
ptree=predict(modtree,testing)
table(prf,ptree)
# From the results, it appears that the random forest model has the best accuracy for testing dataset.
answers=predict(modrf,testing)
pml_write_files = function(x){
  n = length(x)
  for(i in 1:n){
    filename = paste0("problem_id_",i,".txt")
    write.table(x[i],file=filename,quote=FALSE,row.names=FALSE,col.names=FALSE)
  }
}
answers


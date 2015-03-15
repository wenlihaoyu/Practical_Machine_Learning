# Machine Learning Peer Assessments
========================================================
###  using Random Forest Modeling, and got over 99% accuracy on my training set of 60% of the total data. All my submitted test cases were succesful.

## Loading and cleaning Data

```r
library(ggplot2)
library(caret)
```

```
## Loading required package: lattice
```

```r
library(caret)

library(e1071)
setwd('C:\\Users\\wenlihaoyu\\Desktop\\PracticalMachine')
testsets <- read.csv("pml-testing.csv",na.strings=c("NA",""))
trainsets <- read.csv("pml-training.csv",na.strings=c("NA",""))
NAs <- apply(trainsets,2,function(x) {sum(is.na(x))}) 
training <- trainsets[,which(NAs == 0)]
testing <- testsets[,which(NAs == 0)]
```

## Building data sets for training and cross validation. 
### Using 60% for training and 40% for Cross Validation.
###  Removing variables that have time, or names in it, also those are the Columns 1..6

```r
trainIndex <- createDataPartition(y = training$classe, p=0.6,list=FALSE)
trainSet <- training[trainIndex,]
crossValidationSet <- training[-trainIndex,]
removeIndex <- as.integer(c(1,2,3,4,5,6))
trainSet <- training[,-removeIndex]
testSet <- testing[,-removeIndex]
```

## Build random forest model with full training model

```r
mytrControl = trainControl(method = "cv", number = 4)
modelFit <- train(classe ~.,data = trainSet, method="rf", trControl = mytrControl)
```

```
## Loading required package: randomForest
## randomForest 4.6-10
## Type rfNews() to see new features/changes/bug fixes.
```

```r
modelFit
```

```
## Random Forest 
## 
## 19622 samples
##    53 predictor
##     5 classes: 'A', 'B', 'C', 'D', 'E' 
## 
## No pre-processing
## Resampling: Cross-Validated (4 fold) 
## 
## Summary of sample sizes: 14716, 14716, 14718, 14716 
## 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy   Kappa      Accuracy SD   Kappa SD   
##    2    0.9958211  0.9947139  0.0008234691  0.001041767
##   27    0.9979105  0.9973570  0.0009470856  0.001198005
##   53    0.9957702  0.9946494  0.0015906216  0.002012148
## 
## Accuracy was used to select the optimal model using  the largest value.
## The final value used for the model was mtry = 27.
```


## Calculate the errors of the Cross Validation Set.

```r
predicted <- predict(modelFit, crossValidationSet)
SampleError <- sum(predicted == crossValidationSet$classe)/nrow(crossValidationSet)
```
So the Out of Sample Error  is: 0 %


## Get the testset results and Predict testing data

```r
results <- predict(modelFit, testSet)
results
```

```
##  [1] B A B A A E D B A A B C B A E E A B B B
## Levels: A B C D E
```

PracMachineLearning_WeightLiftingExerciseAnalysis

------------------------------------------------------------
PracMachineLearning - WeightLiftingExerciseAnalysis




---
title: "pml-project"
output: Markdown file.
---


Parallel processing to improve speed.
----------------------------
I have used doParallel library and made 4 clusters to do parallel processing. Later I registered these clusters to use them for fitting algorithms.

```
library(doParallel);
rCluster <- makePSOCKcluster(4);
registerDoParallel(rCluster);
```

Source, Cleaning and Processing Data
--------------------------
Imported pml-training.csv into workspace and assigned those values to trainRawData variable. In further steps, I cleaned data by removing columns with NA's from dataset. This is done to reduce the number of insignificant columns thereby improving the processing time and accuracy.

```
trainRawData <- read.csv("pml-training.csv",na.strings=c("NA",""));

NAs <- apply(trainRawData,2,function(x) {sum(is.na(x))});
validData <- trainRawData[,which(NAs == 0)];
```
After executing above code, we have received 60 variables are predictors.


Data Partitioning for Trainind and Testing
------------------------------
To make efficient model, we need to train our model with 70% of given test data. Once model is prepared, it is always good to test it with rest 30% data to cross validate the predicted values against already existing values.

```
library(caret);
trainIndex <- createDataPartition(y = validData$classe, p=0.7,list=FALSE);
trainData <- validData[trainIndex,];
```
I have used caret createDataPartition method in caret package to split data into two sections.
* Training data - 70% of pml-training.csv
* Testing data - 30% of pml-training.csv

CreatedDataPartition functions gives indices of split data. 

Further Clearning
------------------------

According to the details mentioned in http://groupware.les.inf.puc-rio.br/har it is understood that timestamps, username and other details are not highly significant in predicting the Classe variable of the dataset. So, accordingly, I have removed the irrelevant variables using Grep function.

```
removeIndex <- grep("timestamp|X|user_name|new_window",names(trainData));
trainData <- trainData[,-removeIndex];
```

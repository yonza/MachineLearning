---
title: "How well did they do?"
output: html_document
---



```{r, echo=FALSE}

library(caret)
trainingTemp <- read.csv('training.csv')
testingTemp <- read.csv('testing.csv')
```

First certain columns were manually excluded as they were unecessary to put in the final model.  For example the ID of the participant should not be related to the quality of the exercise. From these the training and testing sets were created. 

```{r}
colUsing <- c(8,9,10,11, 37:49,60:68, 84,85,86,113:124, 151:160) #manually selecting out columns

training <- trainingTemp[,colUsing]
testing <- testingTemp[,colUsing]
```

Next the training set was partioned into a training and validation set (75% training) 

```{r}

inTrain <- createDataPartition(training$classe, p=.75, list=FALSE)

training <- training[inTrain,]
validation <- training[-inTrain,]
```

First a model fit was tried using PCA pre-processing and a random forest method. 

```{r}
modFit <- train(training$classe~.,method = 'rpart', preProcess = 'pca', data=training)
```

This led to poor model fit (below).  When triedagain without pre-processing accuracy improved 

```{r}
modFit #poor accuracy 37% only 3

modFit <- train(training$classe~.,method = 'rpart', data=training)

modFit ##accuracy of 57%
```

The following shows the predictions of the validation set:

```{r}

myPred <- predict(modFit, validation)
confusionMatrix(myPred, validation$classe)
```

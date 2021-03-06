---
title: "Machine Learning"
author: "Melissa Miller"
date: "February 13, 2018"
output:
  word_document: default
  pdf_document: default
  html_document: default
---

**Introduction**  
Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement - a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it. In this project, your goal will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways. More information is available from the website here: http://groupware.les.inf.puc-rio.br/har (see the section on the Weight Lifting Exercise Dataset)


**Goal** 
The goal of this project of the Coursera Practical Machine Learning course is to predict the manner in which people did the exercise. This is the "classe" variable in the training set.

**Report** 
This report describes:
- how this model is built
- how cross validation is used
- what the expected out of sample error 
- justification of the made choices
- Results of prediction model predicting 20 different test cases

** Reproducibility** 
This part of the report will set the working directory, load libraries and get the data.   

```{r}
# set working directory
trainingset<- read.csv("C:/Users/mloun/Desktop/Machine Learning/pml-training.csv")
testingset <- read.csv("C:/Users/mloun/Desktop/Machine Learning/pml-testing.csv")

#load Libraries 
library(caret)
library(randomForest)
library(rpart) 
library(rpart.plot)
library(RColorBrewer)
library(tree)
```

**Checking out data** 
```{r}
dim(trainingset)
dim(testingset)
```

**Cleaning Data 
In this section we will clean the data
```{r}
trainingset<-trainingset[,colSums(is.na(trainingset)) == 0]
testingset <-testingset[,colSums(is.na(testingset)) == 0]

# This will remove any data not relevant 
trainingset   <-trainingset[,-c(1:7)]
testingset <-testingset[,-c(1:7)]

#the new data set 
dim(trainingset)
dim(testingset)
head(trainingset)
head(testingset)
```
There is now a total of 53 variables with 19622 observations (training). There is a total of 53 variables and 20 0bservations in the testing set.  The next step will be cross validation in which the training will be broken down into two sections.  

**Cross Validation** 
This will allow us to split the data for validation.  
```{r}
subsamples <- createDataPartition(y=trainingset$classe, p=0.75, list=FALSE)
subTraining <- trainingset[subsamples, ] 
subTesting <- trainingset[-subsamples, ]
dim(subTraining)
dim(subTesting)
head(subTraining)
head(subTesting)
```

**Data Review** 
```{r}
plot(subTraining$classe, col="blue", main="Bar Plot of levels of the variable classe within the subTraining data set", xlab="classe levels", ylab="Frequency")
```
Looking at the plot, the data shows how A is the most popular and D is the least.  

**Model Building**
WE are going to conduct a few different models, first is a decision Tree.  

**Decision Tree** 
```{r}
model1 <- rpart(classe ~ ., data=subTraining, method="class")
prediction1 <- predict(model1, subTesting, type = "class")
rpart.plot(model1, main="Classification Tree", extra=102, under=TRUE, faclen=0)
```
Looking at the data, there is a 73% accuracy rate with a pretty narrow confidence interval. This is not a very accurate model.   
```{r}
confusionMatrix(prediction1, subTesting$classe)
```

**Random Forrest**
A random forest is out most accurate set of data.  
```{r}
model2 <- randomForest(classe ~. , data=subTraining, method="class")

prediction2 <- predict(model2, subTesting, type = "class")

confusionMatrix(prediction2, subTesting$classe)
```
Looking at this data we can see the prediction for all classes and that the overall accuracy is around .995.  we can also see the sensativity of each class, specifically each class looks to be between 95% and 98% accurate.  This is a much better model than the other.  

**Prediction** 
This section will give the prediction of the next 20 observations.  
```{r}
predictfinal <- predict(model2, testingset, type="class")
predictfinal
```# Machine-Learning-
MAchine Learning Final Project 

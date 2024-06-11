# Flight price prediction in the USA

2024 Spring, Introduction to Artificial Intelligence Final Project

## Introduction

Developing an model that can predict airline ticket prices between 16 airports in the USA.  

Using airport ATL to be an example:  

<div align="center">
  <img src="png/airlines.png" alt="airlines.png" width="600">
</div>

## Prerequisites

The requirements are listed in requirement.txt. 

You can set up your environment by using the command below:

    pip install -r requirements.txt

## Dataset

We use the dataset on kaggle: [Flight Prices](https://www.kaggle.com/datasets/dilwong/flightprices/data), it includes all of the flights from 2022-04-16 to 2022-10-05, from and to 16 airports in the USA. The original CSV file has nearly six million data, and 27 features. We select the flights on 2022-04-17 and keep the data of economy class only, and that will be a dataset including 7 thousands data. 

To clean and prepare the dataset for training and testing, we only keep the following 13 features, which are listed below with their correlation coefficients:

<div align="center">
  <img src="png/correlations.png" alt="correlations.png" width="600">
</div>

After preparing the data, we randomly separate the dataset to training data and testing data with a ratio of 8 to 2.

## Models 

### Decision Tree

Our baseline is the decision tree algorithm. It is a simple method using a single tree to sort all data points into several sections. In every round, calculate the loss and find the best point to split the dataset into at most min_samples_split groups. And this process will continue until the depth is equal to max_depth. After this process, we obtain a tree by splitting the data points into groups.

In our case, we use mean squared error to decide where to split the data. We split them into two groups in every round. Since we set the maximum depth of the tree to be none and the min_samples_split to 5, the data will be separated into several leaves and each leaf representing the prediction result under that specific situation.

### Random Forest

We want to train two models in different methods on the baseline of the decision tree, which are random forest and gradient boosting. Random forest is an advanced version of Decision tree, in the other words, forest is constructed by n_estimator random trees. To construct every tree, select a dataset randomly from the original dataset. Note that the size will be the same as the original one, but the data can be repeated, thus some of the data may not be selected. After word, apply the dataset into decision tree method and construct a forest. 

In our case, the forest contains 50 trees. To obtain the final result of prediction, we take the average of each prediction result of the trees by the “predict” function to improve accuracy.

### Gradient Boosting

Gradient boosting is a method of combining simple models to build a strong model. In this case, we use gradient boosting on the decision tree. Different from random forest algorithms, Gradient boosting algorithms focus on optimization. Instead of training trees simultaneously, this method tries to fix the error from the last tree. In particular, gradient boosting has two important main features, subsample and learning rate. There will be an initial loss value at the beginning of this process, and the residual value between current result and the real value will be calculated in every round. To optimize the upcoming training, the next tree will be trained using the residual result and learn to predict the new residual. After word, we only take part of it to update and optimize the prediction by multiplying it to learning rate. This process will repeat n_estimator times. Another main feature of gradient boosting is subsample, which is a ratio that can control the size of each sub dataset. 

In our case, we set the learning rate to 0.2, subsample to 0.8 and n_estimators to 50. Which means there will be 50 rounds of optimization, each time using a randomly selected dataset with 80 percent large of the original dataset to train, and 20 percent of the result will be adopted to optimize the prediction.

## Results

We are going to analyze the result from several different points of view. 

First, these are the RMSE, R<sup>2</sup> score and accuracy result of decision tree, random forest and gradient boosting. By comparing these data, we can see that the gradient boosting algorithm performs the best among the three algorithms, the random forest comes in second, and the decision tree is the worst. This conforms to our expectation. It is worth mentioning that the gradient boosting and the random forest algorithm has a similar result in R<sup>2</sup> score but an obvious difference in accuracy. A reasonable explanation of this phenomenon is that we only have 50 trees in both two methods, if there are more trees, the difference will be more obvious.

- Decision Tree:

  <img src="png/dt_perform.png" alt="dt_perform.png" width="280">
  
- Random Forest:

  <img src="png/rf_perform.png" alt="rf_perform.png" width="280">
  
- Gradient Boosting:

  <img src="png/gb_perform.png" alt="gb_perform.png" width="280">
  

Second, here is a graph representing the distribution of actual and predicted total fare. We stack the graph of three results together to better observe the difference. In this graph, we can see that the distribution of the decision tree is more scattered than the other methods. The distribution of gradient boosting and random forest are slightly different, but better than the decision tree.

<div align="center">
  <img src="png/compare.png" alt="compare.png" width="500">
</div>

Third, let’s consider an example case and compare the prediction to the actual result. In this case, we can observe that it is unusual that the random forest performs better than gradient boosting. The possible reason is the setting of hyper parameters, learning rate and subsamples. Since we aimed to maximize the training result, some of the single cases may exist like this, which is the satisfaction of the overall performance.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="png/test.png" alt="test.png" width="650">

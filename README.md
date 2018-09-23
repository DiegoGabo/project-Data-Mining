### Sales Forecasting

## Introduction
The purpose of this project is to create a sales forecasting model for one of Europe’s most important
retailers. In the following paper we explain our workflow which consisted of the following steps:
• Data exploration
• Data cleaning
• Feature engineering
• Dataset split
• Models training and validation
• Model selection and evaluation

## Data exploration
The provided dataset consists of 523021 instances of daily information about the stores, between
March 1st, 2016 and February 28, 2018. There are 36 features about number of sales, number of
visitors, store information, weather information and demographic information.
There are 3 categorical features (StoreType with 4 levels, AssortmentType with 3 levels, Events
with 22 levels) and 6 features with some missing values (Max_Gust_SpeedKm_h with 78% of
missing values, Events with 24%, CloudCover with 8%, and the three visibility features with 2%).
There are also some numerical features which are actually categorical, such as the Region ID
which is represented by a number between 0 and 10, or the Store ID.

## Data cleaning
We drop the Region ID. It makes no sense as a numerical variable because we don’t want it to
act as an ordering between the different regions, and there are already 3 features which describe
a region (area in km 2 , GDP, population), so these features already act as an identifier and also
provide some information.
We also drop every row where IsOpen = 0, because in that case we automatically set the num-
ber of sales to 0, independently from the other features.

# Categorical features
We convert StoreType to a numerical variable by assigning a fixed map of values between 0 and 3.
We believe this makes sense because there is an order between the 4 levels of the original categorical
variable: e.g. ’Hyper Market’ is bigger than ’Super Market’. So, in a sense, this feature would be
an indicator of how big the store is.
We convert the 3-level AssortmentType feature to 2 dummy binary features, indicating if the
store has a Non-Food Department or a Fish Department respectively. If both of these features are
0, it means that the store has a General assortment type.
The Events feature has many levels (21, not including NaN) and also many missing values
(nearly 24%).
We believe it can safely be assumed that a NaN in this feature simply means that none of
the atmospherical events (Rain, Fog, Hail, Snow, Thunderstorm) occurred. This is because when
Events is NaN the other features suggest a better weather (e.g. higher temperature and visibility)
than when Events is not NaN.
We convert this feature in 5 boolean features that are active when the corresponding atmo-
spherical event happened.

# Missing values
We decided to impute the missing values from the other features with Random Forest with default
parameters.

## Feature engineering
The features we added are:
• IsSaturday and IsSunday, signaling if the day is a Saturday or a Sunday;
• WasOpenYesterday and IsOpenTomorrow, signaling if the store was open yesterday or is
open tomorrow;
• MeanMonthSales, which is the mean number of sales of all the stores for that month;
• StdMonthSales, which is the standard deviation of the number of sales of all the sotres fot
that month;
• MeanStoreSales, which is the mean number of sales for that store;
• StdStoreSales, which is the standard deviation of the number of sales for that store;
• MeanRegionSales, which is the mean of numer of sales of all the stores of that region;
• StdRegionSales, which is the standard deviation of number of sales of all the stores of that
region;
• OrdinalDate, number between 0 and 364 which indicates the day of the year.

## Dataset split
After cleaning the dataset,we have divided it into three subsets:
• val1, used to calculate the model’s hyperparameters and make a first features selection;
• val2, used to select the best model;
• training set, used to train the calculated models.
2We firts have created val2 using all the datas related to the last 2 months (Gianuary and
February 2018) to have a validation set as similar as possible to the future test set, val1 extracting
10% of the data randomly and we have left the rest of the datas for the training set.
Then we decided to anlyze all our models using a val1 made of two consecutive months to have
a similar situation in va1, val2 and the future test and we noticed that the performace improved
in a significant way, so the three final dataset are as follows:
• val1: from 01/11/2017 to 31/12/2017
• val2: from 01/01/2018 to 28/02/2018
• training set: from 01/03/2017 to 31/10/2017

## Models training and validation

# Analyzed models
We decided to analyze four different models:
• RANDOM FOREST
• EXTREMELY RANDOMIZED TREES
• XGBOOST
• MULTIPLE LINEAR REGRESSION

# Generated models
To optimize the trees we have firts performed a feature selection based on the feature importance.
This was calculated with the tool SelectFromModel from the library sklearn.feature_selection.
After the feature selection we tuned the hyperparameters calculating the error on a validation
set. We saw that for random forest and extremely randomize trees, the featurest selection led to
a significant improvement, but it doesent happened in XGBOOST, so for this model there is no
previus feature selection.
RANDOM FOREST
• number_of_attributes: 13
• number_of_freatures: 4
• depht: 15
• number_of_estimators: 250
EXTREMELY RANDOMIZED TREES
• number_of_attributes: 12
• number_of_features: 3
• depht: 22
• number_of_estimators: 250
3XGBOOST
• subsample: 0.6
• η: 0.3
• colsample_bytree: 0.6
• min_child_weight: 5
• max_depht: 15
• number_of_estimator: 250
MULTIPLE LINEAR REGRESSION
• loss_function: intensive
• learning_rate: optimal
• α: 1.10 6
• b: 0.0325

## Results
According to the given error formula the errors on val2 for each model are:
• RANDOM FOREST: 0.046836707570122794
• EXTREMELYRANDOMIZE TREES:0.04787037272346659
• XGBOOST: 0.06101349388459094
• MULTIPLE LINEAR REGRESSION: 0.146891
So we decide to use the Random Forest model to predict the test data.

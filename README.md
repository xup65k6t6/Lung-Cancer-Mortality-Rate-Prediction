# Lung-Cancer-Mortality-Rate-Prediction

# Introduction
Using different machine learning algorithms to predict lung cancer mortality rate in United States. 

# Datasets

# Language and libraries
## Language: Python
## Libraries
* Data Manipulation: pandas, numpy
* Data Visualization: matplotlib, seaborn, graphviz
* Statistics: statsmodels, patsy(dmatrices)
* Machine Learning: sklearn, xgboost

# Data Preprocessing
## Convert FIPS column to correct format
Federal Information Processing Standard or FIPS is a categorical variable. It is a code with five digits. The left two digits showing the state and the three right digits showing the county code.

## Deal with Unexpected values
* **State** : should be of categorical type for saving memory when the data set is large. However, because the given data set is small, it is an optional step and we are not performing data type conversion.
* **Incidence_Rate** : should be float. However, there are some weird data input such as *, _, and __. Also, some numbers come with #, and we will deal with it later.
* **Avg_Ann_Incidence** : should be integer. However, there are some weird data input such as '3 or fewer', _, and __.
* **Recent_Trend** : should be categorical. However, there are some weird data input such as *, _, and __.
* **Mortality_Rate** : should be float. However, there are some weird data input such as *.
* **Avg_Ann_Deaths** : should be integer. However, there are some weird data input such as *. Also some numbers contain a comma, which should be removed.

The '#' values following number values in Incidence_Rate (such us "84 #") are expected values possibly because of data collection issues.
On the other hand, the \* value in the Incidence Rate and Mortality Rate columns signifies '16 or fewer' cases. This could be because in the counties with very few reported cases, a specific number could not be attributed and hence, a non-numeric placeholder was used.

## Deal with missing value in explanatory/ dependent variable
* Remove columns with over 20% of missing value
* Impute

## Deal with missing value (\*) in response variable (Mortality_Rate)
* Check the distribution of missing values
* Final decision: remove all * values

## Merge the population data to the main dataframe

# Feature Engineering
## Convert population number to rates by population for each county
Some of the numerical variables are dependent on the populations, so it would be helpful for algoritgns by converting all the raw data to per 100,000 persons rates (divide by population and multiply by 100,000).

# EDA (Exploratory Data Analysis)
## Visualize correlation between features
\ insert heatmap

# Feature Selection
Selected features

* All_Poverty: removed highly correlated features such as 'M_Poverty', 'F_Poverty', 'M_With', 'M_Without', 'F_With', 'F_Without', 'All_With', 'All_Without', 'Avg_Ann_Incidence', 'Avg_Ann_Deaths', 'POPESTIMATE2015'.
* Also, Poverty is a good indicator for predicting mortality rate since it also indicates degree of access to healthcare
* Med_Income: removed highly correlated features such as 'Med_Income_White'.
* Income is another variable that could indicate access to better treatments, proper diagnosis, among other factors
* Incidence_Rate: Incidence rate has a positive linear relationship with the mortality rate, hence it is an important feature
* All_Poverty_per100K: removed highly correlated features such as 'M_Poverty_per100K', 'F_Poverty_per100K'
* All_With_per100K: removed highly correlated features such as 'M_With_per100K', 'F_With_per100K'
* All_Without_per100K: removed highly correlated features such as 'M_Without_per100K', 'F_Without_per100K'

\insert heatmap & pairplot

# Model Selection
## Linear Regression
* Simple Linear Regression
* Ridge Regression
* Lasso Regression
### Check VIF (Variance Inflation Factor)
\ insert table
## Non-linear Algorithms
* Random Forest
* XGB Regressor

# Evaluation
* MSE
* R-squared
* MAE
\ insert table

# Potential future improvement
* semi-supervised learning

# Take away



# Lung Cancer Mortality Rate Prediction (Linear Regression, XGBoost)


# Introduction
The goal of the project is to predict lung cancer mortality rate in United States. Note that it is to predict probability of lung cancer rate in each county instead of prediction for each person. This project is not a classification but regression problem.


# Datasets
* 20+ variables related to each county and lung cancer mortality rate (y variable). Total 3,134 rows.
* After data cleaning, there are 2,809 rows left.


# Language and libraries
**Language** : Python


**Libraries** : 
* Data Manipulation: pandas, numpy
* Data Visualization: matplotlib, seaborn, graphviz
* Statistics: statsmodels, patsy(dmatrices)
* Machine Learning: sklearn, xgboost


# Data Preprocessing
## Convert FIPS column to correct format
Federal Information Processing Standard or FIPS is a categorical variable. It is a code with five digits. The left two digits showing the state and the three right digits showing the county code. (e.g. 02013)

In raw data, it is numeric and not five-digits numbers. So, it is required to convert it to five digits values. (e.g. 2013 -> 02013)


## Merge the population data to the main dataframe


## Deal with missing value in explanatory/ dependent variable
* Remove columns with over 20% of missing values -- 'Med_Income_Black' 'Med_Income_Nat_Am' 'Med_Income_Asian'
 'Med_Income_Hispanic'
 

## Deal with missing value (\*) in response variable (Mortality_Rate)

\insert pic 	
![test] (/assets/images/comparison of all poverty ratio.png)

* **Insights** : Data with "\*" value in the Mortality Rate column (10.37% of overall data) shows -
  1. only a little bit lower poverty ratio 
  2. no significant difference from the overall median income
  3. fewer people having health insurance (and the distribution is not normal)
  4. in the columns which are not preprocessed yet such as Incidence_Rate, Avg_Ann_Incidence, Recent_Trend, Avg_Ann_Deaths, this data (* values) has 8+% of meaningless data, such as _ and __)
* **Final decision** : remove all * values from the Mortality_Rate Column


## Deal with Unexpected values and data types
* **State** : should be of *categorical* type for saving memory when the data set is large. However, because the given data set is small, it is an optional step and we are not performing data type conversion.
* **Incidence_Rate** : 168 non-numeric data (163 _ & __ + 5 *). After calculating the Avg_Ann_Incidence and POPESTIMATE2015 for the 5 * values, we impute the missing values for the 163 data points with the median
* **Avg_Ann_Incidence** : 163 non-numeirc data (_ &__). has outliers. impute with the median
* **Recent_Trend** : convert to *categorical*. However, there are some weird data input such as *, _, and __.
* **Mortality_Rate** : should be *float*. However, there are some weird data input such as *.
* **Avg_Ann_Deaths** : convert to *numerical*

* Potential Reasons for unexpected values
  * The '#' values following number values in Incidence_Rate (such us "84 #") are expected values possibly because of data collection issues.
  * On the other hand, the \* value in the Incidence Rate and Mortality Rate columns signifies '16 or fewer' cases. This could be because in the counties with very few reported cases, a specific number could not be attributed and hence, a non-numeric placeholder was used.

For now the summary of dataframe looks like as below.

\insert pic

# Feature Engineering
* Convert population number to rates by population for each county
Some of the numerical variables are dependent on the populations, so it would be helpful for algoritgns by converting all the raw data to per 100,000 persons rates (divide by population and multiply by 100,000).

\insert pic

# EDA (Exploratory Data Analysis) and Feature Selection
* Visualize correlation between all features

\insert heatmap

  * There are some features are highly correlated with each other, such as All_Poverty, M_Poverty, and F_Poverty. Removing those correlation can help reduce colinearity in linear regression models. So, I removed columns with correlation over 0.85 and kept the column having highest correlation with y variable (Mortality_Rate)

* Visualize correlation after removing high correlation (>= 0.85) & check pairplot

\insert heatmap & pairplot

  * The heatmap looks good now.
  * However, one concern in the pairplot is that All_Poverty looks like an exponential distribution and thus transformation is needed.

* Add categorical variable Recent_Trend and draw a heatmap again

\insert heatmap

  * There is no strong correlation now.

# Model Build-up
* Split Data

* Pipeline
 * Feature pipeline
  * Drop features
  * Numerical features
  * Categorical features
 * Model pipeline: After building up feature pipelines, I connected them to the model or algorithms, so I don't need to do the same task again on test data set.

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



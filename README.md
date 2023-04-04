# Lung Cancer Mortality Rate Prediction (Linear Regression, XGBoost)


# Introduction
The goal of the project is to predict the lung cancer mortality rate in the United States. Note that it is to predict the probability of lung cancer rate in each county instead of prediction for each person. This project is not a classification but a regression problem.


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
Federal Information Processing Standard or FIPS is a categorical variable. It is a code with five digits. The left two digits show the state and the three right digits show the county code. (e.g. 02013)

In raw data, it is numeric and not five-digit numbers. So, it is required to convert it to five digits values. (e.g. 2013 -> 02013)


## Merge the population data to the main dataframe


## Deal with missing value in explanatory/ dependent variable
* Remove columns with over 20% of missing values -- 'Med_Income_Black' 'Med_Income_Nat_Am' 'Med_Income_Asian'
 'Med_Income_Hispanic'
 

## Deal with missing value (\*) in response variable (Mortality_Rate)

* **Insights** : Data with "\*" value in the Mortality Rate column (10.37% of overall data) shows -
  1. only a little bit lower poverty ratio 
![](/assets/images/comparison_of_all_poverty_ratio.png)
  2. no significant difference from the overall median income
![](/assets/images/comparison_of_median_income.png)
  3. fewer people have health insurance (and the distribution is not normal)
![](/assets/images/comparison_of_having_health_insurance.png)
  4. in the columns which are not preprocessed yet such as Incidence_Rate, Avg_Ann_Incidence, Recent_Trend, and Avg_Ann_Deaths, this data (* values) has 8+% of meaningless data, such as _ and __)
* **Final decision** : remove all * values from the Mortality_Rate Column


## Deal with Unexpected values and data types
* **State** : should be of *categorical* type for saving memory when the data set is large. However, because the given data set is small, it is an optional step and we are not performing data type conversion.
* **Incidence_Rate** : 168 non-numeric data (163 _ & __ + 5 *). After calculating the Avg_Ann_Incidence and POPESTIMATE2015 for the 5 * values, we impute the missing values for the 163 data points with the median
* **Avg_Ann_Incidence** : 163 non-numeirc data (_ &__). has outliers. impute with the median
* **Recent_Trend** : convert to *categorical*. However, there is some weird data inputs such as *, _, and __.
* **Mortality_Rate** : should be *float*. However, there are some weird data input such as *.
* **Avg_Ann_Deaths** : convert to *numerical*

* Potential Reasons for unexpected values
  * The '#' values following number values in Incidence_Rate (such as "84 #") are expected values possibly because of data collection issues.
  * On the other hand, the \* value in the Incidence Rate and Mortality Rate columns signifies '16 or fewer' cases. This could be because in the counties with very few reported cases, a specific number could not be attributed and hence, a non-numeric placeholder was used.

For now, the summary of dataframe looks like as below.

![](/assets/images/df_info_b4_engineering.png)

# Feature Engineering
* Convert population number to rates by population for each county
Some of the numerical variables are dependent on the populations, so it would be helpful for algorithms by converting all the raw data to per 100,000 person rates (divide by population and multiply by 100,000).

![](/assets/images/df_info_after_engineering.png)

# EDA (Exploratory Data Analysis) and Feature Selection
* Visualize the correlation between all features

![](/assets/images/heatmap_of_all_features.png)

Some features are highly correlated with each other, such as All_Poverty, M_Poverty, and F_Poverty. Removing those correlations can help reduce colinearity in linear regression models. So, I removed columns with correlations over 0.85 and kept the column having the highest correlation with the y variable (Mortality_Rate)

* Visualize correlation after removing high correlation (>= 0.85) & check pairplot

![](/assets/images/heatmap_after_remove_colinearity.png)

![](/assets/images/pairplot.png)

The heatmap looks good now.

However, one concern in the pairplot is that All_Poverty looks like an exponential distribution, and thus transformation is needed.

* Add categorical variable Recent_Trend and draw a heatmap again

![](/assets/images/heatmap_after_adding_categorical.png)

There is no strong correlation now.

# Model Build-up
* Split Data: 80.1% training set, 9.9 validation set, 10% test set

* Pipeline
 * Feature pipeline: use the pipeline to connect data preprocessing among different features
  * Drop features
  * Numerical features: simple impute to the median (although we have done this by hand before)
  * Special numerical feature: for the All-Poverty feature, I noticed it would be better to do a log transformation
  * Categorical features: simple impute to most frequent value, and do one-hot encoding
 * Model pipeline: After building up feature pipelines, I connected them to the model or algorithms, so I don't need to do the same task again on the test data set.

# Model Selection
## Linear Regression
* Simple Linear Regression
* Ridge Regression
* Lasso Regression

Among all linear regressions, the simple linear regression has the best performance, while the other two have similar results in terms of MSE, MAE, and R-squared.

![](/assets/images/linear_regression_comparison.png)

The reason is that I have already filtered out other features, so the function of selecting features in Ridge and Lasso algorithms cannot perform well here. Also, the hyperparameter lambda in Ridge and Lasso regression will affect the key features a little bit, leading to slightly worse performance.

### Check VIF (Variance Inflation Factor)
Larger values of VIF indicate multicollinearity. VIF values greater than 10 indicate that a predictor variable is to a large extent dependent on other predictor variables, and this can lead to unstable and misleading regression coefficients and inflated R-squared values

There is no multicollinearity in our feature set since all the VIF values are in the range of 1-4, which is acceptably low. Hence, there is no need to refit the model.

In order to get a better sense of the R-squared value in presence of multicollinearity, you can use the adjusted R-squared metric that penalizes the presence of multicollinearity in the model. Since the VIF values here are in an acceptable range, I am not performing this step as the high R squared is not due to the presence of multicollinearity but indeed represents the truly explained variance.

![](/assets/images/VIF.png)

## Non-linear Algorithms
* Random Forest
* XGB Regressor

Ensemble learning models such as Random Forest and XGBoost models can perform better since they can model complex non-linear relationships that regression models cannot.

Also, XGBoost can work with missing values in the data without the need for manual imputation. Since currently we have removed all the rows from the data where the Mortality Rate is missing, using XGBoost can prove to be very useful here.

The performance of Random Forest v.s. XGBoost :

![](/assets/images/RF_vs_XGB.png)

(P.S. The first row is random forest. & Parameters in both models have been tuned.)

So, I decide to use XGBoost as the final model.

# Evaluation
* MSE
* R-squared
* MAE

Compare the best results from linear and non-linear regression:

Simple Linear Regression v.s. XGBoost Regression

![](/assets/images/best_linear_result.png) 
![](/assets/images/best_nonlinear_result.png)

XGBoost is still the best model so far for this problem.

# Potential future improvement
- Semi-supervised learning: when imputing the missing value, doing semi-supervised learning may be helpful. It can prevent losing information (remove missing value rows) and still relatively maintain accuracy.
- Correctly tung hyperparameter: when tuning the hyperparameter in XGBoost, I accidentally guess the best parameters and did not put a wider range into searching CV loop. So, it is possible to have a higher score if I tune the hyperparameters correctly.

# Takeaways
- Clean dirty and missing data from the scratch
- Use pipeline to connect every step from imputing to training models
- Tune hyperparameter
- Deploy Ridge, Lasso, Random Forest, and XGBoost algorithms for the first time


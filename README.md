# Predicting Election Results With Census Data

In this project I utilize categorical models to predict whether a congressional district voted for the Republican Party's presidential candidate in 2012 and 2016 based on US census data. 


The following files/folders are included in this repository:

	1. Census_Data_API_Work.ipynb 
	2. Classification_Models _Previous_Results.ipynb 
	3. Classification_Models _Census_Data_Only.ipynb
	4. congressional_data_and_targets.csv 
	5. election_results.csv 
	6. Images
	7. comparison_2020 - The updated results for the 2020 Election


The process for this project was as follows:

1. Obtain demographic data and presidential election results for each congressional district from the US Census API

2. Set up the data frame, perform EDA, and create engineered features

3. Perform categorical models on the data frame

4. Summarize the results


## Part 1: Obtain the data

The API calls and data frame creation are contained in the file ‘Census_Data_API_Work.ipynb’. I utilized the US Census Bureau’s API to obtain my data. There are several surveys available on their API, I chose to use the annual version of the “American Community Survey” (ACS) for my analysis. The 2011 and 2015 surveys were published in October of 2012 and 2016, respectively. This is the most current and readily available data prior to Election Day in November.

National election results are reported on the precinct level (a precinct can be as small as a few city blocks or as large as an entire county). I performed my analysis on the congressional district level because the census API has demographic information readily available at this level. The county level API calls return incomplete information for less than half of US counties. The US Census API contains data from 2010 forward, which I why this project only utilizes data points from the last two elections. 

I obtained the congressional district election results from an excel file by Stephen Wolf, a reporter for the Daily Kos. The election results are stored in the file titled “election_results.csv”.  The file “congressional_data_and_targets.csv” contains the merged 2016 and 2012 election results and census data.

I then created a unique identifier for each congressional district by election year for differentiation purposes. Lastly I removed the 14 districts that returned ‘NaN’ values in the election results columns. These districts became obsolete between the 2012 and 2016 elections and therefore had no values in the target columns.


## Part 2: Set up the data frame, perform EDA, and create engineered features

The two notebooks “Classification_Models_Previous_Results.ipynb” and “Classification_Models _Census_Data_Only.ipynb” contain the data frame setup, EDA and modeling process for my analysis, both with and without the congressional district’s previous election cycle results. Each file follows the exact same format and is separated into twenty sections. Eight models were performed in each file on two different databases (both on the normal data frame and the one with engineered features). The sole difference between the two notebook files is that the data frames within Classification_Models_Previous_Results.ipynb” contain two columns representing the percentage of votes the district casted for the Democratic and Republican presidential candidates in the previous election cycle. I will point out the difference between the two notebooks and data frames below where necessary.

I begin by loading the necessary libraries, packages and data frame. There are 860 total rows that correspond to a congressional district and a specific election year (either 2012 or 2016). There are 435 total congressional districts; five of these districts correspond to US territories who do not vote in US presidential elections (430 voting districts * 2 electoral years = 860 total observations for this study - https://www.census.gov/prod/cen2010/briefs/c2010br-08.pdf). I then create a Boolean target variable (**Section 1**). If the district voted for the Republican candidate for president the target value is equal to 1.

In **Section 2** I perform my Exploratory Data Analysis (EDA). I first run a correlation matrix of the 46 features and target variable. In the upper-right hand corner of the correlation matrix below (from the notebook with previous electoral history) is a histogram of the target variable.

![Correlation Matrix Of Features And Target Histogram](/Images/correlation_matrix_and_histogram.png)

According to the correlation matrix, there are few features with a high positive correlation and voting for the Republican Party (the scale maxed out at 0.25), however there are several features with a correlation of -0.6. (*Please note that because previous electoral history is included below the scale reaches negative one*). Among the strongest positively correlated features with districts and voting for the Republican Party are: total veterans, total married households, total white citizens, total native born residents, total workers in the agriculture field, and total workers in the manufacturing industry. Among the strongest negatively correlated features with districts and not voting for the Republican Party are: total non-citizens, total foreign-born residents and median gross rent.

I then ran a box and whiskers plot of each continuous feature to view how far spread out each one was from its median. 

![Box And Whiskers Plot Of Continuous Features](/Images/features_boxplot_whiskers.png)

Various demographic features are far more spread out than others. For instance, the inner quartile range of the feature total white residents is much more spread out than others. This is mostly due to a few extreme outliers.

I then proceed to feature engineering in **section 3**. I begin by normalizing my data. For instance I divide demographic data by the total population to put them in percentage format. I create two data frames: one with the standard features as is and one with my engineered features, described below. I created Boolean columns for each industry’s worker count. If the congressional districts workers are employed by more than ten percent in an industry, then the Boolean feature is equal to 1. I then drop the column that these threshold features were derived from. I run each data frame (standard features and engineered features) in each notebook through all eight models.


## Part 3: Perform categorical models on the data frame

The remaining sections (**section 4 – 20**) contain the categorical models. Eight models were performed in total for both data frames in each notebook. The models are listed below:

	1. Initial logistic regression – default parameters
	2. Logistic regression - grid search
	3. Logistic regression - grid search with polynomial features
	4. K-nearest neighbors - grid search
	5. Decision Tree – no limits
	6. Decision Tree – grid search
	7. Random forest
	8. XGBoost

The results are summarized in the table below:

![Evaluation Of Models Results Summary](/Images/evaluation_of_models_results_summary.png)

All eight models were performed on each data frame in each notebook. The results table is broken up into four categories: (1) models performed with census data and the standard features data frame, (2) models performed with census data and the engineered features data frame, (3) models performed with census data, previous presidential election results, and the standard features data frame, (4) models performed with census data, previous presidential election results, and the engineered features data frame. The scores highlighted in green represent the highest performing model in each category and the scores highlighted in red represent the lowest performing model in each category, according to their F1 scores.

Each model allocated the 860 congressional districts between training and testing data sets. The test size for each model was set to 20% with the exception of the first logistic model (25%). As expected, every model performed better on their training data than their test data set, the percent difference is highlighted in orange in the column titled ‘% Delta (Test-Train)’. The column ‘Delta Models (E-S)’ appears twice and lists the improvement in F1 and accuracy score for each model between the engineered and standard data frame. The far right column titled ‘Model Comparison’ lists the percentage improvement the inclusion of each congressional district’s previous presidential electoral results has on the models; a column is included for the data frames with standard features and engineered features. The range of improvement in the F1-scores is between 18.70% and 6.88%. I list the top performing models below (Please open the table in a new window to view it clearly):

	1. Census Data Only – Standard Features: XGBoost (F1: 0.8380)
	2. Census Data Only – Engineered Features: Logistic Regression GS (F1: 0.8360)
	3. Census and Previous Election Results Data – Standard Features: Random Forest (F1: 0.9341)
	4. Census and Previous Election Results Data – Engineered Features: XGBoost (F1: 0.9348)


The breakdown for each model is listed below. The confusion matrices are from the notebook with previous electoral history because these were the best performing models:


### Model 1: Initial logistic regression – default parameters

This logistic model was performed with default parameters (the test data set default is 25% of the total data)

#### Standard Features:

![Logistic Regression Confusion Matrix For Standard Features](/Images/1_lm1_cm.png)

#### Engineered Features:

![Logistic Regression Confusion Matrix For Engineered Features](/Images/2_lm2_cm.png)


### Model 2: Logistic regression – grid search

This logistic model was performed with grid search. I include the feature importance charts for the models with and without the presidential results for the previous electoral cycle. The lasso regression method performed better than ridge regression, which is why features were eliminated.

#### Standard Features:

##### Feature Importance With Previous Electoral History Included:

![Feature Importance For Logistic Regression With Standard Features and Previous Electoral History and Grid Search](/Images/3_lm3_features.png)

Above is a graph of the top features coefficients by magnitude. Only nine features were kept by lasso regression. The absolute values of the features are:

1) 'Previous_Presidential_R', 4.5306
2) 'Median_Gross_Rent', -0.4136
3) 'Total_Non_Citizens', -0.3184
4) 'Total_Working_Finance', -0.1391
5) 'Total_Wealthy_Households', -0.1122
6) 'Total_Working_Retail', 0.1046
7) 'Total_Working_Manufacturing', 0.0773
8) 'Total_Mexican_Origin', -0.0731
9)'Previous_Presidential_D', -0.0261

##### Feature Importance Without Previous Electoral History Included:

![Feature Importance For Logistic Regression With Standard Features Without Previous Electoral History and Grid Search](/Images/3_lm3_features_census_only.png)

Above is a graph of the top features coefficients by magnitude. The absolute values of the features are:

1) 'Total_Mexican_Origin', -1.2546
2) 'Total_Married_Households', 1.2221
3) 'Total_Non_Citizens', -0.8751
4) 'Total_Less_Highschool', 0.7684
5) 'Below_Poverty_Level_LTM', -0.7166
6) 'Median_Age', -0.6445
7) 'Total_Business_Degree', 0.6438
8) 'Total_Workers', -0.5867
9) 'Total_White_People', 0.5601
10) 'Median_Income', -0.5047

Only the features 'Total_Mexican_Origin' and 'Total_Non_Citizens' remain in each of the above models. The previous percentage of votes cast for the Republican Party is by far the most important feature with whether or not the district voted for the Republican presidential candidate in the next election cycle. Votes cast for the Democratic presidential candidate was not removed by Lasso regression because of votes cast for independent voters. The stronger the independent candidate the more the model weighed the feature 'Previous_Presidential_D'.

##### Confusion Matrix For Logistic Model With Grid Search – Standard Features:

![Logistic Regression Confusion Matrix For Standard Features and Grid Search](/Images/3_lm3_cm.png)

Best Parameters: Penalty: l1, C: 0.1

#### Engineered Features:

##### Feature Importance With Previous Electoral History Included:

![Feature Importance For Logistic Regression With Engineered Features and Previous Electoral History and Grid Search](/Images/4_lm4_features.png)

Above is a graph of the top features coefficients by magnitude. The absolute values of the features are:

1) 'Previous_Presidential_R', 7.4916
2) 'Ten_Percent_Retail', 0.6028
3) 'Median_Gross_Rent', -0.5489
4) 'Total_Mexican_Origin', -0.4979
5) 'Total_Bachelor_Holders', -0.4103
6) 'Gini_Index', -0.3419
7) 'Total_Non_Citizens', -0.3029
8) 'Total_Wealthy_Households', -0.2109
9) 'Median_Age', 0.1272
10) 'Total_Puerto_Rican_Origin', -0.1253

##### Feature Importance Without Previous Electoral History Included:

![Feature Importance For Logistic Regression With Engineered Features Without Previous Electoral History and Grid Search](/Images/4_lm4_features_census_only.png)

Above is a graph of the top features coefficients by magnitude. The absolute values of the features are:

1) 'Total_Married_Households', 0.9355
2) 'Total_Mexican_Origin', -0.7888
3) 'Total_Business_Degree', 0.3869
4) 'Below_Poverty_Level_LTM', -0.3829
5) 'Total_Labor_Force_Eligible', -0.3693
6) 'Ten_Percent_Retail', 0.3247
7) 'Total_Non_Citizens', -0.3246
8) 'Total_Workers', -0.3220
9) 'Total_White_People', 0.3176
10) 'Median_Income', -0.3123

Only the features 'Ten_Percent_Retail', 'Total_Mexican_Origin' and 'Total_Non_Citizens' remain in each of the above models. Like before, the previous percentage of votes cast for the Republican Party is by far the most important feature. In contrast with the standard features, votes cast for the Democratic presidential candidate was removed by Lasso regression.


##### Confusion Matrix For Logistic Model With Grid Search – Engineered Features and Previous Electoral Results:

![Logistic Regression Confusion Matrix For Engineered Features and Grid Search](/Images/4_lm4_cm.png)

This was the best performing model in the notebook with census data only on the engineered features data set. The F1-score is 0.8360 and the accuracy score is 0.8198 for this model.

Best Parameters: Penalty: l1, C: 0.5


### Model 3: Logistic regression – grid search and polynomials

This logistic model was performed with polynomials to the third degree included in the grid search

#### Standard Features:

![Logistic Regression Confusion Matrix For Standard Features](/Images/5_lm5_cm.png)
Best parameters: C: 0.5, Penalty: l1, Solver': liblinear

#### Engineered Features:

![Logistic Regression Confusion Matrix For Engineered Features](/Images/6_lm6_cm.png)
Best Parameters: C: 0.5, Penalty: l1, Solver: liblinear


### Model 4: K-Nearest Neighbors – grid search and polynomials

K-Nearest neighbors is the worst performing model for each data set for each both models that included previous electoral history and the engineered data set of the model that utilized census data only.

#### Standard Features:

![ K-Nearest Neighbors Confusion Matrix For Standard Features](/Images/7_knn1_cm.png)
Best Parameters: N-neighbors: 11, knn__weights: uniform

#### Engineered Features:

![K-Nearest Neighbors Confusion Matrix For Engineered Features](/Images/8_knn2_cm.png)
Best Parameters: N-neighbors: 11, knn__weights: uniform


### Model 5: Decision trees without limits

Without limits these decision trees are over fit to the training data and therefore poorly predicted their test set (high variance).

#### Standard Features:

![Decision Tree For Standard Features](/Images/DT_model_1.png)

#### Engineered Features:

![Decision Tree For Engineered Features](/Images/DT_model_2.png)


### Model 6: Decision trees with grid search

Using grid search these trees had a maximum depth and minimum leaf size boundary. These simpler trees performed better on their test data than the trees without limits.

#### Standard Features:

![Decision Tree For Standard Features and Grid Search](/Images/DT_model_3.png)
Best Parameters: 'criterion': 'entropy', 'max_depth': 2, 'min_samples_leaf': 80

#### Engineered Features:

![Decision Tree For Engineered Features and Grid Search](/Images/DT_model_4.png)
Best Parameters: 'criterion': 'gini', 'max_depth': 3, 'min_samples_leaf': 20


### Model 7: Random Forest

Random forest performed best on the standard features data frame with previous electoral history included. The best parameters are listed below.

#### Standard Features:

Best Parameters: 'max_depth': 5, 'max_features': 0.25, 'min_samples_leaf': 0.03, 'n_estimators': 200

#### Engineered Features:

Best Parameters: 'max_depth': 5, 'max_features': 0.25, 'min_samples_leaf': 0.04, 'n_estimators': 300


### Model 8: XGBoost

XGBoost performed best on the engineered features data frame with previous electoral history included and the standard features data frame with only census data. The best parameters are listed below; they were the same for each model.

#### Standard Features:

Best Parameters: learning_rate: 0.1, max_depth: 6, min_child_weight: 10, n_estimators: 30, subsample: 0.7

#### Engineered Features:

Best Parameters: learning_rate: 0.1, max_depth: 6, min_child_weight: 10, n_estimators: 30, subsample: 0.7


## Part 4: Key Takeaways

The XGboost, Random Forest and Logistic Regression models with grid search performed best on the data set. K-Nearest Neighbors scored the lowest F1 score.

The percentage of votes cast for the previous Republican presidential candidate improved my models’ F1 scores between 6.88% and 18.70%. Without the previous vote included the models were still able to perform well on census data alone. The features that mattered the most after electoral history was removed were mostly about income, industry employment, education and country of origin. Only the race based feature 'Total_White_People' remained in the top ten features when previous electoral history was removed. Although census data can predict the congressional district’s voting patterns fairly well, what each political party represents and the presidential candidate of the political party account for the small inaccuracies of these models. In swing states these can cause slight differences in the popular vote, which will lead to major differences in the electoral votes.

## Part 5: Updates for the 2020 Election



### Results:

The updated election results come from David Nir's [article](https://www.dailykos.com/stories/2020/11/19/1163009/-Daily-Kos-Elections-presidential-results-by-congressional-district-for-2020-2016-and-2012). As of February 16, 2021 there are a handful of counties that have not yet reported complete reults of the 2020 election online, these will effect the results of the models.

![Evaluation Of Models Results for 2020 Election Summary](/comparison_2020/images/evaluation_of_models_results_summary_2020.png)

**Standard Features - Census Data Only**: As before, the Descision Tree model without level limits has the lowest F1 score, but the logictic regression model with polynomials has the highest F1 score (previously the XGBoost model was highest).
**Engineered Features - Census Data Only**: The decision trees model with limits has the lowest F1 score (previously the K Nearest Neighbors model with standard features was lowest), the logictic regression model with polynomials has the highest F1 score (previously the logictic regression model with standard features was highest).

**Standard Features - Census Data Only**: The K Nearest Neighbors model has the lowest F1 score (as previously), the XGBoost model has the highest F1 score (previously the random forest model was highest).
**Engineered Features - Census Data Only**: As before the K Nearest Neighbors model has the lowest F1 score and the XGBoost model has the highest F1 score.

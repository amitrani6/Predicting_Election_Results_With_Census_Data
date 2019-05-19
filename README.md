# Predicting Election Results With Census Data

In this project I utilize categorical models to predict whether a congressional district voted for the Republican Party's presidential candidate in 2012 and 2016 based on US census data. 


The following files/folders are included in this repository:

	1. Census_Data_API_Work.ipynb 
	2. Classification_Models _Previous_Results.ipynb 
	3. Classification_Models _Census_Data_Only.ipynb
	4. congressional_data_and_targets.csv 
	5. election_results.csv 
6. Images


The process for this project was as follows:

1. Obtain demographic data and presidential election results for each congressional district from the US Census API

2. Set up the data frame, perform EDA, and create engineered features

3. Perform categorical models on the data frame

4. Summarize the results


### Part 1: Obtain the data

The API calls and data frame creation are contained in the file ‘Census_Data_API_Work.ipynb’. I utilized the US Census Bureau’s API to obtain my data. There are several surveys available on their API, I chose to use the annual version of the “American Community Survey” (ACS) for my analysis. The 2011 and 2015 surveys were published in October of 2012 and 2016, respectively. This is the most current and readily available data prior to Election Day in November.

National election results are reported on the precinct level (a precinct can be as small as a few city blocks or as large as an entire county). I performed my analysis on the congressional district level because the census API has demographic information readily available at this level. The county level API calls return incomplete information for less than half of US counties. The US Census API contains data from 2010 forward, which I why this project only utilizes data points from the last two elections. 

I obtained the congressional district election results from an excel file by Stephen Wolf, a reporter for the Daily Kos. The election results are stored in the file titled “election_results.csv”.  The file “congressional_data_and_targets.csv” contains the merged 2016 and 2012 election results and census data.

I then created a unique identifier for each congressional district by election year for differentiation purposes. Lastly I removed the 14 districts that returned ‘NaN’ values in the election results columns. These districts became obsolete between the 2012 and 2016 elections and therefore had no values in the target columns.

### Part 2: Set up the data frame, perform EDA, and create engineered features

The two notebooks “Classification_Models_Previous_Results.ipynb” and “Classification_Models _Census_Data_Only.ipynb” contain the data frame setup, EDA and modeling process for my analysis, both with and without the congressional district’s previous election cycle results. Each file follows the exact same format and is separated into twenty sections. Eight models were performed in each file on two different databases (both on the normal dataframe and the one with engineered features). The sole difference between the two notebook files is that the data frames within Classification_Models_Previous_Results.ipynb” contain two columns representing the percentage of votes the district casted for the Democratic and Republican presidential candidates in the previous election cycle. I will point out the difference between the two notebooks and dataframes below where necessary.

I begin by loading the necessary libraries, packages and dataframe. There are 860 total rows that correspond to a congressional district and a specific election year (either 2012 or 2016). There are 435 total congressional districts; five of these districts correspond to US territories who do not vote in US presidential elections (430 voting districts * 2 electoral years = 860 total observations for this study - https://www.census.gov/prod/cen2010/briefs/c2010br-08.pdf). I then create a Boolean target variable (**Section 1**). If the district voted for the Republican candidate for president the target value is equal to 1.

In **Section 2** I perform my Exploratory Data Analysis (EDA). I first run a correlation matrix of the 46 features and target variable. In the upper-right hand corner of the correlation matrix below (from the notebook with previous electoral history) is a histogram of the target variable.

![Correlation Matrix Of Features And Target Histogram](/Images/correlation_matrix_and_histogram.png)

According to the correlation matrix, there are few features with a high positive correlation and voting for the Republican Party (the scale maxed out at 0.25), however there are several features with a correlation of -0.6. (*Please note that because previous electoral history is included below the scale reaches negative one*). Among the strongest positively correlated features with districts and voting for the Republican Party are: total veterans, total married households, total white citizens, total native born residents, total workers in the agriculture field, and total workers in the manufacturing industry. Among the strongest negatively correlated features with districts and not voting for the Republican Party are: total non-citizens, total foreign-born residents and median gross rent.

I then ran a box and whiskers plot of each continuous feature to view how far spread out each one was from its median. 

![Box And Whiskers Plot Of Continuous Features](/Images/features_boxplot.png)

Various demographic features are far more spread out than others. For instance, the inner quartile range of the feature total white residents is much more spread out than others. This is mostly due to a few extreme outliers.

I then proceed to feature engineering in **section 3**. I begin by normalizing my data. For instance I divide demographic data by the total population to put them in percentage format. I create two data frames: one with the standard features as is and one with my engineered features, described below. I created Boolean columns for each industry’s worker count. If the congressional districts workers are employed by more than ten percent in an industry, then the Boolean feature is equal to 1. I then drop the column that these threshold features were derived from. I run each data frame (standard features and engineered features) in each notebook through all eight models.

### Part 3: Perform categorical models on the data frame

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

Each model allocated the 860 congressional districts between training and testing data sets. The test size for each model was set to 20% with the exception of the first logistic model (25%). As expected, every model performed better on their training data than their test data set, the percent difference is highlighted in orange in the column titled ‘% Delta (Test-Train)’. The column ‘Delta Models (E-S)’ appears twice and lists the improvement in F1 and accuracy score for each model between the engineered and standard data frame. The far right column titled ‘Model Comparison’ lists the percentage improvement the inclusion of each congressional district’s previous presidential electoral results has on the models; a column is included for the data frames with standard features and engineered features. The range of improvement in the F1-scores is between 18.70% and 6.88%. I list the top performing models below:

	1. Census Data Only – Standard Features: **XGBoost (F1: 0.8380)**
	2. Census Data Only – Engineered Features: **Logistic Regression GS (F1: 0.8360)**
	3. Census and Previous Election Results Data – Standard Features: **Random Forest (F1: 0.9341)**
	4. Census and Previous Election Results Data – Engineered Features: **XGBoost (F1: 0.9348)**

The breakdown for each model is listed below:



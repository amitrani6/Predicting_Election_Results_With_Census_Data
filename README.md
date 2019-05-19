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

2. Set up the data frame

3. Perform EDA and create engineered features

4. Perform categorical models on the data frame

5. Summarize the results


### Part 1: Obtain the data

The API calls and data frame creation are contained in the file ‘Census_Data_API_Work.ipynb’. I utilized the US Census Bureau’s API to obtain my data. There are several surveys available on their API, I chose to use the annual version of the “American Community Survey” (ACS) for my analysis. The 2011 and 2015 surveys were published in October of 2012 and 2016, respectively. This is the most current and readily available data prior to Election Day in November.

National election results are reported on the precinct level (a precinct can be as small as a few city blocks or as large as an entire county). I performed my analysis on the congressional district level because the census API has demographic information readily available at this level. The county level API calls return incomplete information for less than half of US counties. The US Census API contains data from 2010 forward, which I why this project only utilizes data points from the last two elections. 

I obtained the congressional district election results from an excel file by Stephen Wolf, a reporter for the Daily Kos. The election results are stored in the file titled “election_results.csv”.  The file “congressional_data_and_targets.csv” contains the merged 2016 and 2012 election results and census data.

I then created a unique identifier for each congressional district by election year for differentiation purposes. Lastly I removed the 14 districts that returned ‘NaN’ values in the election results columns. These districts became obsolete between the 2012 and 2016 elections and therefore had no values in the target columns.

### Part 2: Set up the data frame

The two notebooks “Classification_Models_Previous_Results.ipynb” and “Classification_Models _Census_Data_Only.ipynb” contain the data frame setup, EDA and modeling process for my analysis both with and without the congressional district’s previous election cycle results. Each file follows the exact same format. The sole difference is that the data frames within Classification_Models_Previous_Results.ipynb” contain two columns representing the percentage of votes the district casted for the Democratic and Republican presidential candidates in the previous election cycle.

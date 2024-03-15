Author: Suhani Sharma
## Project Overview
This is a data science project that investigates the features that can help us predict if a power outage will be severe based on several factors from previous power outage data. This project is for the UCSD course DSC 80: The Practice and Application of Data Science.

## Introduction
The dataset I have chosen covers major power outage data in the continental United States starting from January 2000 up to July 2016.
The main questions that I want to focus on in this project is: **When and where do major power outages tend to occur? And what variables and characteristics are most likely to lead to a higher severity outage?**
There is a greater significance to understanding this dataset. By having more in-depth knowledge on these outages there is the possibility of letting people know when outages, especially severe ones, are likely to occur so that they can prepare accordingly.
This data set contains 1534 rows where each row represents a different instance of a power outage.

Since my main objective is to understand when and where outages happen, timestamps, locations, and durations are going to be some of the most important ones to consider. The relevant columns for this information are: 

| Column Name             | Description                                                                                                                     |
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| `OBS`                     | Observation ID                                                                                                                  |
| `YEAR`                    | Indicates the year when the outage event occurred                                                                                |
| `U.S._STATE`              | Represents all the states in the continental U.S.                                                                               |
| `CLIMATE.REGION`          | U.S. Climate regions as specified by National Centers for Environmental Information (nine climatically consistent regions in continental U.S.A.) |
|`ANOMALY.LEVEL`           | Oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season                                          |
| `CLIMATE.CATEGORY`        | Climate episodes corresponding to the years; "Warm", "Cold", or "Normal" episodes of the climate                                |
| `CAUSE.CATEGORY`          | Categories of all the events causing the major power outages                                                                    |
| `CAUSE.CATEGORY.DETAIL`  | Detailed description of the event categories causing the major power outages                                                    |
| `OUTAGE.DURATION`         | Duration of outage events (in minutes)                                                                                          |
| `DEMAND.LOSS.MW`          | Amount of peak demand lost during an outage event (in Megawatt) [but in many cases, total demand is reported]                   |
| `CUSTOMERS.AFFECTED`      | Number of customers affected by the power outage event                                                                          |
| `RES.PRICE`               | Monthly electricity price in the residential sector (cents/kilowatt-hour)                                                       |
| `COM.PRICE`               | Monthly electricity price in the commercial sector (cents/kilowatt-hour)                                                        |
| `IND.PRICE`               | Monthly electricity price in the industrial sector                                                                               |
| `TOTAL.PRICE`             | Average monthly electricity price in the U.S. state (cents/kilowatt-hour)                                                       |
| `RES.SALES`               | Electricity consumption in the residential sector (megawatt-hour)                                                                |
| `COM.SALES`               | Electricity consumption in the commercial sector (megawatt-hour)                                                                 |
| `IND.SALES`               | Electricity consumption in the industrial sector (megawatt-hour)                                                                 |
| `TOTAL.SALES`             | Total electricity consumption in the U.S. state (megawatt-hour)                                                                 |
| `RES.PERCEN`              | Percentage of residential electricity consumption compared to the total electricity consumption in the state (in %)              |
| `COM.PERCEN`              | Percentage of commercial electricity consumption compared to the total electricity consumption in the state (in %)               |
| `IND.PERCEN`              | Percentage of industrial electricity consumption compared to the total electricity consumption in the state (in %)               |
| `PC.REALGSP.USA`          | Per capita real GSP in the U.S. (measured in 2009 chained U.S. dollars)                                                         |
| `PC.REALGSP.REL`          | Relative per capita real GSP as compared to the total per capita real GDP of the U.S. (expressed as fraction of per capita State real GDP & per capita US real GDP) |

Some prediction factors that we might want to consider would involve the surrounding circumstances of the outage. Beyond what has already been discussed, this section could include:
`CLIMATE.REGION`, `U.S._STATE`, `CLIMATE.CATEGORY`, `CUSTOMERS.AFFECTED`, `RES.PRICE`, `COM.PRICE`, and `IND.PRICE` just to name a few. However as we continue with the data cleaning and exploratory analysis and discover trends or interesting features in our data, this is subject to change. 
With that being said let us begin cleaning the data set and do some exploration. 


## Data Cleaning and Exploratory Data Analysis

**Dropping irrelevant columns**

I only want to use the most relevant columns. Some of the main columns that I will be dropping regard population and land vs water percentage information that I do not believe will be very useful in the context of this problem. 
Other columns that I will remove involve the start date and time and restoration date and time columns. It is redundant to have this information given that we already have a column of the duration of every outage in minutes which can essentially be derived from those four other columns. 
Some other information that I will drop is columns that are derived from others such as postal codes or month which serve more as identification of the row rather than providing any information that we can use to predict anything.

**Adjusting column naming convention** 

The naming convention of all of the columns is quite inconvenient since it is in all caps when data frame columns are usually in snake case. So I am going to convert all of these column names accordingly.
Now instead of having columns named `OBS` and `CLIMATE.REGION` they are not called `obs` and `climate_region`. 

**Creating outage duration bins**

Later in this project I will be classifying the predicted durations of a power outage. In order to understand that I need to figure out various groups or bins to classify the most common groups of how long a power outage lasts. To do this I am going to follow the steps listed below:
- Create a box plot to see where most of the data lies
- Remove outlier data points 
- Determine the most appropriate bins 
- Create a `duration_bins` columns to reflect results 

<iframe
  src="assets_plots/boxplot.html"
  width="800"
  height="500"
  frameborder="0"
></iframe> 

We notice from the above boxplot that our maximum value of a power outage is significantly higher than all other values which means it is justifialbe to drop values that are greater than our 75th percentile.

Before I determine the most appropriate bins I will transform the outage_duration column into outage_duration_hours so that the information is a little easier to understand.

<iframe
  src="assets_plots/histogram.html"
  width="800"
  height="500"
  frameborder="0"
></iframe> 


From the histogram above it is clear that the most relevant bins are: 0-10 hours, 11-20 hours, 21-30 hours, 31-40 hours, and 41-50 hours. So now I am able to create an additional column that stores the power outage durations in their bin format. 

**NaN values in the `climate_region` column** 
I noticed is that states outside of the continental United States did not have values for their `climate_region` column. While the number of rows may be insignificant compared to the entire data frame at large, I am going to impute the value 'Non Continental' into this column since I intend to use `climate_region` for my prediction later. 

**Analyzing the `cause_category` and `cause_category_detail`columns**
Similar to the `climate_region` column, I plan on using the `cause_category` column as a prediction feature. This column is also associated with the `cause_category_detail` column so I analyzed if there was any sort of relationship. In my investigation I wanted to see if perhaps certain values of `cause_category` were often associated with certain `cause_category_detail` values. My assumption was that if there was some sort of association then that meant the `cause_category_detail` included potentially useful information. 
However what I found was that there did not seem there was any such correlation so my conclusion was to drop the `cause_category_detail` column altogether.

**Understanding the relationship between `res_sales`, `com_sales`, `ind_sales`, and `total_sales`**

I notice that there are various columns which are broken up into sub-categories. For example the data frame includes the res_sales, com_sales, and ind_sales columns which respectively represent the electricity consumption in megawatts per hour of residential, commerical, and industrial sectors. The data frame also had a `total_sales` column which I validated was simply the sum of the earlier three columns. Since the `total_sales` column does not provide any additional information I decided to drop it and any other column that is simply the total of its associated `res`, `com`, and `ind` columns. 

With that we have the final cleaned data frame which looks like the following: 
### INSERT THE DATA FRAME HERE
print(counts[['Quarter', 'Count']].head().to_markdown(index=False))

### Univariate Analysis 

**Average Outage Durations by State**

The first trend I wanted to explore was if different states have varying power outage durations. 

<iframe
  src="assets_plots/bar_chart.html"
  width="800"
  height="500"
  frameborder="0"
></iframe> 

Based on the bar chart above we can see that Michigan is the US state with the longest power outages on average at about 22 hours which is almost an entire day. The lack of a uniform distribution potentially indicates that different states have different likelihoods of having longer outages than others. 

**Outages over Time**

The data set that I am using covers about 16 years of data, from the year 2000 to the year 2016. While this might not be the most helpful prediction factor it will still be important to understand how often power outages occur over the years. 

<iframe
  src="assets_plots/line.html"
  width="800"
  height="600"
  frameborder="0"
></iframe> 

From the above graph we can see that 2011 was the year with the most number of outages. There appears to have been a positive trend in the number of outages but the numbers seem to decrease after the peak in 2011. 


### Bivariate Analysis 
**Residential Prices of Electricity in California and Demand Loss**

Another thing to explore is the more economical side of the data set which details information such as the price of electricity, the amount of electric consumption, and percent of customers served in a region.
There is also additional economic information about the users themselves such as their per capita real gross state product (GSP). 

The first thing I am curious about is if there is any correlation between the residential prices of electricity in an area and the demand loss they experience in an outage. But something I noticed that these variables might be based on each state which means that for every row there are a lot of repeating values which means the data could become unintentionally misleading. So first let us just explore the values for the state of California.

<iframe
  src="assets_plots/scatter.html"
  width="800"
  height="600"
  frameborder="0"
></iframe> 

Aside from a few outlier points it seems that regardless of the residential price of electricity of a reagion they have rougly the same demand loss. 

**Demand Loss by Climate Region**

To get a better idea of the density let's try a heatmap instead where we use groups of the various climate regions to understand how the average demand loss is affected. 

<iframe
  src="assets_plots/heat_map.html"
  width="800"
  height="600"
  frameborder="0"
></iframe> 

### Interesting Aggregates
Let's continue this exploration by doing aggregate analysis of the data on a larger scale. We can look at the averages of various variables and group them based on their respective states. 

### INSERT PIVOT TABLE HERE 
The full pivot table is not shown but what we can find is that the state with the most number of customers affected by an outage on average and that has the most megawatts of demand los on average is South Carolina. 

The fact that the state with the most customers affected on average is the same as the one with the most demand loss on average indicated that perhaps there is a correlation between these two variables that will be worth exploring later.



## Assessment of Missingness
Let us explore the variables that are Not Missing At Random (NMAR). <br>
The definition of NMAR is the chance that a value is missing because of what that value actually could have been. This also means that the value that is missing is independent of other columns. <br>
One of the columns that I believe could be interpreted as NMAR is `com_percen` which represents the commercial consumption of electircity compared to the total in a particular state. 

The data frame from earlier showed that there is nothing to indicate that any of the other columns could explain why the commercial percentage of a region is missing. And since commerical percentages is information that would be relevant for any power outage there is also no reason to assume it is missing by design. Therefore one could reason that this column has information that is NMAR as perhaps the value itself was the reason it was marked as NaN or was not recorded.

Now we can look into values that might be Missing at Random (MAR) which also means the reason they are missing is dependent on the other columns in the data frame. A non-trivial column with missing values that I want to look into is the `res_price` column. One of the columns that I believe res_price might be dependent upon is the `climate_category` as different climate areas may have different trends in how much they charge for electricity. Without this information it might be hard to determine the price of electricity in that area.

I am going to create a new column of boolean values for if the `total_price` is missing in that row. 



Present and interpret the results of your missingness permutation tests with respect to your data and question. Embed a plotly plot related to your missingness exploration; ideas include:
• The distribution of column Y when column X is missing and the distribution of column Y when column X is not missing, as was done in Lecture 8.
• The empirical distribution of the test statistic used in one of your permutation tests, along with the observed statistic.



## Hypothesis Testing
Now we move on to the hypothesis testing portion of this project. I want to better understand how the duration of a power outage may change depending on the state that we're in. I am particularly interested in information regarding California. Given that this state is particularly dense I would assume that the average power outage duration in California perhaps takes longer that the average power outage in just any state. Therefore I have the following null and alternate hypotheses:

Null hypothesis: The average duration of a power outage anywhere in the United States **is equal to** the average duration of a power outage in California. 
Alternate hypothesis: The average duaration of a power outage in the United States **is less than** the average duration of a power outage in California. 
 
The significance level I am going to use is 0.05 and my test statistic is going to be the average outage duration in hours for a California power outage. The main reason for this test statistic is to see if the mean deviate a large amount from our observed statistic. 
The first thing I need to do is determine the observed average outage duration in the entire data frame. 
The number of samples I will take from the dataframe will be 20% of the number of rows that are for California. 

The resulting p-value was 0.057. Since the p-value is greater than our significance level, we **fail to reject** the null hypothesis which means that we do not have enough evidence to suggest that the average power outage duration in California is significantly greater than the ovearll average power outage duration.  


## Framing a Prediction Problem
The type of prediction model I am using is classification, specifically my classifier will be binary classification of the `duration_bin` column. In other words, `duration_bin` is our response variable. 

Currently that column has multiple classes but I will turn it into a binary column of 0's and 1's where 0 represents a non-severe power outage as they are those that 0-10 hours and a severe power outage is one that lasts 10 or more hours.  

The metric by which we will evaluate this model is R squared which represents how well our data fits to our model. 

Almost all of the information that will be used in the model is demographic information that we would have access to before the outage occurs in order to make our prediction. One could argue that `demand_loss_mw` is not something we would have knowledge of before an outage occurs, however if we look at the source for which this data comes from we have the following information:
 > "Amount of peak demand lost during an outage event (in Megawatt) [but in many cases, total demand is reported]"

As such it is acceptable to use that column for out prediction. 

## Baseline Model
The information that I will be using in my model is the following list of columns: 
> Nominal

`climate_region`

> Quantitative

- `customers_affected`
- `res_price`
- `com_price`
- `ind_price`
- `res_sales`
- `com_sales`
- `ind_sales`
- `pc_realgsp_rel`
- `demand_loss_mw`

My initial model will be a Decision Tree Classifier however it will require that I encode the nominal column of `climate_region` which will be done using One-Hot Encoding. 
The hyper-parameter of the will include:
- maximum depth of 5
- criterion of entropy
- minimum of 100 samples per leaf node 

The resulting values that I used to measure accuracy were R squared which is a metric that ranges from 0 to 1. We can think of these as almost like a percentage of accuracy. From the variables above we can see that the model has an accuracy of about 75% on the training data sets and of about 69% on the testing set. One thing to note is that this level of accuracy is not very much better than if we were to guess 0-10 about 65% of the time and hope we are correct since 65% of the values are that value. 
While this can be considered a decent starting point there are certainly improvements to be made. s


## Final Model
## Fairness Analysis
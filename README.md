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

**1. Dropping irrelevant columns**
I only want to use the most relevant columns. Some of the main columns that I will be dropping regard population and land vs water percentage information that I do not believe will be very useful in the context of this problem. 
Other columns that I will remove involve the start date and time and restoration date and time columns. It is redundant to have this information given that we already have a column of the duration of every outage in minutes which can essentially be derived from those four other columns. 
Some other information that I will drop is columns that are derived from others such as postal codes or month which serve more as identification of the row rather than providing any information that we can use to predict anything. 
---
**2. Adjusting column naming convention** 
The naming convention of all of the columns is quite inconvenient since it is in all caps when data frame columns are usually in snake case. So I am going to convert all of these column names accordingly.
Now instead of having columns named `OBS` and `CLIMATE.REGION` they are not called `obs` and `climate_region`. 
---
**3. Creating outage duration bins**
Later in this project I will be classifying the predicted durations of a power outage. In order to understand that I need to figure out various groups or bins to classify the most common groups of how long a power outage lasts. To do this I am going to follow the steps listed below:
- Create a box plot to see where most of the data lies
- Remove outlier data points 
- Determine the most appropriate bins 
- Create a `duration_bins` columns to reflect results 

<iframe
  src="assets_plots/boxplot.html"
  width="800"
  height="400"
  frameborder="0"
></iframe> 

We notice from the above boxplot that our maximum value of a power outage is significantly higher than all other values which means it is justifialbe to drop values that are greater than our 75th percentile.

Before I determine the most appropriate bins I will transform the outage_duration column into outage_duration_hours so that the information is a little easier to understand.

<iframe
  src="assets_plots/histogram.html"
  width="800"
  height="600"
  frameborder="0"
></iframe> 

From the histogram above it is clear that the most relevant bins are: 0-10 hours, 11-20 hours, 21-30 hours, 31-40 hours, and 41-50 hours. So now I am able to create an additional column that stores the power outage durations in their bin format. 
---
**4. NaN values in the `climate_region` column** 
I noticed is that states outside of the continental United States did not have values for their `climate_region` column. While the number of rows may be insignificant compared to the entire data frame at large, I am going to impute the value 'Non Continental' into this column since I intend to use `climate_region` for my prediction later. 
---
**5. Analyzing the `cause_category` and `cause_category_detail`columns**
Similar to the `climate_region` column, I plan on using the `cause_category` column as a prediction feature. This column is also associated with the `cause_category_detail` column so I analyzed if there was any sort of relationship. In my investigation I wanted to see if perhaps certain values of `cause_category` were often associated with certain `cause_category_detail` values. My assumption was that if there was some sort of association then that meant the `cause_category_detail` included potentially useful information. 
However what I found was that there did not seem there was any such correlation so my conclusion was to drop the `cause_category_detail` column altogether.
---
**6. Understanding the relationship between `res_sales`, `com_sales`, `ind_sales`, and `total_sales`**
I notice that there are various columns which are broken up into sub-categories. For example the data frame includes the res_sales, com_sales, and ind_sales columns which respectively represent the electricity consumption in megawatts per hour of residential, commerical, and industrial sectors. The data frame also had a `total_sales` column which I validated was simply the sum of the earlier three columns. Since the `total_sales` column does not provide any additional information I decided to drop it and any other column that is simply the total of its associated `res`, `com`, and `ind` columns. 

With that we have the final cleaned data frame which looks like the following: 
### INSERT THE DATA FRAME HERE
print(counts[['Quarter', 'Count']].head().to_markdown(index=False))

### Univariate Analysis 


### Bivariate Analysis 

### Interesting Aggregates


## Assessment of Missingness
## Hypothesis Testing
## Framing a Prediction Problem
The type of prediction model I am using is classification, specifically my classifier will be binary classification of the `duration_bin` column. In other words, `duration_bin` is our response variable. 

Currently that column has multiple classes but I will turn it into a binary column of $0$'s and $1$'s where $0$ represents a non-severe power outage as they are those that $0-10$ hours and a severe power outage is one that lasts $10$ or more hours.  

The metric by which we will evaluate this model is $R^2$ which represents how well our data fits to our model. 

The information that I will be using to predict is the following list of columns: `climate_region`, `customers_affected`, `res_price`, `com_price`, `ind_price`, `res_sales`, `com_sales`, `ind_sales`, `pc_realgsp_rel`, and `demand_loss_mw`. Almost all of which is demographic information that we would have access to before the outage occurs in order to make our prediction. One could argue that `demand_loss_mw` is not something we would have knowledge of before an outage occurs, however if we look at the source for which this data comes from we have the following information:
 > "Amount of peak demand lost during an outage event (in Megawatt) [but in many cases, total demand is reported]"

As such it is acceptable to use that column for out prediction. 

## Baseline Model
## Final Model
## Fairness Analysis
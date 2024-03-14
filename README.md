Author: Suhani Sharma
## Project Overview
This is a data science project that investigates the features that can help us predict if a power outage will be severe based on several factors from previous power outage data. This project is for the UCSD course DSC 80: The Practice and Application of Data Science.

## Introduction
The dataset I have chosen covers major power outage data in the continental United States starting from January 2000 up to July 2016.
The main questions that I want to focus on in this project is: **When and where do major power outages tend to occur? And what variables and characteristics are most likely to lead to a higher severity outage? **
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
> Dropping irrelevant columns 
I will begin with the process of data cleaning. I am only going to take the most relevant columns from the data. Some of the main columns that I will be dropping regard population and land vs water percentage information that I do not believe will be very useful in the context of this problem. Some other columns that I am also going to remove involve the start date and time and restoration date and time columns. It is redundant to have this information given that we already have a column of the duration of every outage which can essentially be derived from those four other columns. Some other information that I will drop is columns that are derived from others such as postal codes or month which serve more as identification of the row rather than providing any information that we can use to predict anything. 

Author: Suhani Sharma
## Project Overview
This is a data science project that investigates the features that can help us predict if a power outage will be severe based on several factors from previous power outage data. This project is for the UCSD course DSC 80: The Practice and Application of Data Science.

## Introduction
The dataset I have chosen covers major power outage data in the continental United States starting from January 2000 up to July 2016.
The main questions that I want to focus on in this project is: **When and where do major power outages tend to occur? And what variables and characteristics are most likely to lead to a higher severity outage? **
There is a greater significance to understanding this dataset. By having more in-depth knowledge on these outages there is the possibility of letting people know when outages, especially severe ones, are likely to occur so that they can prepare accordingly.
This data set contains 1534 rows where each row represents a different instance of a power outage.

Since my main objective is to understand when and where outages happen, timestamps, locations, and durations are going to be some of the most important ones to consider. The relevant columns for this information are: 

| Column Name                   | Description                                                                                                                                                     |
|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `U.S._STATE`                    | Represents all the states in the continental U.S.                                                                                                              |
| `NERC.REGION`                   | The North American Electric Reliability Corporation (NERC) regions involved in the outage event                                                               |
| `CLIMATE.REGION`                | U.S. Climate regions as specified by National Centers for Environmental Information                                                                           |
| `ANOMALY.LEVEL`                 | Oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season                                                                         |
| `CLIMATE.CATEGORY`              | Climate episodes corresponding to the years; "Warm", "Cold", or "Normal"                                                                                      |
| `OUTAGE.START.DATE`           | Day of the year when the outage event started                                                                                                                  |
| `OUTAGE.START.TIME`            | Time of the day when the outage event started                                                                                                                  |
| `OUTAGE.RESTORATION.DATE`      | Day of the year when power was restored to all the customers                                                                                                   |
| `OUTAGE.RESTORATION.TIME`      | Time of the day when power was restored to all the customers                                                                                                   |
| `CAUSE.CATEGORY`                | Categories of all the events causing the major power outages                                                                                                   |
| `CAUSE.CATEGORY.DETAIL`        | Detailed description of the event categories causing the major power outages                                                                                   |
| `HURRICANE.NAMES`              | Name of the hurricane if the outage is due to a hurricane                                                                                                      |
| `OUTAGE.DURATION`              | Duration of outage events (in minutes)                                                                                                                         |
| `DEMAND.LOSS.MW`               | Amount of peak demand lost during an outage event (in Megawatt)                                                                                                |
| `CUSTOMERS.AFFECTED`           | Number of customers affected by the power outage event                                                                                                         |
| `RES.PRICE`                    | Monthly electricity price in the residential sector (cents/kilowatt-hour)                                                                                     |
| `COM.PRICE`                    | Monthly electricity price in the commercial sector (cents/kilowatt-hour)                                                                                      |
| `IND.PRICE`                   | Monthly electricity price in the industrial sector (cents/kilowatt-hour)                                                                                      |
| `TOTAL.PRICE`                  | Average monthly electricity price in the U.S. state (cents/kilowatt-hour)                                                                                     |
| `RES.SALES`                    | Electricity consumption in the residential sector (megawatt-hour)                                                                                              |
| `COM.SALES`                    | Electricity consumption in the commercial sector (megawatt-hour)                                                                                               |
| `IND.SALES`                    | Electricity consumption in the industrial sector (megawatt-hour)                                                                                               |
| `TOTAL.SALES`                  | Total electricity consumption in the U.S. state (megawatt-hour)                                                                                                |
| `RES.PERCEN`                   | Percentage of residential electricity consumption compared to the total electricity consumption in the state (in %)                                              |
| `COM.PERCEN`                   | Percentage of commercial electricity consumption compared to the total electricity consumption in the state (in %)                                               |
| `IND.PERCEN`                   | Percentage of industrial electricity consumption compared to the total electricity consumption in the state (in %)                                               |
| `RES.CUSTOMERS`                | Annual number of customers served in the residential electricity sector of the U.S. state                                                                     |
| `COM.CUSTOMERS`                | Annual number of customers served in the commercial electricity sector of the U.S. state                                                                      |
| `IND.CUSTOMERS`                | Annual number of customers served in the industrial electricity sector of the U.S. state                                                                      |
| `TOTAL.CUSTOMERS`              | Annual number of total customers served in the U.S. state                                                                                                       |
| `RES.CUST.PCT`                 | Percent of residential customers served in the U.S. state (in %)                                                                                              |
| `COM.CUST.PCT`                 | Percent of commercial customers served in the U.S. state (in %)                                                                                               |
| `IND.CUST.PCT`                 | Percent of industrial customers served in the U.S. state (in %)                                                                                               |
| `PC.REALGSP.STATE`            | Per capita real gross state product (GSP) in the U.S. state (measured in 2009 chained U.S. dollars)                                                           |
| `PC.REALGSP.USA`              | Per capita real GSP in the U.S. (measured in 2009 chained U.S. dollars)                                                                                       |
| `PC.REALGSP.REL`              | Relative per capita real GSP as compared to the total per capita real GDP of the U.S. (expressed as fraction of per capita State real GDP & per capita US real GDP) |
| `PC.REALGSP.CHANGE`           | Percentage change of per capita real GSP from the previous year (in %)                                                                                          |
| `UTIL.REALGSP`                | Real GSP contributed by Utility industry (measured in 2009 chained U.S. dollars)                                                                               |
| `TOTAL.REALGSP`               | Real GSP contributed by all industries (total) (measured in 2009 chained U.S. dollars)                                                                         |
| `UTIL.CONTRI`                 | Utility industry׳s contribution to the total GSP in the State (expressed as percent of the total real GDP that is contributed by the Utility industry) (in %)   |
| `POPPCT_UC`                   | Percentage of the total population of the U.S. state represented by the population of the urban clusters (in %)                                               |
| `POPDEN_URBAN`                | Population density of the urban areas (persons per square mile)                                                                                               |
| `POPDEN_UC`                   | Population density of the urban clusters (persons per square mile)                                                                                            |
| `POPDEN_RURAL`                | Population density of the rural areas (persons per square mile)                                                                                               |

Some prediction factors that we might want to consider would involve the surrounding circumstances of the outage. Beyond what has already been discussed, this section could include:
`CLIMATE.REGION` - the specified climate region of the particular outage as detailed by the National Centers for Environmental Information
`CLIMATE.CATEGORY` - the climate episode of the outage during that particular year, can be 'Warm', 'Cold', or 'Normal'
`CAUSE.CATEGORY` - the category of all events that caused this power outage
`CAUSE.CATEGORY.DETAIL` - the detailed description of the power outage cause

## Data Cleaning and Exploratory Data Analysis
I will begin with the process of data cleaning. I am only going to take the most relevant columns from the data. For example I am going to be dropping many of the columns regarding population and water information because these columns are more repetetive than insightful. The population columns that I do keep will all be the ones that are specific to the particular state the outage takes place in rather than general US population information.
Some other information that I will drop is columns that are derived from others such as postal codes or month. 

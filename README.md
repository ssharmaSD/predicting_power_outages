Author: Suhani Sharma
## Project Overview
This is a data science project that investigates the features that can help us predict if a power outage will be severe based on several factors from previous power outage data. This project is for the UCSD course DSC 80: The Practice and Application of Data Science.

## Introduction
The dataset I have chosen covers major power outage data in the continental United States starting from January 2000 up to July 2016.
The main questions that I want to focus on in this project is: **When and where do major power outages tend to occur? And what variables and characteristics are most likely to lead to a higher severity outage? **
There is a greater significance to understanding this dataset. By having more in-depth knowledge on these outages there is the possibility of letting people know when outages, especially severe ones, are likely to occur so that they can prepare accordingly.
This data set contains 1534 rows where each row represents a different instance of a power outage.

Since my main objective is to understand when and where outages happen, timestamps, locations, and durations are going to be some of the most important ones to consider. The relevant columns for this information are: 
| Column | Description |
| ----------- | ----------- |
| `OUTAGE.START.DATE` | The timestamp information for the date at which the outage began.  |
| Paragraph | Text |
| Paragraph | Text |
| Paragraph | Text |
| Paragraph | Text |
| Paragraph | Text |

'OUTAGE.START.TIME' - the timestamp information for the time at which the outage began
'OUTAGE.DURATION' - how long the outage lasted in minutes
To understand how severe an outage was it will be important to consider the number of people that were affected and for how long before they had access to power again. The columns most relevant to this part of the project will be:
'CUSTOMERS.AFFECTED' - the number of customers that were affected by the outage NOTE: this column has several similar ones that contains subsets of information stored in this column.
'OUTAGE.RESTORATION.DATE' - the timestamp information for the date at which the outage ended
'OUTAGE.RESTORATION.TIME' - the timestamp information for the time at which the outage ended
'DEMAND.LOSS.MW' - the amount of peak demand lost during the outage in megawatts 

Some prediction factors that we might want to consider would involve the surrounding circumstances of the outage. Beyond what has already been discussed, this section could include:
'CLIMATE.REGION' - the specified climate region of the particular outage as detailed by the National Centers for Environmental Information
'CLIMATE.CATEGORY' - the climate episode of the outage during that particular year, can be 'Warm', 'Cold', or 'Normal'
'CAUSE.CATEGORY' - the category of all events that caused this power outage
'CAUSE.CATEGORY.DETAIL' - the detailed description of the power outage cause

## Data Cleaning and Exploratory Data Analysis
I will begin with the process of data cleaning. I am only going to take the most relevant columns from the data. For example I am going to be dropping many of the columns regarding population and water information because these columns are more repetetive than insightful. The population columns that I do keep will all be the ones that are specific to the particular state the outage takes place in rather than general US population information.
Some other information that I will drop is columns that are derived from others such as postal codes or month. 

Author: Suhani Sharma
## Project Overview
This is a data science project that investigates the features that can help us predict if a power outage will be severe based on several factors from previous power outage data. This project is for the UCSD course DSC 80: The Practice and Application of Data Science.

## Introduction
The dataset I have chosen covers major power outage data in the United States starting from January 2000 up to July 2016.
The main questions that I want to focus on in this project are: **When and where do major power outages tend to occur? And what variables and characteristics are most likely to lead to a higher severity outage?**
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
Now instead of having columns named `OBS` and `CLIMATE.REGION` they are now called `obs` and `climate_region`. 

**Creating outage duration bins**

Later in this project I will be classifying the predicted durations of a power outage. In order to understand that I need to figure out various groups or bins to classify the most common groups of how long a power outage lasts. To do this I am going to follow the steps listed below:
- Create a box plot to see where most of the data lies
- Remove outlier data points 
- Determine the most appropriate bins 
- Create a `duration_bins` columns to reflect the results 

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
  height="450"
  frameborder="0"
></iframe> 


From the histogram above it is clear that the most relevant bins are: 0-10 hours, 11-20 hours, 21-30 hours, 31-40 hours, and 41-50 hours. So now I am able to create an additional column that stores the power outage durations in their bin format. 

**NaN values in the `climate_region` column** 
I noticed is that states outside of the continental United States did not have values for their `climate_region` column. While the number of rows may be insignificant compared to the entire data frame at large, I am going to impute the value 'Non Continental' into this column since I intend to use `climate_region` for my prediction model later. 

**Analyzing the `cause_category` and `cause_category_detail`columns**
Similar to the `climate_region` column, I plan on using the `cause_category` column as a prediction feature. This column is also associated with the `cause_category_detail` column so I analyzed if there was any sort of relationship. In my investigation I wanted to see if perhaps certain values of `cause_category` were often associated with certain `cause_category_detail` values. My assumption was that if there was some sort of association then that meant the `cause_category_detail` included potentially useful information. 
However what I found was that there did not seem there was any such correlation so my conclusion was to drop the `cause_category_detail` column altogether.

**Understanding the relationship between `res_sales`, `com_sales`, `ind_sales`, and `total_sales`**

I notice that there are various columns which are broken up into sub-categories. For example the data frame includes the res_sales, com_sales, and ind_sales columns which respectively represent the electricity consumption in megawatts per hour of residential, commerical, and industrial sectors. The data frame also had a `total_sales` column which I validated was simply the sum of the earlier three columns. Since the `total_sales` column does not provide any additional information I decided to drop it and any other column that is simply the total of its associated `res`, `com`, and `ind` columns. 

With that we have the final cleaned data frame which looks like the following: 
### see if this is finally formatted correctly
|   obs |   year | us_state   | climate_region     |   anomaly_level | climate_category   | cause_category     |
|------:|-------:|:-----------|:-------------------|----------------:|:-------------------|:-------------------|
|     2 |   2014 | Minnesota  | East North Central |            -0.1 | normal             | intentional attack |
|     4 |   2012 | Minnesota  | East North Central |            -0.1 | normal             | severe weather     |
|     5 |   2015 | Minnesota  | East North Central |             1.2 | warm               | severe weather     |
|     6 |   2010 | Minnesota  | East North Central |            -1.4 | cold               | severe weather     |
|     9 |   2015 | Minnesota  | East North Central |             0.6 | warm               | intentional attack |


|   demand_loss_mw |   customers_affected |   res_price |   com_price |   ind_price |   res_sales |   com_sales |
|-----------------:|---------------------:|------------:|------------:|------------:|------------:|------------:|
|              nan |                  nan |       12.12 |        9.71 |        6.49 | 1.58699e+06 | 1.80776e+06 |
|              nan |                68200 |       11.79 |        9.25 |        6.71 | 1.85152e+06 | 1.94117e+06 |
|              250 |               250000 |       13.07 |       10.16 |        7.74 | 2.02888e+06 | 2.16161e+06 |
|              nan |                60000 |       10.63 |        8.34 |        6.15 | 1.67635e+06 | 1.78614e+06 |
|               20 |                 5941 |       11.53 |        8.89 |        6.61 | 1.8443e+06  | 1.95687e+06 |

|   ind_sales |   res_percen |   com_percen |   ind_percen |   pc_realgsp_rel |   areapct_urban |   outage_duration_hour | duration_bin   |
|------------:|-------------:|-------------:|-------------:|-----------------:|----------------:|-----------------------:|:---------------|
| 1.88793e+06 |      30.0325 |      34.2104 |      35.7276 |          1.08979 |            2.14 |              0.0166667 | 0-10           |
| 1.99303e+06 |      31.9941 |      33.5433 |      34.4393 |          1.07148 |            2.14 |             42.5       | 41-50          |
| 1.77794e+06 |      33.9826 |      36.2059 |      29.7795 |          1.09203 |            2.14 |             29         | 21-30          |
| 1.90987e+06 |      31.1928 |      33.2358 |      35.5382 |          1.06683 |            2.14 |             31         | 31-40          |
| 1.79586e+06 |      32.9369 |      34.9472 |      32.072  |          1.09203 |            2.14 |              2.58333   | 0-10           |

### Univariate Analysis 

**Average Outage Durations by State**

The first trend I wanted to explore was if different states have varying power outage durations. 

<iframe
  src="assets_plots/bar_chart.html"
  width="800"
  height="450"
  frameborder="0"
></iframe> 

Based on the bar chart above we can see that Michigan is the US state with the longest power outages on average at about 22 hours which is almost an entire day. The lack of a uniform distribution potentially indicates that different states have different likelihoods of having longer outages than others. 

**Outages over Time**

The data set that I am using covers about 16 years of data, from the year 2000 to the year 2016. While this might not be the most helpful prediction factor it will still be important to understand how often power outages occur over the years. 

<iframe
  src="assets_plots/line.html"
  width="800"
  height="450"
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
  height="450"
  frameborder="0"
></iframe> 

Aside from a few outlier points it seems that regardless of the residential price of electricity of a reagion they have rougly the same demand loss. 

**Demand Loss by Climate Region**

To get a better idea of the density let's try a heatmap instead where we use groups of the various climate regions to understand how the average demand loss is affected. 

<iframe
  src="assets_plots/heat_map.html"
  width="800"
  height="450"
  frameborder="0"
></iframe> 

### Interesting Aggregates
Let's continue this exploration by doing aggregate analysis of the data on a larger scale. We can look at the averages of various variables and group them based on their respective states. 

| us_state             |   customers_affected |   demand_loss_mw |
|:---------------------|---------------------:|-----------------:|
| Alabama              |              83881.3 |        291.5     |
| Arizona              |              62803.4 |       1500.25    |
| Arkansas             |              45194.2 |        149.9     |
| California           |             111982   |        731.679   |
| Colorado             |              50185.2 |        189       |
| Connecticut          |              43500   |         34.1667  |
| Delaware             |                800   |          4.13043 |
| District of Columbia |              97946.4 |        420       |
| Florida              |             255756   |        775.4     |
| Georgia              |             103803   |        444.667   |
| Hawaii               |             147237   |        536       |
| Idaho                |               7000   |        133.143   |
| Illinois             |             192780   |        203.5     |
| Indiana              |              47304.6 |        117.25    |
| Iowa                 |             118333   |        383.333   |
| Kansas               |              23333.3 |          0       |
| Kentucky             |             142162   |        172       |
| Louisiana            |             185535   |        147.6     |
| Maine                |              42331.1 |         38.125   |
| Maryland             |              52640.6 |        120.048   |
| Massachusetts        |              89145.8 |       2657.89    |
| Michigan             |             101451   |       1058.82    |
| Minnesota            |              62870.3 |         67.5     |
| Mississippi          |               5000   |         15       |
| Missouri             |              59000   |        162.2     |
| Nebraska             |             123106   |        381       |
| Nevada               |              22220   |         14       |
| New Hampshire        |              13869.8 |          0       |
| New Jersey           |              48301.8 |        106.455   |
| New Mexico           |             166667   |        346.667   |
| New York             |             226893   |        933.733   |
| North Carolina       |              84226.7 |        959.917   |
| North Dakota         |              34500   |        155       |
| Ohio                 |              36521.9 |        143.727   |
| Oklahoma             |              47354.4 |        125.625   |
| Oregon               |              16700.3 |         67.1111  |
| Pennsylvania         |             114924   |        177.25    |
| South Carolina       |             382795   |       3282.67    |
| South Dakota         |                0     |        228.5     |
| Tennessee            |              69491.2 |        434.625   |
| Texas                |             156638   |        447.333   |
| Utah                 |              10714.8 |        186.048   |
| Vermont              |                  0   |          0       |
| Virginia             |             129627   |        374.857   |
| Washington           |              47833   |        215.793   |
| West Virginia        |              66383   |         24       |
| Wisconsin            |              37595   |        119.857   |
| Wyoming              |              11833.3 |         26.75    |



 What we can find is that the state with the most number of customers affected by an outage on average and that has the most megawatts of demand los on average is South Carolina. 

The fact that the state with the most customers affected on average is the same as the one with the most demand loss on average indicated that perhaps there is a correlation between these two variables that will be worth exploring later.



## Assessment of Missingness
Let us explore the variables that are Not Missing At Random (NMAR). <br>
The definition of NMAR is the reason that a value is missing is because of what that value actually could have been. This also means that the missingness of that value is independent of other columns. <br>
One of the columns that I believe could be interpreted as NMAR is `com_percen` which represents the commercial consumption of electircity compared to the total in a particular state. 

The data frame from earlier showed that there is nothing to indicate that any of the other columns could explain why the commercial percentage of a region is missing. And since commerical percentages is information that would be relevant for any power outage there is also no reason to assume it is purposefully omitted missing. Therefore one could reason that this column has information that is NMAR as perhaps the value itself was the reason it was marked as NaN or was not recorded.

Now we can look into values that might be Missing at Random (MAR) which means the reason they are missing is dependent on some other column(s) in the data frame. A non-trivial column with missing values that I want to look into is the `res_price` column. One of the columns that I believe res_price might be dependent upon is the `climate_category` as different climate areas may have different trends in how much they charge for electricity.

Below is a bar plot that shows how the missingness of `res_price` differs in different climate categories.

<iframe
  src="assets_plots/climate_dist_plot.html"
  width="800"
  height="450"
  frameborder="0"
></iframe> 

From the graph we can see that only when the value of `climate_category` is equal to 'normal' do we have a missing value in the `res_price` column. 
Our permutation test will use the total variation distance (TVD) as the test statistic and have a significance level of 0.01. Below are the hypotheses.

Null hypothesis: The distribution of missingness for `res_price` is not dependent upon `climate_category`.

Alternative hypothesis: The distribution of missingness for `res_price` is dependent upon `climate_category`.

The observed test statistic is 0.51.

After our permutation testing where we used the Total Variation Distance (TVD) as the test statistic what we find is that the p-value is equal to 0.0 which is significantly lower than our significance level of 0.01 which means that we **reject** our null hypothesis meaning that we have sufficient evidence to suggest that the `res_price` missingness **is** dependent upon `climate_category`. 

Let's try this process again but with the `cause_category`.

<iframe
  src="assets_plots/cause_dist_plot.html"
  width="800"
  height="450"
  frameborder="0"
></iframe> 

For reference the observed test statistic in this test is 0.18
While the graph is not the most helpful in showing us how missingness of `res_price` is related to `cause_category` we do obtain a p-value of 0.476 which is certainly higher than our significance level meaning that we **fail to reject** the null hypothesis.


## Hypothesis Testing
Now we move on to the hypothesis testing portion of this project. I want to better understand how the duration of a power outage may change depending on the state that we're in. I am particularly interested in information regarding California. Given that this state is particularly dense I would assume that the average power outage duration in California perhaps takes longer that the average power outage in just any state. Therefore I have the following null and alternate hypotheses:

Null hypothesis: The average duration of a power outage anywhere in the United States **is equal to** the average duration of a power outage in California. 

Alternate hypothesis: The average duaration of a power outage in the United States **is less than** the average duration of a power outage in California. 
 
The significance level I am going to use is 0.05 and my test statistic is going to be the average outage duration in hours for a California power outage. The main reason for this test statistic is to see if the mean deviate a large amount from our observed statistic. 
The first thing I need to do is determine the observed average outage duration in the entire data frame. 
The number of samples I will take from the dataframe will be 20% of the number of rows that are for California. 

The resulting p-value was 0.061. Since the p-value is greater than our significance level, we **fail to reject** the null hypothesis which means that we do not have enough evidence to suggest that the average power outage duration in California is significantly greater than the ovearll average power outage duration.  


## Framing a Prediction Problem
The type of prediction model I am using is classification, specifically my classifier will be binary classification of the `duration_bin` column. In other words, `duration_bin` is our response variable. 

Currently that column has multiple classes but I will turn it into a binary column of 0's and 1's where 0 represents a non-severe power outage as they are those that 0-10 hours and a severe power outage is one that lasts more than 10 hours.  

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

My initial model will be a Decision Tree Classifier which will take in all of the features that I input and find patterns by which I can classify them into 'severe' and 'non-severe' groups. 

Note: this process will require that I encode the nominal column of `climate_region` which will be done using One-Hot Encoding for the model's pipeline.

The hyper-parameter of the Decision Tree will include:
- maximum depth of 5
- criterion of entropy
- minimum of 100 samples per leaf node 

As mentioned, the resulting values that I used to measure accuracy were R squared which is a metric that ranges from 0 to 1. We can think of these as almost like a percentage of accuracy. From the variables above we find that the model has an accuracy of about 75% on the training data sets and of about 69% on the testing set. One thing to note is that this level of accuracy is not very much better than if we were to guess 0-10 about 65% of the time and hope we are correct since 65% of the values are that value. 
While this can be considered a decent starting point there are certainly improvements to be made.

## Final Model
The new features that I added were `cause_category` and `areapct_urban`.
The reason I thought it would be beneficial to add the cause category is because for the same reason that certain areas of climate are more likely than others to experience more severe outages than others I also thought that outages caused by certain issues or failures were more likely to be harder to remedy than others. I included `areapct_urban` because I thought that if an area has a greater urban population then outages might be less likely to be severe since returning the power would be a very large priority in such urban settings. 

They were good for my prediction model because they allowed me to generalize the data a bit more and from the results they did not lead to overfitting in the model the increase in my testing and trainig accuracies was consistern for both changes.

The biggest change that I made to the Final Model compared to the Baseline was using a Random Forest rather than just a single Decision Tree. While a Decision Tree uses the parameters that it has to make the best classifications, a Random Forest will make lots of different trees and try many other methods before returning the one with the best performance out of all of them. This helps me simultaneously improve training and testing accuracy with less risk of overfitting my data. 
The modeling algorithm I chose involved encoding all the categorical variables so that the random forest could use numerical values in its classification. I also made sure to standardize the customers affected column as I did not want there to be a major skew between areas that had a lot of customers affected just because the state is one with a much larger population or square footage than another. 
The hyper-parameters I explored in Grid Search were: 
- criterion
- maximum tree depth 
- number of estimation decision trees to create 

My Grid Search returned the following as the optimal parameters:
- criterion: 'gini'
- maximum tree depth: 10
- number of estimatino decision trees: 80


## Fairness Analysis

For my fairnes analysis I wanted to explore how the model performs with climate regions that are Western (Group X) and those that are not Western (Group Y) and used the `climate_region` column to make these distinctions.
> Group X: Western 
- Southwest
- Northwest
- West North Central
- West 

> Group Y: Non-western
- East North Central
- Central
- South
- Southeast
- Northeast
- Non Continental

The following were the hypotheses that I had for this test.
Null hypothesis: The classifier model's accuracy is the same for both Western and Non-Western regions, any differences that we see are simply due to chance. 

Alternate hypothesis: The classifier model's accuracy is not the same for both groups due to reasons other than simply chance alone. 

The test statistic that I used wasthe absolute difference in accuracies for the two groups. The significance level that I used was 0.05. 

My permutation test yielded a p-value of 0.888 and because this is greater than our signifcance level it indicates that we **fail to reject** the null hypothesis. 

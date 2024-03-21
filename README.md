# power-outages
Project for Dsc 80 at UCSD

# Introduction
In this project, I examined a data set of major power outages in the U.S. from January 2000 to July 2016. These major outages are defined by the Department of Energy to have impacted at least 50,000 customers, or causing an unplanned energy demand loss of at least 300 MegaWatts. This dataset was accessed from Purdue University's Laboratory for Advancing Sustainable Critical Infrastructure, at https://engineering.purdue.edu/LASCI/research-data/outages. 

The dataset includes information about the major outages, as well as geographical, climatic, land-use characteristics, electricity consumption patterns and economic characteristics of the states affected by the outages. 

In this project, I will clean the data set and conduct exploratory data analysis in order to gain a basic understanding of the information provided. I will then analysze the missingness mechanisms and dependency of the data set. 

Finally, I will explore my research question, which is that given the outage start time, and OTHER VARIABLES, can we predict how many people the outage will affect? This is important because when more customers are affected, this in turn leads to more resources spent towards restoring proper functionality.

The original raw DataFrame contains 1534 rows, corresponding to 1534 outages, and 57 columns. However, I will only focus on a few of these columns for the sake of my analysis. 
|Column	                 |Description|
|---                     |---        |
|`'YEAR'	`            |Year an outage occurred|
|`'MONTH'`	                 |Month an outage occurred|
|`'U.S._STATE'`	         |State the outage occurred in|
|`'NERC.REGION'`	     |North American Electric Reliability Corporation (NERC) regions involved in the outage event|
|`'CLIMATE.REGION'`	            |U.S. Climate regions as specified by National Centers for Environmental Information (9 Regions)|
|`'ANOMALY.LEVEL'`	              |Oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season|
|`'OUTAGE.START.DATE'`	          |Day of the year when the outage event started|
|`'OUTAGE.START.TIME'`	          |Time of the day when the outage event started|
|`'OUTAGE.RESTORATION.DATE'`	              |Day of the year when power was restored to all the customers|
|`'OUTAGE.RESTORATION.TIME'`	     |Time of the day when power was restored to all the customers|
|`'CAUSE.CATEGORY'`             |Categories of all the events causing the major power outages|
|`'OUTAGE.DURATION'`            |Duration of outage events (in minutes)|
|`'DEMAND.LOSS.MW'`             |Amount of peak demand lost during an outage event (in Megawatt) [but in many cases, total demand is reported]|
|`'CUSTOMERS.AFFECTED'`         |Number of customers affected by the power outage event|
|`'TOTAL.PRICE'`                |Average monthly electricity price in the U.S. state (cents/kilowatt-hour)|
|`'TOTAL.SALES'`                |Total electricity consumption in the U.S. state (megawatt-hour)|
|`'TOTAL.CUSTOMERS'`            |Annual number of total customers served in the U.S. state|
|`'POPPCT_URBAN'`           |	Percentage of the total population of the U.S. state represented by the urban population (in %)|
|`'POPDEN_URBAN'`           |Population density of the urban areas (persons per square mile)|
|`'AREAPCT_URBAN'`          |Percentage of the land area of the U.S. state represented by the land area of the urban areas (in %)|

# Data Cleaning and Exploratory Data Analysis
The first step is to clean the data to make sure it is suitable for effective analysis. 
## Cleaning
1. I start by dropping irrelevant columns and only keeping the features that I am interested in for analysis. These are: 'YEAR', 'MONTH', 'U.S._STATE', 'NERC.REGION', 'CLIMATE.REGION', 'ANOMALY.LEVEL',
        'OUTAGE.START.DATE', 'OUTAGE.START.TIME', 'OUTAGE.RESTORATION.DATE', 'OUTAGE.RESTORATION.TIME',
        'CAUSE.CATEGORY', 'OUTAGE.DURATION', 'DEMAND.LOSS.MW', 'CUSTOMERS.AFFECTED', 'TOTAL.PRICE',
        'TOTAL.SALES', 'TOTAL.CUSTOMERS', 'POPPCT_URBAN', 'POPDEN_URBAN',
        'AREAPCT_URBAN'.

2. Next, I combine the OUTAGE.START.DATE and OUTAGE.START.TIME columns into one Timestamp object in an OUTAGE.START column. I do the same for OUTAGE.RESTORATION.DATE and OUTAGE.RESTORATION.TIME. I then dropped the old columns since all the relevant information is in OUTAGE.START and OUTAGE.RESTORATON.

3. Next, I check my outcomes of interest, OUTAGE.DURATION, CUSTOMERS.AFFECTED, and DEMAND.LOSS.MW for values of 0, which are likely indicative of missing values since major outages wouldn't have a duration of 0 minutes, 0 customers affected, or 0 MW of energy lost. I replace 0 values in these columns with np.nan.

4. I combine 'POPPCT_URBAN', 'POPDEN_URBAN', and 'AREAPCT_URBAN' into one column, 'URBAN,' which accounts for the percent of a state's population is urban, the density of these areas, and normalizing for differences in state land that is urban. 


## Exploratory Data Analysis
In my exploratory data analysis, I first perform univariate analysis to examine the distribution of single variables. 

First, I wanted to see how the number of outages has changed over time.
<iframe
  src="assets/outage_over_time.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I also wanted to see the distribution of major causes of power outages.
<iframe
  src="assets/major_causes.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Then, I wanted to see the distribution of the number of outages by each U.S. state. 
<iframe
  src="assets/map1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


# Assessment of Missingness

## NMAR Analysis
Several columns contain missing data in the data set, but one of these columns that is likely NMAR is 'CUSTOMERS.AFFECTED.' This is because the missingness is likely due to the data collection method, which aggregates data from a variety of sources. If certain companies did not report the number of customers that were affected, then there would be missing values. 

Additional data we could collect to determine if 'CUSTOMERS.AFFECTED' is MAR is to collect the individual reporting companies for each outage, and then conduct analysis to see whether the missingness of the customers is dependent on the company.

## Missingness Dependency
To test missingness dependency, we will focus on the distribution of 'OUTAGE.DURATION'. 

# Hypothesis Testing
**Null Hypothesis:** On average, the duration of severe weather outages is the same as the duration of intentional attack outages.

**Alternate Hypothesis:** On average, the duration of severe weather outages is greater than the duration of intentional attack outages.

**Test Statistic:** Difference in means. Specifically, mean outage duration of severe weather - mean outage duration of intentional attacks.

I performed a permutation test with 10,000 simulations in order to generate an empirical distribution of the test statisic under the null hypothesis.

# Framing a Prediction Problem
My model will try to predict the cause of a power outage. This will be a binary classification because we are only focusing on outages cause by severe weather or intentional attacks. 

The metric I am using the evaluate my model is the F1 score, because there is an imbalance within the classes so this will most effectively balance that out and incorporate both the precision and recall.

At the time of prediction, we would know the state, NERC region, climate region, anomaly level, and SOMETHING ELSE. This information will allow us to predict what the cause of a major power outage is.

# Baseline Model
My model is a binary classifier using the features U.S. State, NERC Region, Climate Category, and Anomaly Level to predict whether a major outage is caused by severe weather or an intentional attack. This information would provide companies with how to approach infrastructure problems related to energy. 

The features are:
- U.S. State, which is nominal
- NERC Region, which is nominal
- Climate Category, which is nominal
- Anomaly Level, which is quantitative

The performance of this model was not great.

# Final Model

# Fairness Analysis



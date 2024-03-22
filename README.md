# power-outages
Project for Dsc 80 at UCSD

# Introduction
In this project, I examined a data set of major power outages in the U.S. from January 2000 to July 2016. These major outages are defined by the Department of Energy to have impacted at least 50,000 customers, or causing an unplanned energy demand loss of at least 300 MegaWatts. This dataset was accessed from Purdue University's Laboratory for Advancing Sustainable Critical Infrastructure, at https://engineering.purdue.edu/LASCI/research-data/outages. 

The dataset includes information about the major outages, as well as geographical, climatic, land-use characteristics, electricity consumption patterns and economic characteristics of the states affected by the outages. 

First, I will clean the data set and conduct exploratory data analysis in order to gain a basic understanding of the information provided. I will then analyze the missingness mechanisms and dependency of the data set. 

Finally, I will explore my research question, which is what are the most important causes and characteristics of major power outages? I will try to build a model that predicts what the cause of a major power outage is in a certain place at a certain time. This is important because if energy companies can predict what will cause a power outage, they can enact preventative measures such as stronger infrastructure against weather, or better security to reduce attacks. 

The original raw DataFrame contains 1534 rows, corresponding to 1534 outages, and 57 columns. However, I will only focus on a few of these columns for the sake of my analysis. 
|Column                |Description|
|---                |---        |
|`'YEAR'`                |Year an outage occurred|
|`'MONTH'`                |Month an outage occurred|
|`'U.S._STATE'`                |State the outage occurred in|
|`'NERC.REGION'`                |North American Electric Reliability Corporation (NERC) regions involved in the outage event|
|`'CLIMATE.REGION'`                |U.S. Climate regions as specified by National Centers for Environmental Information (9 Regions)|
|`'ANOMALY.LEVEL'`                |Oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season|
|`'OUTAGE.START.DATE'`                |Day of the year when the outage event started|
|`'OUTAGE.START.TIME'`                |Time of the day when the outage event started|
|`'OUTAGE.RESTORATION.DATE'`                |Day of the year when power was restored to all the customers|
|`'OUTAGE.RESTORATION.TIME'`                |Time of the day when power was restored to all the customers|
|`'CAUSE.CATEGORY'`                |Categories of all the events causing the major power outages|
|`'OUTAGE.DURATION'`                |Duration of outage events (in minutes)|
|`'DEMAND.LOSS.MW'`                |Amount of peak demand lost during an outage event (in Megawatt) [but in many cases, total demand is reported]|
|`'CUSTOMERS.AFFECTED'`                |Number of customers affected by the power outage event|
|`'TOTAL.PRICE'`                |Average monthly electricity price in the U.S. state (cents/kilowatt-hour)|
|`'TOTAL.SALES'`                |Total electricity consumption in the U.S. state (megawatt-hour)|
|`'TOTAL.CUSTOMERS'`                |Annual number of total customers served in the U.S. state|
|`'POPPCT_URBAN'`                |Percentage of the total population of the U.S. state represented by the urban population (in %)|
|`'POPDEN_URBAN'`                |Population density of the urban areas (persons per square mile)|
|`'AREAPCT_URBAN'`                |Percentage of the land area of the U.S. state represented by the land area of the urban areas (in %)|

# Data Cleaning and Exploratory Data Analysis
The first step is to clean the data to make sure it is suitable for effective analysis. 
## Cleaning
1. I start by dropping irrelevant columns and only keeping the features that I am interested in for analysis. These are: `YEAR`, `MONTH`, `U.S._STATE`, `NERC.REGION`, `CLIMATE.REGION`, `ANOMALY.LEVEL`,
        `OUTAGE.START.DATE`, `OUTAGE.START.TIME`, `OUTAGE.RESTORATION.DATE`, `OUTAGE.RESTORATION.TIME`,
        `CAUSE.CATEGORY`, `OUTAGE.DURATION`, `DEMAND.LOSS.MW`, `CUSTOMERS.AFFECTED`, `TOTAL.PRICE`,
        `TOTAL.SALES`, `TOTAL.CUSTOMERS`, `POPPCT_URBAN`, `POPDEN_URBAN`, `AREAPCT_URBAN`.

2. Next, I combine the `OUTAGE.START.DATE` and `OUTAGE.START.TIME` columns into one Timestamp object in an `OUTAGE.START` column. I do the same for `OUTAGE.RESTORATION.DATE` and `OUTAGE.RESTORATION.TIME`. I then dropped the old columns since all the relevant information is in `OUTAGE.START` and `OUTAGE.RESTORATON`.

3. Next, I check my outcomes of interest, `OUTAGE.DURATION`, `CUSTOMERS.AFFECTED`, and `DEMAND.LOSS.MW` for values of 0, which are likely indicative of missing values since major outages wouldn't have a duration of 0 minutes, 0 customers affected, or 0 MW of energy lost. I replace 0 values in these columns with np.nan.

4. I combine `POPPCT_URBAN`, `POPDEN_URBAN`, and `AREAPCT_URBAN` into one column, `URBAN`, which accounts for the percent of a state's population is urban, the density of these areas, and normalizing for differences in state land that is urban. 

The first few rows of this cleaned DataFrame are shown below, with a portion of columns selected.

| U.S._STATE   | NERC.REGION   | CAUSE.CATEGORY     |   OUTAGE.DURATION |   CUSTOMERS.AFFECTED | OUTAGE.START        |
|:-------------|:--------------|:-------------------|------------------:|---------------------:|:--------------------|
| Minnesota    | MRO           | severe weather     |              3060 |                70000 | 2011-07-01 17:00:00 |
| Minnesota    | MRO           | intentional attack |                 1 |                  nan | 2014-05-11 18:38:00 |
| Minnesota    | MRO           | severe weather     |              3000 |                70000 | 2010-10-26 20:00:00 |
| Minnesota    | MRO           | severe weather     |              2550 |                68200 | 2012-06-19 04:30:00 |
| Minnesota    | MRO           | severe weather     |              1740 |               250000 | 2015-07-18 02:00:00 |


## Exploratory Data Analysis

### Univariate Analysis
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

### Bivariate Analysis
I conducted many bivariate analyses, and the most significant results are shown below.

I examined the relationship between Outage Duration and Customers Affected, two metrics of the severity of a power outage. I expected there to be a positive correlation, since major outages likely affect a lot of customers and have a long duration, but there was variability within this. There are many outages that affected a lot of customers but were not as long, indicating that Customers Affected might be a better metric for measuring outage severity.
<iframe
  src="assets/duration_cust.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The plot below shows the relation between outage duration and cause category. It shows that some of the outages with the longest duration were due to a fuel supply emergency.
<iframe
  src="assets/duration_cause.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Grouping and Aggregates
I grouped by NERC Region and then performed an aggregate function mean() to get the average severity metrics for each region. The severity metrics are Outage Duration, Customers Affected, and Demand Loss. The first few rows of this DataFrame are shown below: 
| NERC.REGION   |   OUTAGE.DURATION |   CUSTOMERS.AFFECTED |   DEMAND.LOSS.MW |
|:--------------|------------------:|---------------------:|-----------------:|
| ASCC          |           nan     |                14273 |           35     |
| ECAR          |          5603.31  |               256354 |         1314.48  |
| FRCC          |          4271.12  |               375007 |         1072.6   |
| FRCC, SERC    |           372     |                  nan |          nan     |
| HECO          |           895.333 |               126729 |          466.667 |

I also performed grouping with a pivot table, on Climate Region and Cause Category to see which regions experienced severe weather outages the most. The first few rows of this data frame are shown below:
| CLIMATE.REGION     |   equipment failure |   fuel supply emergency |   intentional attack |   islanding |   public appeal |   severe weather |   system operability disruption |
|:-------------------|--------------------:|------------------------:|---------------------:|------------:|----------------:|-----------------:|--------------------------------:|
| Central            |                   7 |                       4 |                   38 |           3 |               2 |              135 |                              11 |
| East North Central |                   3 |                       5 |                   20 |           1 |               2 |              104 |                               3 |
| Northeast          |                   5 |                      14 |                  135 |           1 |               4 |              176 |                              15 |
| Northwest          |                   2 |                       1 |                   89 |           5 |               2 |               29 |                               4 |
| South              |                  10 |                       7 |                   28 |           2 |              42 |              113 |                              27 |

# Assessment of Missingness

## NMAR Analysis
Several columns contain missing data in the data set, but one of these columns that is likely NMAR is `CUSTOMERS.AFFECTED`. This is because the missingness is likely due to the data collection method, which aggregates data from a variety of sources. If certain companies did not report the number of customers that were affected, then there would be missing values. 

Additional data I could collect to determine if `CUSTOMERS.AFFECTED` is MAR is to collect the individual reporting companies for each outage, and then conduct analysis to see whether the missingness of the customers is dependent on the company.

## Missingness Dependency
To test missingness dependency, I will focus on the distribution of `OUTAGE.DURATION`. I will test this against the columns `CAUSE.CATEGORY` and `MONTH`.

### Cause Category
First, I examine the distribution of Cause Category when Duration is missing vs not missing.

**Null Hypothesis:** The distribution of Cause Category is the same when Duration is missing vs not missing.

**Alternate Hypothesis:** The distribution of Cause Category is different when Duration is missing vs not missing. 

<iframe
  src="assets/cause_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

 I found an observed TVD of 0.444 which has a p value of 0.0. The empirical distribution of the TVDs is shown below. At this value, I reject the null hypothesis in favor of the alternate hypothesis, which is that the distribution of Cause Category is significantly different when Duration is missing vs not, indicating that the missingness of Duration is dependent on Cause Category.
<iframe
  src="assets/cause_tvd.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Month
Next, I examined the dependency of Duration missing on another column, `MONTH`. 
**Null Hypothesis:** The distribution of Month is the same when Duration is missing vs not missing.

**Alternate Hypothesis:** The distribution of Month is different when Duration is missing vs not missing. 

Here is the distribution of Month when Duration is missing vs not missing.
<iframe
  src="assets/month_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I found an observed TVD of 0.143. This had a p value of 0.1756. The empirical distribution of the TVDs is shown below. At this value, I fail to reject the null hypothesis in favor of the alternate hypothesis. The distribution of Month is not significantly different when Duration is missing vs not, indicating that the missingness of Duration is not dependent on Month. 
<iframe
  src="assets/month_tvd.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


# Hypothesis Testing
I will be testing whether the outage duration is greater on average for severe weather outages over intentional attack outages. The relevant columns for this test are `OUTAGE.DURATION` and `CAUSE.CATEGORY`. I will only be using the outages where `CAUSE.CATEGORY` is equal to 'severe weather' or 'intentional attack'. 

**Null Hypothesis:** On average, the duration of severe weather outages is the same as the duration of intentional attack outages.

**Alternate Hypothesis:** On average, the duration of severe weather outages is greater than the duration of intentional attack outages.

**Test Statistic:** Difference in means. Specifically, mean outage duration of severe weather - mean outage duration of intentional attacks.

I performed a permutation test with 10,000 simulations in order to generate an empirical distribution of the test statisic under the null hypothesis. 

The p-value I got was 0.0, so with a standard significance level of 0.05, we reject the null hypothesis because the results are statistically significant. We conclude that on average, the duration of severe weather outages is greater than intentional attack outages.

The plot below shows the observed difference against the empirical distribution of differences from the permutation tests.
<iframe
  src="assets/h_test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

# Framing a Prediction Problem
My model will try to predict the cause of a power outage. This will be a binary classification because we are only focusing on outages cause by severe weather or intentional attacks. 

The metric I am using the evaluate my model is the F1 score, because there is an imbalance within the classes so this will most effectively balance that out and incorporate both the precision and recall.

At the time of prediction, we would know the state, NERC region, climate region, anomaly level, year, month, total sales, total price, total customers, and the urban factor. This information will allow us to predict what the cause of a major power outage is.

# Baseline Model
My model is a binary classifier using the features NERC Region, Anomaly level, Year, and Urban factor to predict whether a major outage is caused by severe weather or an intentional attack. This information would provide companies with how to approach energy infrastructure problems and decide whether to devote resources to better security against attacks or better protection from severe weather.

The features are: `NERC.REGION`, `ANOMALY.LEVEL`, `YEAR`, and `URBAN`.
The predicted columns was converted to 1 for severe weather and 0 for intentional attack.

The performance of this model was pretty good, with an r-squared of 0.764 on the test set. 
The F1 score was 0.831.


# Final Model
My final model incorporated these features: `NERC.REGION`, `CLIMATE.REGION`, `ANOMALY.LEVEL`, `YEAR`, `MONTH`, `TOTAL.PRICE`, `TOTAL.SALES`, `TOTAL.CUSTOMERS`, `URBAN`. I used a DecisionTreeClassifier and was able to achieve an R-squared of 0.88 when testing on the test set. 

I used GridSearchCV to find the best hyperparameters for the DecisionTreeClassifier.

I used a F1 score to measure the performance of my model. I got an F1 score of 0.905, and since the F1 score increased from the baseline to the final, this indicates better performance of the final model. 

# Fairness Analysis
My groups for the fairness analysis are longer vs shorter outages. This is defined as outages that are greater than 3000 minutes, vs outages that are less than 3000 minutes. 

I decided on these groups because the cause category (which is predicted by my model) can greatly determine the outage duration. We want to make sure that the model can predict the classification well because this can inform energy companies on what to focus on to prevent longer outages. 

My evaluation metric will be F1 score since the classes (longer vs shorter) are imbalanced, and this metric accounts for that imbalance, while also incorporating the precision and recall. I will use permutation tests to calculate the F1 score for longer vs shorter outages (that are randomly shuffled) and then compare this absolute difference to my initial observed absolute difference. 

**Null Hypothesis:** The model is fair. Its F1 scores for longer and shorter outages are roughly the same, and any differences are due to random chance.

**Alternative Hypothesis:** The model is unfair. Its F1 score for longer outages is significantly different from the F1 score for shorter outages.

I performed a permutation test with 10000 trials. My significance level is the standard 0.05, and I got a p_value of 0.0 so because this is below the significance level, I reject the null hypothesis. The model is significantly different in terms of F1 score for longer vs shorter outages.

The figure below shows the distribution of the statistic. 
<iframe
  src="assets/fairness.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


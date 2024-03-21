# power-outages
Project for Dsc 80 at UCSD

# Introduction
In this project, I examined a data set of major power outages in the U.S. from January 2000 to July 2016. These major outages are defined by the Department of Energy to have impacted at least 50,000 customers, or causing an unplanned energy demand loss of at least 300 MegaWatts. This dataset was accessed from Purdue University's Laboratory for Advancing Sustainable Critical Infrastructure, at https://engineering.purdue.edu/LASCI/research-data/outages. 

The dataset includes information about the major outages, as well as geographical, climatic, land-use characteristics, electricity consumption patterns and economic characteristics of the states affected by the outages. 

In this project, I will clean the data set and conduct exploratory data analysis in order to gain a basic understanding of the information provided. I will then analysze the missingness mechanisms and dependency of the data set. 

Finally, I will explore my research question, which is that given the outage start time, and OTHER VARIABLES, can we predict how many people the outage will affect? This is important because when more customers are affected, this in turn leads to more resources spent towards restoring proper functionality. 

# Data Cleaning and Exploratory Data Analysis
The first step is to clean the data to make sure it is suitable for effective analysis. 

## Cleaning
I start by combining the OUTAGE.START.DATE and OUTAGE.START.TIME columns into one Timestamp object in an OUTAGE.START column. I do the same for OUTAGE.RESTORATION.DATE and OUTAGE.RESTORATION.TIME. 

Next, I decided to drop some columns which will be irrelevant to my analysis. I dropped the POSTAL.CODE column since these values are essentially the same as in U.S._STATE, and I chose to keep the full state names for easier readability. I also dropped the old outage start and restoration date and time columns. 

## Exploratory Data Analysis
In my exploratory data analysis, I first perform univariate analysis to examine the distribution of single variables. 

First, I wanted to see the distribution of the number of outages by each U.S. state. MAP OBJECT



# Assessment of Missingness


# Hypothesis Testing

# Framing a Prediction Problem

# Baseline Model

# Final Model

# Fairness Analysis



# Analyzing Power Outages Across the U.S States

**Name(s)**: Michael Ren

**Website Link**: https://masteryoda29.github.io/U.S-Power-Outage-Data-Visualization-and-Analysis/

## Introduction

This is a dataset containing the set of power outages collected from the years 2000 to 2016, collected statewide, and includes regions encoded under the NERC. A timeline of outage start date and times up to recovery date and time are provided. The dataset was accessed from Purdue University’s Laboratory for Advancing Sustainable Critical Infrastructure, at https://engineering.purdue.edu/LASCI/research-data/outages.

 
While many might not care about power outages on a daily basis, but given the actual inconvenience and confusion that usually occurs when outages happen, it is not just residents that suffer from outage occurences, but industries and commercial activities also become negatively impacted, and thus stopping regions and companies from generating profit, which can force companies to scale back their operations and incur significant losses in revenue.   

Prior to cleaning the dataset, the data was imported from an online repository containing an excel file, and after conversion, to 56 columns and 1540 total rows. Relevant and insightful columns of data include outage start and recovery time, etc. Using this dataset, I will perform some extraction and cleaning so as to make relationships more clear, and also so that the data can be ready for analysis. Then I will explore the data, in terms of relationships between certain features, determine missingness mechanisms and perform some hypothesis testing. 

Lastly, the pertinent question I am interested in exploring is the calculation and determination of Demand Loss based on relevant features throughout the dataset. In order to thouroughly investigate this question, I will build a regression model and engineer features as well as preprocess all necessary columns and any other necessary transformations so as to best fit the model.  

## Data Cleaning and Exploratory Data Analysis


<p style="font-size: 24px;">Data Cleaning</p>

Due to the conversion between different file types(csv to Pandas Dataframe), unnecessary details such as the purpose of the dataframe were included(No doubt important for the reader! But not for analysis purposes), and converted to additional rows with NaN values saturating the rest of the columns. In order to remedy this problem, the description was saved separately as a variable allowing for one to inspect its content aside, and the rows containing descriptive information were filtered out. 

As we further look at the dataframe and compare it to the original spreadsheet, we notice there's an important row with label 'vars' which contains the label for which the values in the columns correspond to. So we replace the current NaN placeholder column names with their values with the values in the 'vars' column, and then drop the vars column. Lastly, we readjust and reset the index, since rows have been filtered. The results of the filtering and extraction from cleaning have the results of making our processes of analysis simpler, as filtering out rows with all NaN values can contribute potential noise to our data and complicate or mislead us in our statistical analysis by adding in extra data points with no meaningful values, potentially shifting aggregate statistics.

<p style="font-size: 24px;">Exploratory Data analysis</p>

<p style="font-size: 18px;">Univariate analysis</p>

<iframe src="assets/Sales.html" width="800" height="600" frameborder="0"></iframe>
<iframe src="assets/Hist.html" width="800" height="600" frameborder="0"></iframe>

Note here for the line plot of total sales over time, that between the period of 2004-2012, roughly every 4 years, electrical utility sales reach localized peaks, and drop to their individual minima every two years inbetween.

I also plotted the bar plot of Urban population density, so as to examine the characteristics of typical urban population densities given the outage distribution. Here we can see the urban population densities can be said to be mainly centered over 2K people per sq.mile, with another major concentration inbetween 4K and 5K people per sq.mile. An outlier of around 9.8K people per sq.mile can also be observed at the tail end of the plot. 

<p style="font-size: 20px;">Bivariate analysis</p>

<iframe src="assets/Scatter.html" width="800" height="600" frameborder="0"></iframe>
<iframe src="assets/Box.html" width="800" height="600" frameborder="0"></iframe>

In addition to univariate analysis, I also conducted an analysis of pairs of columns to see which features might be related or correlated in nonrandom and meaningful ways to each other. Starting off with the first scatter plot, viewing both its regression coefficients, and looking at the plots, one can see there is quite a significant linear association between residential prices and industrial prices, with residential prices lagging behind industrial prices, which intuitively makes sense, as industrial prices might be cheaper due to government subsidies and due to owning the means of production, whereas residential prices are largely market driven. 

The second bivariate plot I have decided to include is a box plot capped at the 90th percentile to exclude outliers with extreme values which also represents a minority of the cases. In this plot one can observe that the largest categories influencing customers affected are 'severe weather', 'system operability disruption' and 'equipment failure', with severe weather having the largest spread and median(median of 100k and upper quartile of around 300K), meaning that 'severe weather' and 'system operability disruption' have the highest impact amongst major number of customers affected by grid power outages. 

## Assessment of Missingness

<p style="font-size: 20px;">Missingness prediction</p>

Before going into actual testing of missingness, just from reasoning over the quantities in the dataset, one potential feature that might fall under NMAR Missingness is Anomaly level itself. We might surmise that the anomaly levels that are missing correspond to extreme/large variations in the average temperature. From a physics standpoint, turbulent and chaotic weather can be much less predictable and harder to measure(numerically with instruments), and this is a reason why weather forecasts often sport incorrect predictions, since sensitivity to initial conditions can lead to drastically different weather outcomes. Measurements of temperature often rely on homogeneity and local temperature equilibrium for accurate predictions, so if there are any fluctuating thermal gradients or large variations in particle density, then these factors can make temperature prediction much more variable, however these are all related to the missing value itself(a.k.a the deviation from the average), so Anomaly levels are very likely NMAR.

Here for the two Missingness test I conduct, I separately test the possibility of NMAR missingness dependence of the 'DEMAND LOSS' column upon the Cause category and the Climate category. 

__Null Hypothesis__: The distribution of Cause.Category is the same when Demand Loss is missing vs not missing.

__Alternate Hypothesis__: The distribution of Cause.Category is different when Demand Loss is missing vs not missing.

<iframe src="assets/TVD.html" width="800" height="600" frameborder="0"></iframe>
<iframe src="assets/causePval.html" width="800" height="600" frameborder="0"></iframe>

By examining both the TVD plot and the p value distributions, we can see that the p value 0.052 just hinges upon the rejection significance, and from the comparison of TVD plots that "islanding", "fuel supply emergency", and "System operability disruptions" differ significantly, so in the NMAR dependence of Demand Loss upon Cause.Category we reject the null hypothesis, and believe it is highly likely missingness in Demand Loss can be explained from Cause.Category values. On the other hand my testing hypothesis for Climate.Category which largely echoes that for the Cause.Category are given below: 

__Null Hypothesis__: The distribution of Climate.Category is the same when Demand Loss is missing vs not missing.

__Alternate Hypothesis__: The distribution of Climate.Category is different when Demand Loss is missing vs not missing.

<iframe src="assets/climatePval.html" width="800" height="600" frameborder="0"></iframe>

However in this case, when we look to the TVD plot and the p value distribution, we can see there is a nonnegligible overlap, roughly around half the data is equivalent to or larger than the observed p value for the data set, so in this case we fail to reject the null hypothesis. 



## Hypothesis Testing

Pertaining to the dataset, I will test and observe whether or not there is a greater on average power outage duration corresponding to the sign of the anomaly level. 

__Null Hypothesis:__ There is no clear preference and the distribution is the same between lower than average(-) temperature anomalies, and above average(+) temperature anomalies. 

__Alternative Hypothesis:__ The distributions of power outage durations differ for lower-than-average (negative) temperature anomalies and higher-than-average (positive) temperature anomalies.

<iframe src="assets/Outage_Duration_Distribution.html" width="800" height="600" frameborder="0"></iframe>

Looking at the distribution of the two populations, and performing a K.S statistics test, we see that the p value is 0.052, just barely passing the p value test. And one might be surprised that there is a slight preference/correlation towards colder weather than hotter weather. Given that the condition required for cyclones and hurricanes to form is lower barometric pressure in local regions, combining this with noninertial forces due to Earth's rotation such as the Coriolis force, the forgoing qualities cause the large scale rotational/vortex motion which is observed in cyclones and hurricanes. This variation in temperature and pressure which is more dramatic near coastal regions, leads to higher favorability to forming extreme weather, and coastal regions, which can exchange temperature via convection and conduction via air and land, tend to be colder. 

## Framing a Prediction Problem

The model I will be creating will attempt to predict the Demand loss. This falls under the umbrella of regression, since the model will predict some exact numbers, and evaluate under chosen metrics its accuracy. 

The metric I will be using is Mean Squared Error. MSE is appropriate because it penalizes larger errors more heavily, aligning with the goal of minimizing significant prediction deviations. Its squared nature ensures sensitivity to outliers and provides a clear, interpretable measure of model performance.

## Baseline Model

## Final Model

## Fairness Analysis
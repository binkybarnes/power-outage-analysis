# Predicting Outage Duration

## Introduction

### Introduction and Question Identification

In this report, I analyze power outages that occurred in the US. This dataset contains information spanning from January 2000 to July 2016. The data was sourced from here: https://engineering.purdue.edu/LASCI/research-data/outages/outagerisks

I will be taking a look at what characteristics affect power outages with longer outage durations. There is geospatial and electrical consumer data that would be helpful in establishing a connection between these two values. With other features, we will build a model that can predict how long an outage will last when given certain information.

The dataset contains 1534 rows of outages. Below are some relevant columns.

| Column                  | Desc                                                                         |
| ----------------------- | ---------------------------------------------------------------------------- |
| `CLIMATE.CATEGORY`      | the climate episodes corresponding to the years                              |
| `CLIMATE.REGION`        | nine climatically consistent regions in continental U.S.A.                   |
| `U.S._STATE`            | all the states in the continental U.S.                                       |
| `CAUSE.CATEGORY`        | all the events causing the major power outages                               |
| `CAUSE.CATEGORY.DETAIL` | Detailed description of the event categories causing the major power outages |
| `OUTAGE.DURATION`       | Duration of outage events (in minutes)                                       |
| `CUSTOMERS.AFFECTED`    | Number of customers affected by the power outage event                       |

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

1. I skipped some rows in the excel file that would were not useful.

2. Then I combined `OUTAGE.START.DATE` and `OUTAGE.START.TIME` into `OUTAGE.START`. Same thing with `OUTAGE.RESTORATION.DATE` and `OUTAGE.RESTORATION.TIME` into `OUTAGE.RESTORATION`.

3. I discovered that `CAUSE.CATEGORY.DETAIL` had the same entries (coal) but with different whitespace, so I stripped their white space. I also made them lowercase to remove duplicates.

I have not performed imputation on missing values yet.

Here is the first few rows of the cleaned DataFrame

|     | OUTAGE.START        | OUTAGE.RESTORATION  | OBS | YEAR | MONTH | U.S.\_STATE | POSTAL.CODE | NERC.REGION | CLIMATE.REGION     | ANOMALY.LEVEL | CLIMATE.CATEGORY | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL | HURRICANE.NAMES | OUTAGE.DURATION | DEMAND.LOSS.MW | CUSTOMERS.AFFECTED | RES.PRICE | COM.PRICE | IND.PRICE | TOTAL.PRICE |   RES.SALES |   COM.SALES |   IND.SALES | TOTAL.SALES | RES.PERCEN | COM.PERCEN | IND.PERCEN | RES.CUSTOMERS | COM.CUSTOMERS | IND.CUSTOMERS | TOTAL.CUSTOMERS | RES.CUST.PCT | COM.CUST.PCT | IND.CUST.PCT | PC.REALGSP.STATE | PC.REALGSP.USA | PC.REALGSP.REL | PC.REALGSP.CHANGE | UTIL.REALGSP | TOTAL.REALGSP | UTIL.CONTRI | PI.UTIL.OFUSA | POPULATION | POPPCT_URBAN | POPPCT_UC | POPDEN_URBAN | POPDEN_UC | POPDEN_RURAL | AREAPCT_URBAN | AREAPCT_UC | PCT_LAND | PCT_WATER_TOT | PCT_WATER_INLAND |
| --: | :------------------ | :------------------ | --: | ---: | ----: | :---------- | :---------- | :---------- | :----------------- | ------------: | :--------------- | :----------------- | :-------------------- | --------------: | --------------: | -------------: | -----------------: | --------: | --------: | --------: | ----------: | ----------: | ----------: | ----------: | ----------: | ---------: | ---------: | ---------: | ------------: | ------------: | ------------: | --------------: | -----------: | -----------: | -----------: | ---------------: | -------------: | -------------: | ----------------: | -----------: | ------------: | ----------: | ------------: | ---------: | -----------: | --------: | -----------: | --------: | -----------: | ------------: | ---------: | -------: | ------------: | ---------------: |
|   0 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00 |   1 | 2011 |     7 | Minnesota   | MN          | MRO         | East North Central |          -0.3 | normal           | severe weather     | nan                   |             nan |            3060 |            nan |              70000 |      11.6 |      9.18 |      6.81 |        9.28 | 2.33292e+06 | 2.11477e+06 | 2.11329e+06 | 6.56252e+06 |    35.5491 |     32.225 |    32.2024 |       2308736 |        276286 |         10673 |         2595696 |      88.9448 |       10.644 |     0.411181 |            51268 |          47586 |        1.07738 |               1.6 |         4802 |        274182 |     1.75139 |           2.2 |    5348119 |        73.27 |     15.28 |         2279 |    1700.5 |         18.2 |          2.14 |        0.6 |  91.5927 |       8.40733 |          5.47874 |
|   1 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00 |   2 | 2014 |     5 | Minnesota   | MN          | MRO         | East North Central |          -0.1 | normal           | intentional attack | vandalism             |             nan |               1 |            nan |                nan |     12.12 |      9.71 |      6.49 |        9.28 | 1.58699e+06 | 1.80776e+06 | 1.88793e+06 | 5.28423e+06 |    30.0325 |    34.2104 |    35.7276 |       2345860 |        284978 |          9898 |         2640737 |      88.8335 |      10.7916 |      0.37482 |            53499 |          49091 |        1.08979 |               1.9 |         5226 |        291955 |        1.79 |           2.2 |    5457125 |        73.27 |     15.28 |         2279 |    1700.5 |         18.2 |          2.14 |        0.6 |  91.5927 |       8.40733 |          5.47874 |
|   2 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00 |   3 | 2010 |    10 | Minnesota   | MN          | MRO         | East North Central |          -1.5 | cold             | severe weather     | heavy wind            |             nan |            3000 |            nan |              70000 |     10.87 |      8.19 |      6.07 |        8.15 | 1.46729e+06 | 1.80168e+06 |  1.9513e+06 | 5.22212e+06 |    28.0977 |     34.501 |     37.366 |       2300291 |        276463 |         10150 |         2586905 |      88.9206 |       10.687 |     0.392361 |            50447 |          47287 |        1.06683 |               2.7 |         4571 |        267895 |     1.70627 |           2.1 |    5310903 |        73.27 |     15.28 |         2279 |    1700.5 |         18.2 |          2.14 |        0.6 |  91.5927 |       8.40733 |          5.47874 |
|   3 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00 |   4 | 2012 |     6 | Minnesota   | MN          | MRO         | East North Central |          -0.1 | normal           | severe weather     | thunderstorm          |             nan |            2550 |            nan |              68200 |     11.79 |      9.25 |      6.71 |        9.19 | 1.85152e+06 | 1.94117e+06 | 1.99303e+06 | 5.78706e+06 |    31.9941 |    33.5433 |    34.4393 |       2317336 |        278466 |         11010 |         2606813 |      88.8954 |      10.6822 |     0.422355 |            51598 |          48156 |        1.07148 |               0.6 |         5364 |        277627 |     1.93209 |           2.2 |    5380443 |        73.27 |     15.28 |         2279 |    1700.5 |         18.2 |          2.14 |        0.6 |  91.5927 |       8.40733 |          5.47874 |
|   4 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00 |   5 | 2015 |     7 | Minnesota   | MN          | MRO         | East North Central |           1.2 | warm             | severe weather     | nan                   |             nan |            1740 |            250 |             250000 |     13.07 |     10.16 |      7.74 |       10.43 | 2.02888e+06 | 2.16161e+06 | 1.77794e+06 | 5.97034e+06 |    33.9826 |    36.2059 |    29.7795 |       2374674 |        289044 |          9812 |         2673531 |      88.8216 |      10.8113 |     0.367005 |            54431 |          49844 |        1.09203 |               1.7 |         4873 |        292023 |      1.6687 |           2.2 |    5489594 |        73.27 |     15.28 |         2279 |    1700.5 |         18.2 |          2.14 |        0.6 |  91.5927 |       8.40733 |          5.47874 |

### Univariate Analysis

Let's look at the `OUTAGE.DURATION` distribution since that is what we are trying to predict.

<iframe
  src="assets/uni-plot1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

There are very big outliers, but not many of them. I consider dropping them later down.

I had a strong feeling that the outage cause category would be significant in predicting outage duration. Let's look at a barplot of outage causes.

<iframe
  src="assets/uni-plot2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Severe weather and intentional attacks are the most common causes for outages by a lot.

### Bivariate Analysis

I wanted to observe any possible correlation between `CAUSE.CATEOGORY` and outage duration

<iframe
  src="assets/bi-plot1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Severe weather seems to have a very concentrated distribution with high outage durations. Also, intentional attack has the widest distribution.

Let's visualize the average outage duration for each state.

<iframe
  src="assets/bi-plot2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Looks like east mid west has longer outages.

### Interesting Aggregates

Let's make a pivot table of cause category to features associated with outage severeness. I wanted to see if these three features have similar distributions to each other to help my model feature selection.

| CAUSE.CATEGORY | CUSTOMERS.AFFECTED | DEMAND.LOSS.MW | OUTAGE.DURATION |\n|:------------------------------|---------------------:|-----------------:|------------------:|\n| equipment failure | 101936 | 372.4 | 1816.91 |\n| fuel supply emergency | 0.142857 | 540.222 | 13484 |\n| intentional attack | 1790.53 | 9.15135 | 429.98 |\n| islanding | 6169.09 | 396.564 | 200.545 |\n| public appeal | 7618.76 | 1784.93 | 1468.45 |\n| severe weather | 188575 | 618.662 | 3883.99 |\n| system operability disruption | 211066 | 928.905 | 728.87 |

## Assessment of Missingness

### NMAR Analysis

`DEMAND.LOSS.MW` is likely to be NMAR because the main use for that column is to assess the money lost. However, if it was a small outage, then there is less of a need to report it. Because the missing data is influenced by the magnitude of the outage and its perceived importance, it is not missing at random.

To see if `DEMAND.LOSS.MW` could be MAR, I would investigate if there is a dependency on CUSTOMERS.AFFECTED, since I believe the amount of energy lost would be more important when it involves a lot of customers.

### Missingness Dependency

#### Finding a MAR column

Now I want to find a column that is MAR. I will select `CAUSE.CATEGORY.DETAIL` because I believe whether it is missing depends on `CAUSE.CATEGORY`, since there might not be a need for more detail if the category is sufficient enough.

**Null Hypothesis**: The distribution of `CAUSE.CATEGORY` is the same whether or not `CAUSE.CATEGORY.DETAIL` is missing.

**Alternate Hypothesis**: The distribution of `CAUSE.CATEGORY` is different when `CAUSE.CATEGORY.DETAIL` is missing.

**Test Statistic**: TVD since categorical and two sided.

<iframe
  src="assets/missing-plot1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The observed TVD is 0.2885925544217483, and I found a p-value of 0.0. Therefore, we have enough evidence to reject the null. Detail missing is likely to be MAR on cause category.

#### Finding a not MAR column

Now I want to find a column's whose missingess is not dependent on another column. I will show that the missingness of `DEMAND.LOSS.MW` is not dependent on `RES.PERCEN`, since `DEMAND.LOSS.MW` is also a measure of severity.

**Null Hypothesis**: The distribution of `CAUSE.CATEGORY` is the same whether or not `DEMAND.LOSS.MW` is missing.

**Alternate Hypothesis**: The distribution of `CAUSE.CATEGORY` is different when `CAUSE.CATEGORY.DETAIL` is missing.

**Test Statistic**: Difference of means

<iframe
  src="assets/missing-plot2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Observed difference in mean 0.08070380363253488, with a p-value of 1.0. Therefore we can say that the missingness of `DEMAND.LOSS.MW` is not dependent on `RES.PERCEN`.

## Hypothesis Testing

### Permutation Test

I will test whether the cause category detail of coal and hurricanes have the same outage duration averages. I will perform a permutation test.

**Null**: The average of `OUTAGE.DURATION` for `CAUSE.CATEGORY.DETAIL_Coal` and `CAUSE.`CATEGORY.DETAIL_hurricanes` is the same

**Alterantive**: The average of `OUTAGE.DURATION` for `CAUSE.CATEGORY.DETAIL_Coal` is greater than the average with `CAUSE.CATEGORY.DETAIL_hurricanes`

**Test Statistic**: Difference in means of `OUTAGE.DURATION` of the two groups.

The observed difference in means was -18228.944, with a p-value of 0. We reject the null hypothesis and can conclude that, on average, the outage duration for coal is greater than hurricanes.

<iframe
  src="assets/hypo-plot1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

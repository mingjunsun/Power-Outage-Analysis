# Power Outage Analysis
This is the Project 3 under course DSC80
by Mingjun Sun and Ziqi Shang


## Introduction

In both industrial production and daily life, electricity is a fundamental resource utilized extensively. As a result, power outages can profoundly impact individuals and operations. By meticulously examining power outage data, we aim to effectively improve the quality of electricity usage, identify potential causes of outages, and formulate corresponding enhancement strategies.

This project revolves primarily around a pivotal inquiry: "Where and when are major power outages most prevalent?" Understanding the geographic locations and timeframes prone to significant power outages enables the identification of patterns, facilitating preparedness and further research to mitigate sudden power disruptions.

This dataset includes 56 column variables and 1534 row variable in total. The columns include relative informations about major power outage events in the continental U.S., such as 

| Column                    | Description                                                 |
| ------------------------- | ----------------------------------------------------------- |
| `OUTAGE.START.DATE`       | The date when an outage occurs                              |
| `OUTAGE.START.TIME`       | The time when an outage occurs                              |
| `OUTAGE.DURATION`         | The minutes an outage lasts                                 |
| `CLIMATE.CATEGORY`        | The climate type(warm, normal, cold) where an outage occurs |
| `U.S._STATE`              | The state where an outage occurs                            |
| `OUTAGE.RESTORATION.DATE` | The date when an outage ends                                |
| `CAUSE.CATEGORY.DETAIL`   | The detailed reason for an outage.                          |

We noted that there're many redundant data in the dataset, such as the `YEAR` and `MONTH` column is duplicated since `OUTAGE.START.DATE` is included. And with `OUTAGE.DURATION`, `OUTAGE.START.DATE` and `OUTAGE.START.TIME`, we can directly compute the `OUTAGE.RESTORATION.DATE` and the `OUTAGE.RESTORATION.TIME`. Therefore, we exclude some of such columns from our analysis.

Note that we take into account some columns that seems unrelated to our question, but it's useful to detect the below topic of missingness.

## Data Cleaning and EDA

### Data Cleaning

#### Combine Data Column and Time Column

It's not convenience if a specific datetime is described by two columns, so first we combine the `OUTAGE.START.DATE` column and the `OUTAGE.START.TIME` column into a single `OUTAGE.START` column with the `pd.Timestamp` type.

The cleaned dataframe's datatype for each useful column is listed below.

```
OUTAGE.START               datetime64[ns]
OUTAGE.DURATION                    object
U.S._STATE                         object
CLIMATE.CATEGORY                   object
OUTAGE.RESTORATION.DATE            object
CAUSE.CATEGORY.DETAIL              object
dtype: object
```

The cleaned dataframe is shown below(with only representative rows selected for display).

| OUTAGE.START        |   OUTAGE.DURATION | U.S._STATE   | CLIMATE.CATEGORY   | OUTAGE.RESTORATION.DATE   | CAUSE.CATEGORY.DETAIL   | OUTAGE.RESTORATION.MISSING   | OUTAGE.DURATION.MISSING   | OUTAGE.START.MISSING   | CAUSE.CATEGORY.DETAIL.MISSING   | RANDOM   | NO_RANDOM   |
|:--------------------|------------------:|:-------------|:-------------------|:--------------------------|:------------------------|:-----------------------------|:--------------------------|:-----------------------|:--------------------------------|:---------|:------------|
| 2015-07-18 02:00:00 |              1740 | Minnesota    | warm               | 2015-07-19 00:00:00       | nan                     | False                        | False                     | False                  | True                            | a        | False       |
| 2012-06-19 04:30:00 |              2550 | Minnesota    | normal             | 2012-06-20 00:00:00       | thunderstorm            | False                        | False                     | False                  | False                           | b        | False       |
| 2010-10-26 20:00:00 |              3000 | Minnesota    | cold               | 2010-10-28 00:00:00       | heavy wind              | False                        | False                     | False                  | False                           | b        | True        |
| 2014-01-24 00:00:00 |            108653 | Wisconsin    | cold               | 2014-04-09 00:00:00       | Coal                    | False                        | False                     | False                  | False                           | b        | True        |
| 2013-08-12 11:55:00 |                 4 | Oregon       | normal             | 2013-08-12 00:00:00       | suspicious activity     | False                        | False                     | False                  | False                           | b        | False       |


### Univariate Analysis

#### Distribution of Outage Duration

In the univariate analysis, we would analyze the distribution of the outage frequency in each state and climate categories.

<iframe src="assets/univariate1.html" width=800 height=600 frameBorder=0></iframe>

This shows that normal climate seems to have more frequent power outage out of the three climate categories, which differs from what we expected that power outage ususlly occur under abnormal climate. Thus, we will further investigate this phenomenon in later analysis. 

#### Distribution of Outage U.S. States

Then, we use the same way to analyze the distribution of U.S. states of the outages.

<iframe src="assets/univariate2.html" width=800 height=600 frameBorder=0></iframe>

The plot above is skewed right, with most of states have low frequencies of power outage. Some states, such as California, Texas, Washington, Michigan, and so on has frequently occur power outages(more than 50 times throughout 2000 to 2016), while there are also some other states that are less frequent, which only have several power outages throughout 2000 to 2016.

### Bivariate Analysis

Then, we perform bivariate analysis between the outage duration and the climate where the outage occurs.

<iframe src="assets/bivariate1.html" width=800 height=600 frameBorder=0></iframe>

Since duration is one of the biggest matrix to determine the severeness of an outage event. In this part, we will use boxplot to present the relationship between outage durantion and each climate category. Accoridng to the boxplot above, "normal" climate has a median outage duration of 563 minutes, "cold" climate has a median outage duration of 816 minutes, and "warm" climate has a median outage duration of 881 minutes, which illustrates the "normal" climate seems to have lower outage durantion compare to abnormal climates (cold and warm). However, "cold" and "normal" climate seems to have more outliers.

To further explore this relationship, we calculate the average outage duration minutes of different states and plot a line graph.

<iframe src="assets/bivariate2.html" width=800 height=600 frameBorder=0></iframe>

The previous univariate analysis highlighted states like California, Texas, Washington, Michigan, and New York as experiencing the most frequent outage events. However, the bivariate plot suggests that other states, such as Alaska, Wisconsin, and West Virginia, might have notably longer outage durations. This implies that states with the highest frequency of outages might not necessarily experience longer outages. Besides, we also found that Alaska barely experience power outage, but it has the longest duration of outage. To explore further on what really affects a major power outage, we're calculating the median and quartiles of outage durations for these specific states."

<iframe src="assets/bivariate3.html" width=800 height=600 frameBorder=0></iframe>

The graph reveals significant variations in outage durations across different states. We would see that the overall trend for outage duration in different states shows a increasing trend in this graph. States with higher mean and median outage durations might experience more prolonged power outages on average compared to states with lower values. States like Kansas, District of Columbia, Florida, and Louisiana display wider ranges of outage durations, implying a mix of both shorter and longer outages within these regions. Arizona stands out with a paradoxical pattern—despite a relatively high mean outage duration, it has significantly low median, 25th quartile, and 75th quartile values.

### Interesting Aggregates

In the above mentioned bivariate analysis, we get a grouped table stat_df. Here, we show it again in table form. Below shows some rows of a grouped table aggreated by states and calculated the mean, median, and quartile of the outage duration.

| U.S._STATE    |     mean |   median |    25Q|      75Q|
|:--------------|---------:|---------:|------:|--------:|
| Mississippi   |   84     |     17.5 |   4   |   97.5  |
| Hawaii        |  845.4   |    543   | 237   | 1367    |
| Massachusetts |  944.167 |    211   |  19   | 1443.75 |
| Tennessee     | 1041.97  |    310   |  39   | 1230    |
| New York      | 6034.96  |   2880   | 268.5 | 8156.25 |

We aim to investigate the correlation between various climate categories (as indicated in the 'CLIMATE.CATEGORY' column) and the outage durations across different states. Below is a pivot table depicting this relationship. (A limited number of rows are displayed for clarity.)

| U.S._STATE    |    cold |  normal |    warm |
| :------------ | ------: | ------: | ------: |
| Wisconsin     | 12545.6 | 3962.56 |    1605 |
| Hawaii        |    1367 |   205.5 |  1224.5 |
| Massachusetts | 160.333 | 1159.38 |     721 |
| Tennessee     | 1476.58 | 1015.75 | 341.857 |
| New York      | 8914.58 | 3673.56 | 6092.83 |

To observe it more intuitively, we plot the table below.

<iframe src="assets/bar_climate_category.html" width=800 height=600 frameBorder=0></iframe>

It appears that there's a considerable variation in outage durations across different climate categories, suggesting a potential trend where warmer climates experience longer outages. However, to ascertain the validity of this observation, we'll conduct a hypothesis test.

## Assessment of Missingness

### NMAR Analysis

The criteria of determining whether the missing variable depends on itself. One of the column in our dataset with missing values that is possibly NMAR is the CAUSE.CATEGORY.DETAIL column. One possible reason might be issues during data collection, such as incomplete forms, human error, or system failures, could lead to missing information. Another possible reason might be vertain outages might occur due to rare or unclassified events that don't fit into the predefined categories. If we can include a column INCIDENT_REPORTING_LEVEL indicating higher or lower reporting levels might influence whether the cause details were captured, we might establish a MAR pattern for the missingness of the CAUSE.CATEGORY.DETAIL column.

### Missingness Dependency

Now, we decided to test the dependency between the missingness of OUTAGE.DURATION with two columns: OUTAGE.START and CAUSE.CATEGORY.DETAIL..

#### OUTAGE.DURATION and OUTAGE.RESTORATION.DATE (MAR)

Null hypothesis: The missingness of outage duration does not depend on outage start time.
Alternative hypothesis: The missingness of outage duration does depend on outage start time.

we first create an additional column OUTAGE.START.MISSING to record the whether the OUTAGE.START is missing.

Since it's a category column, we use TVD(Total Variation Distance) to perform permutation test. Recall that:

$$
TVD(X,Y) = \dfrac 12\sum_{i=1}^{n} \left| X_i - Y_i \right|
$$

The null hypothesis for the permutation test is that the specific column does not depend on another column. So if p-value is less than the significance level, we reject the null hypothesis, which means that we may think the column depend on another. Otherwise, we fail to reject the null hypothesis, which means that it's more possible to believe the column does not depend on another.

Below, we computed the TVD, run the permutation tests of 500 rounds, set the significance level to 0.05, calculate the p-value, draw the plot and get the conclusion.

The result is shown below.

<iframe src="assets/permutation_test_OUTAGE.DURATION.MISSING_OUTAGE.RESTORATION.MISSING.html" width=800 height=600 frameBorder=0></iframe>

The calculated p-value is 0.0 which is below the significant level 5%. Thus, we reject the null hypothesis that the missingness of outage duration does not depend on outage start time. Based on our test result, we can see that the missingness of the OUTAGE.DURATION is MAR because the missingness of OUTAGE.DURATION is correlated with the OUTAGE.START.

#### OUTAGE.DURATION and CAUSE.CATEGORY.DETAIL (MCAR)

Null hypothesis: The missingness of outage duration does not depend on the cause of outage.
Alternative hypothesis: The missingness of outage duration does depend on cause of outage.

<iframe src="assets/permutation_test_OUTAGE.DURATION.MISSING_CAUSE.CATEGORY.DETAIL.html" width=800 height=600 frameBorder=0></iframe>

The plot result shows that p-value is 0.86, which is largely greater than the significance level of 5%, so we fail to reject the null hypothesis. Hence, we conclude that the missingness of OUTAGE.DURATION does not depend on the missingness of CAUSE.CATEGORY.DETAIL (MCAR).


## Hypothesis Testing

### Permutation Test

Returning to our investigation focus, we're exploring whether climate categories influence power outage durations. Reflecting on the plot in the intriguing aggregates section, we've conceived the notion that there might be connections between climate and power outage durations. However, relying solely on observations lacks robustness. Therefore, we will be conducting a hypothesis test to determine whether to reject our thought.

We will perform permutation test on the distribution of outage durations in the warmer climate areas and the distribution in the cold climate areas to see whether there is an actual increase in duration time to cold areas or not.

Null hypothesis: the outage duration time in the warm climate and the cold climate comes from the same distribution.

Alternative hypothesis: the outage duration time in the warm climate and the cold climate comes from different distributions.

These hypotheses directly address the question by comparing outage durations in warmer and cold climates. They provide a clear distinction: one assumes no difference in distributions (null hypothesis), while the other anticipates a difference (alternative hypothesis), aligning perfectly with the investigation goal.

Significance level: 5%, which is standard in statistical analysis.
Number of permutation test rounds: 500 that using 500 permutation test rounds ensures a robust assessment. However, we don't want the permutation test to run too long which decrease the efficiency.

Test statistics: the outage duration is numeric data rather than categorical. And recall that numeric data uses diff of means, which the categorical data uses TVD. Thereby, we use the diff of means as our test statistics.

The value is numeric rather than category, so we use the absolute difference between the mean of the two groups to check it. Recall the formula:

$$
diff\_of\_means(A,B) = |\dfrac 1n\sum_{i=1}^nA_i
- \dfrac 1m\sum_{i=1}^mB_i|
$$

Using the similar way, we perform the permutation test below.

<iframe src="assets/hyp_OUTAGE.DURATION_CLIMATE.CATEGORY_warm_cold.html" width=800 height=600 frameBorder=0></iframe>

### Conclusion

Surprisingly, our analysis revealed a p-value of 0.478, surpassing the predetermined significance level of 0.05. As a result, we do not have sufficient evidence to reject the null hypothesis. This outcome indicates that the outage duration times in both warm and cold climates likely stem from the same distribution. Consequently, our investigation does not support the presence of a relationship between outage duration and climate categories.

This unexpected outcome challenges our initial intuition and highlights the complexity of factors influencing outage durations, suggesting that climate might not play a significant role in this particular scenario. It is also plausible that power outage duration is not the major criteria of determining major power outage, so we need to analysis the correlation in multiple dimensions. Further exploration and consideration of additional variables may be necessary to comprehensively understand the multifaceted determinants affecting major power outages in the US.

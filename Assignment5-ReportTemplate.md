# SENG-637 Assignment 5

**Topic** - Software Reliability Assessment

## Table of Contents

- [Introduction](#introduction)
- [Video demo](#video-demo)
- [Assessment using Reliability Growth Testing](#assessment-using-reliability-growth-testing)
- [Assessment using Reliability Demonstration Chart](#assessment-using-reliability-demonstration-chart)
- [Comparison of results](#comparison-of-results)
- [Similarity and Differences between the two techniques](#similarity-and-differences-between-the-two-techniques)
- [Division of team work](#division-of-team-work)
- [Difficulties, challenges, and lessons learned](#difficulties-challenges-and-lessons-learned)
- [Comments and feedback](#comments-and-feedback)
- [Contributors](#contributors)

## Introduction

In this assignment we will explore the use of Reliability Growth Testing (RGT) and Reliability Demonstration Chart (RDC) to assess the reliability of a software system.

Then we will compare these techniques to see what they are like and how they are different from one another.

## Video demo

Link to the video demonstration of killed/surviving mutants and is _TBA_.

## Assessment using Reliability Growth Testing

For Reliability Growth Testing, after testing the tools suggested by the assignment guideline, we decided to utilize the tool C-SFRAT. The reason for choosing C-SFRAT is because the tool has a very user-friendly way to compare the models with different combinations of covariates.

For C-SFRAT, our approach is to first run the entire set of failure data with every model and covariate combination possible, and then figure out which model is best suitable for the behavior of the failure data. (see model_comparison.csv).

For comparing the models, we will be using the AIC and BIC criterias that is computed by C-SFRAT, which are widely used in model selection. The model with the best AIC should have the greatest amount of variation using the fewest possible independent variables, and the lower the AIC score, the better[[1]](https://www.scribbr.com/statistics/akaike-information-criterion/).

BIC, on the otherhand, provides a good estimation on the performance of the model for future data and is also the lower the BIC score the better[[2]](https://stanfordphd.com/BIC.html).

After analyzing the Model Comparison tables from CS-FRAT, we have found the models Discrete Weibull Type 3 with covariate F is the best model with AIC of 122.199 and BIC of 127.935. The second best model is Geometric Model with covariate F with AIC of 125.323 and BIC of 129.625.

After testing with the various ranges on these models, we have found that using subset of 21 out of the full set of 31 (approximately 67-70% of the full failure dataset) and setting the Covariate F as 20 efforts per interval gave us the best performance of the models.

One of the reason that we can think of is that from the 20th interval there has been a huge up-climb of failures and as such using only the subset before interval 20 will not effectively predict the failures with the models.

Time to Failure Plot of the two models:

![TTF plot](images/time-between-failures.PNG)

Intensity Plot

![Intensity plot](images/intensity.PNG)

As can be seen from the plot, the failure rate and MTTF for the original failure data and the predictions at the last interval (31) are:

| Dataset        | Failure Rate (F/Interval) | MTTF (intervals) |
| -------------- | ------------------------- | ---------------- |
| Raw Data       | 92/31 = 2.96              | 1/2.96 = 0.337   |
| DW3 Prediction | 91/31 = 2.94              | 1/2.94 = 0.341   |
| GM Prediction  | 90/31 = 2.90              | 1/2.90 = 0.344   |

Using interval 21 for DW3 and GM models to predict the last 10 intervals, the failure rate and MTTF are very close to the original failure data.

### Application to Decision-Making given Target Failure Rate
So how can we utilize the prediction from the models? Businesses who are concerned with their software reliability will have a target failure rate or the mean time to failure that is considered unacceptable.
For example, if the business deems the acceptable failure rate to be 3 Failures/Interval, then at 31st interval we are still good since the raw data is at 2.96Failures per interval. However, using our Discrete Weibull Type 3 model, we can predict at which interval point in the future that the failure rate may become unacceptable. 

### Advantages/Disadvantages of Reliability Growth Testing
The advantage for Reliability Growth Testing is that it allows the user to predict the failure behavior using a set of predefined models and using AIC and BIC it is easy to compare which models is the best for the data.
One disadvantage for the reliability growth testing is that the predictions are very dependent on the subset of data (or ranges) of which the data is used for the prediction. For example, if there are some distortions or outliers of data within the subset of data, the predictions might not be accurate. 
Another disadvantage, comparing to the RDC that we are to discuss next, is that it can only use target failure rate or mttf to gauge the acceptableness, but does not account for more detail such as user and developer’s appetite for risk etc. 


## Assessment using Reliability Demonstration Chart

The RDC-11 Excel sheet was used for plotting RDC graphs. As it was only configured to plot 16 failures it had to be modified for this data.

The default risk profile was used -

| Parameter                | Value |
| ------------------------ | ----- |
| Discrimination Ratio (γ) | 2     |
| Developer's Risk (α)     | 0.1   |
| User's Risk (β)          | 0.1   |

The failure data had to be modified before plotting. As the data was given as failures per interval, not time between failures. This was done by assuming failures were uniformly distributed in each interval. For example, if given data is

| T   | FC  |
| --- | --- |
| 1   | 2   |
| 2   | 3   |

Then it would be converted into time between failures as follows. At `T=1`, there are two failures, it can be assumed that one failure is at `T=0.5` and the other is at `T=1`.

| Cumulative Failure Count | Time between failures | Cumulative Time |
| ------------------------ | --------------------- | --------------- |
| 1                        | 0.5                   | 0.5             |
| 2                        | 0.5                   | 1               |
| 3                        | 0.33                  | 1.33            |
| 4                        | 0.33                  | 1.67            |
| 5                        | 0.33                  | 2               |

The converted data used for RDC can be found **[here](Data_for_RDC.xlsx)**.

#### RDC Plots

1. The first plot below shows the RDC graph using the calculated MTTF of the SUT, FIO = 92 failures/31 intervals = 2.97, giving MTTF = 1/2.97 = 0.337

   ![RDC plot Initial](images/RDC_Initial.PNG)

2. The second plot is the minimum MTTF for the system to be considered acceptable. This minimum was determined by changing the FIO until a minimum was found; where the SUT barely enters the accept region. It was determined the FIO was 675/31 = 21.77 failures per interval. Giving a MTTF of 0.046.

   ![MTTFmin RDC plot](images/RDC_MTTFmin.PNG)

3. The third plot is double the minimum MTTF. This gives a MTTF of 0.092 and a FIO of 337.5/31 = 10.89 failures per inteval. In this case the SUT almost immediately goes into the reject region.

   ![MTTFmin doubled RDC plot](images/RDC_MTTFdouble.PNG)

4. The fourth plot is half the minimum MTTF. This gives a MTTF of 0.023 and a FIO of 1350/31 = 43.55 failures per inteval. In this case the SUT immediately goes into the accept region.

   ![MTTFmin half RDC plot](images/RDC_MTTFhalf.PNG)

## Comparison of results

## Similarity and Differences between the two techniques

Similarities between Reliability Growth Testing (RGT) and Reliability Demonstration Chart (RDC) are as listed below.

#### Similarities

1. Both techniques are used to get the reliability statistics of the system.

2. Both rely on target failure rate or MTTF.

3. Both need failure data to work.

#### Differences

1. RGT is used to make reliability predictions based on exising failure data, whereas RDC is a way of checking whether the target failure rate is met or not in the existing failure data.

2. RGT uses failure curve models to make reliability predictions, whereas RDC uses risk profile to verify the reliability of the system.

3. RGT will simply make predictions based on the failure data and model selected, whereas RDC will determine if the SUT is acceptable based on risk profile.

## Division of team work

| Tester                   | Section                                                                          |
| ------------------------ | -------------------------------------------------------------------------------- |
| Bhavyai Gupta            | Assessment using RDC, RDC vs RGT, Difficulties & challenges, Comments & feedback |
| Drew Burritt             | Assessment using RDC                                                             |
| Michael Man Yin Lee      | Assessment using RGT, RDC vs RGT                                                 |
| Okeoghenemarho Obuareghe | TBA                                                                              |

## Difficulties, challenges, and lessons learned

1. While choosing tools for reliability testing, we had some difficulties in getting the right tool for our project.

   - Our group first decided to test **SRTAT-SRE-tool**. This tool could import the sample data files but couldn't import the target data file. There wasn't any documentation available for the tool either, on how to use it and what input formats it supports. The [help page](http://www.dtic.mil/dtic/tr/fulltext/u2/a275405.pdf) (under Help menu) for the tool was not available either.

   - Then we tested **CASRE**. This tool couldn't even launch on 64-bit Windows 11 machine. Every executable we tried threw below error.
     ![CASRE Error](images/casre_error.png)

   - Finally, we tested **C-SFRAT** and it worked fine.

2. The given Reliability Demonstration Chart (RDC) data was not in accordance with the given failure data of the SUT. The RDC had to be modified to accept as many data points as given in the failure data.

## Comments and feedback

1. The assignment description document [`Assignment5.md`](Assignment5.md) is not very detailed this time.

2. One of the recommended software **SRTAT-SRE-tool** is very hard to use. There is no documentation available for it as well.

3. The Reliability Demonstration Chart should have allowed input of all the failure data.

## Contributors

We are group 5, and below are the team members

- [Bhavyai Gupta](https://github.com/zbhavyai)
- [Drew Burritt](https://github.com/dburritt)
- [Michael Man Yin Lee](https://github.com/mlee2021)
- [Okeoghenemarho Obuareghe](https://github.com/oobuareghe)

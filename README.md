# Taxi-Demand-Prediction-in-New-York-City
BUSINESS UNDERSTANDING
Medallion (yellow) cabs are concentrated in the borough of Manhattan but can be hailed anywhere throughout the five boroughs of New York City with a raised hand or from a taxi stand.

In the New York city, people use taxis at a much higher frequency than most places. Instead of booking customers by phone ahead of time, there is still a majority of New York taxi drivers that pick-up passengers on street. Hailing a cab is as simple as stepping off the curb and holding out your arm out

In 2011, Uber announced it was launching its ride sharing service in New York City, ushering in a new era of disruption in the taxi service industry

By allowing anyone to enroll as a taxi driver with their own private vehicle and making it as easy as a click of a cell phone app to book a ride, Uber began establishing a new network of cabs outside of the medallion system.As per recent studies, Taxi patronage has considerably declined since 2011 due to competition from these ride share services.

What if there existed a way to predict taxi ridership that gives valuable insights to taxi dispatchers — as in how to position cabs where they are most needed, how many taxis to dispatch, and how ridership varies over time. Such prediction will help dispatchers immensely in making important decisions that could revive their profit margin.

DATA UNDERSTANDING
For solving any business problem, it is essential to understand the strengths and limitations of the data from which the solution will be built. The data used for building our solution was collected and provided to the NYC Taxi and Limousine Commission (TLC) by technology providers authorized under the Taxicab & Livery Passenger Enhancement Programs (TPEP/LPEP). For the purpose of our modeling we’ll be using the data from January 2015.

Data Source: https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page

DATA CLEANING
Univariate analysis of the data is performed wherein all erroneous data was removed.

As a part of the data cleaning phase we check if the pick-up latitude and longitude fall within NYC and remove the ones that don’t.

<img width="423" alt="image" src="https://github.com/user-attachments/assets/1550e53e-710a-4d04-8c33-359d6f8b84f2" /> 

DATA PREPARATION
Good data preparation allows for efficient analysis and limits the errors and inaccuracies that occur due to erroneous data

1. Clustering and segmentation :
Since a part of our solution is to predict the pickups in a region within some time interval, we need to derive regions and time intervals from data.Lets begin with dividing NYC into K clusters using the latitude and longitude of pickups. Using K-Means clustering, we cluster the pick-up points into different regions.

Since K-Means creates clusters of roughly same sizes(clusters of same number of points), the regions with more number of pickups form smaller and dense clusters whereas regions with lesser number of pickups get connected into larger and loose/sparse clusters.

<img width="782" alt="image" src="https://github.com/user-attachments/assets/4353196f-29c1-41d7-a8d6-1e04df61d798" />

From the data, we observe that a taxi can cover up to 2 miles in 10 minutes. Therefore, we want the inner cluster distance to be greater than 2 miles but not lesser than 0.5 miles. The optimal K value must meet this constraint. We tried a range of different cluster values from 30 to 70 clusters. After trying a different range of clusters, we observed that when the number of clusters is 30.

<img width="844" alt="image" src="https://github.com/user-attachments/assets/bce04502-4346-4c83-8e99-5bb605980f65" /> 

The number of clusters within the 2-mile radius : 7.0

The number of clusters outside the 2-mile radius : 23.0

·The minimum inter cluster distance : 0.475971

As we can see from the graphs that the above condition is met, we choose that as the optimal value for k as 30

2. Time Binning
Every region is split into 10-minute interval, which corresponds to one time bin, i.e. each time bin has 10 minute. However, in the data we have time in the format “YYYY-MM-DD HH:MM:SS which are converted to Unix time format so as to retrieve time in minute/hour format. Number of possible 10 min interval bins : 4464

Total number of pickup_bins from clusters : 130567

Number of possible pickup_bins from all clusters : 133920

Now, with any region-ID and 10 min time bin, we can predict the number of pickups.

3. Smoothing the Data

Now that the data is divided into 10-minute interval bins, each bin should contain at least one pickup. There are chances that a time bin may contain zero pick-ups leading to ratio feature error. In order to smooth this outlier:

<img width="757" alt="image" src="https://github.com/user-attachments/assets/f86566a3-e0c0-4448-8613-2c02470a274d" /> 

We count the total number of pickups in each bin.

There would be no value if there were no pickups in a time bin.Fill the missing pickup time bins with 0.

Then, we add a few pickups from neighboring bins to the bin which contains zero pickups in order to make all the neighboring bin pickups equal.

4. Time series and Fourier Transforms

In theory, any waveform can be represented as the sum of infinite sine waves. Each sine wave has some amplitude and frequency. We can see that the number of pickups in a month in every cluster form a repeating pattern. We do not know the frequency of the repeating pattern. Our pattern cannot be represented by a single frequency as it’s aperiodic. Instead, it is composed of infinite sine wave with each sine wave having a frequency. Fourier transform lets us represent our pattern from time domain (number of pickups per time) to frequency domain(can be viewed as number pickup bins with highest number of pickups).

For each cluster there exists a pattern and using the Fourier transform we can deduce the top frequencies and amplitudes of sine waves which compose our pattern from cluster and use them as features. The frequencies and amplitudes of a cluster are indicative of demand in that cluster. So they can be fed into the model for prediction of number of pickups.

<img width="890" alt="image" src="https://github.com/user-attachments/assets/df46dcf3-57e4-4cc4-90b3-b7d19bcff8be" /> 
Plotting the Amplitude and frequency of the sinusoidal components, we see that

1. The pattern whose repetition is very high will have a high frequency component and vice versa.

2. There are high frequency in morning and evening time durations as the pick-ups are high during peak hours. The same applies to day and night but with less frequencies.

We see that there is very high amplitude at f=0. As the frequency of a sine wave approaches zero, the sine wave tends to be axis-parallel/linear, appearing as a DC component. Since, the wave which we have Fourier transformed is a repeating wave, the DC component looks ambiguous as it is capturing the information of the previous part of the wave. Hence, we will not consider its amplitude and frequency.

MODELING
Baseline models
The first giant step to solving our business problem begins with baseline model. We will walk you through the process of discovering the right baseline model which gave us the best result(least errors!). We use these models with two variations

1. Using Ratios of the 2016 data to the 2015 data i.e. Ratio= P_2016/P_2015

2. Using Previous known values of the 2016 data itself to predict the future values

P_2016 and P_2015 are the pickup densities for their respective years.

This is our data frame with pickup densities of 2015 and 2016 and their ratios.

Our 3 choices were Simple Moving Averages, Weighted Moving Averages and Exponential Weighted Moving Average. For each of them, Ratios and Predictions were calculated for P_2015 and P_2016.


After comparing all the baseline using MAPE (Mean Absolute Percentage Error) as our error metric to know how good the model is with predictions and MSE (Mean Squared Error) to know how well our forecasting model performs with outliers so that we make sure that there is not much of an error margin between our prediction and the actual value.

Feature Engineering
After doing all the data cleaning and preparation and baseline modelling, we now have an arsenal of features which would help build a good predication model. We see that, from baseline modeling, how Exponential weighted moving averages gives the best forecasting among the rest. We will use this as a feature while building the regression model along with others we got from data preparation stage.

Below are the list of 14 features we use for modeling,

· Latitude and Longitude of the cluster

· Top three amplitudes with respective frequencies from fourier transform

· Five previous pickup densities from the cluster where the pickup density is to be predicted

· The pickup density predicted by the EMA_PREV baseline model


REGRESSION MODELS
Test and train split:

For test and train split by time, we take 3 months of 2016 pickup data and split it such that for every region we have 70% data in train and 30% in the test. As this is a time-series problem, we have to split our train and test data on the basis of time.

LINEAR REGRESSION
To begin with, we standardize the test and train data by removing the mean and scaling to unit variance. Then, we apply the model. We have used SDGRegressor as it is well suited for regression problems with large number of training and testing data. Also that it is much faster as it has lesser data to manipulate at every iteration.i.e picks a random instance in the training set at every step and computes the gradients based only on that single instance.

Tuning of Hyper Parameter is done using GridSearchCV() in order to find the best combination of parameter values,.i.e optimal parameter. We also do 3-fold cross validation to improve generalization performance.

DEPLOYMENT
Taxi services are a central transportation method in almost all urban areas. Like so many other fields today the taxi business is undergoing a rapid digital transformation with new actors like Uber taking market shares with innovative digital products. The most important objective for any taxi company and driver is to minimize the vacant driving time, and a very important aspect of this is to know where to find the passengers. Predicting where passengers is a problem ideally suited for a machine learning approach.

Exploiting an understanding of taxi supply and demand could increase the efficiency of the city’s taxi system. Overall, our models for predicting taxi pickups in New York City performed well. The XGboost regression model performed best, likely due to its unique ability to capture complex feature dependencies. We propose to develop a mobile application that would essentially use the insights from our model to position taxicabs with more efficiency. Our model could be used by both taxi drivers/dispatchers as well as city planners.

Some of the issues associated with the model’s use could be that we have used smoothing to average out the bins where pickups would be zero which impacts the accuracy of the model.

One of the major risks associated with our proposed solution is that sometimes there may be latency in Information. Given a location and current time of a taxi driver, as a taxi driver, he/she excepts to get the predicted pickups in his/her region and the adjoining regions. Therefore, it is essential to ensure minimal latency in information.

Some of the risks associated with this plan is that the Yellow taxi fleet itself might reduce year on year with drivers switching to Uber/Lyft hence our model might not have as much data to predict the pickup density accurately in the future.








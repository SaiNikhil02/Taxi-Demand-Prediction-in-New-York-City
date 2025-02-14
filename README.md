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



# Project 1: Query Project

- In the Query Project, I will practice SQL with
  Google Cloud Platform (GCP) and BiqQuery. Answer business-driven
  questions using public datasets housed in GCP. I used the web UI (BiqQuery) and
  command-line tools, and saved my work in a Jupyter notebook.


#### Problem Statement

- You're a data scientist at Lyft Bay Wheels (https://www.lyft.com/bikes/bay-wheels), formerly known as Ford GoBike, the
  company running Bay Area Bikeshare. You are trying to increase ridership, and
  you want to offer deals through the mobile app to do so. 
  
- What deals do you offer though? Currently, your company has several options which can change over time.  Please visit the website to see the current offers and other marketing information. Frequent offers include: 
  * Single Ride 
  * Monthly Membership
  * Annual Membership
  * Bike Share for All
  * Access Pass
  * Corporate Membership
  * etc.

- Through this project, you will answer these questions: 

  * What are the 5 most popular trips that you would call "commuter trips"? 
  
  * What are your recommendations for offers (justify based on your findings)?



## Parts 1, 2, 3


- In Part 1, I will query using the Google BigQuery GUI interface in the cloud.

- In Part 2, I will query using the Linux command line from our virtual machine in the cloud.

- In Part 3, I will query from a Jupyter Notebook in our virtual machine in the cloud, save the results into Pandas, and present a report enhanced by Pandas output tables and simple data visualizations using Seaborn / Matplotlib.

---

## Part 1 - Querying Data with BigQuery



Public Datasets: Bring up your Google BigQuery console, open the menu for the public datasets, and navigate to the the dataset san_francisco.

- The Bay Bike Share has two datasets: a static one and a dynamic one.  The static one covers an historic period of about 3 years.  The dynamic one updates every 10 minutes or so.  THE STATIC ONE IS THE ONE WE WILL USE IN THE PROJECT. 

- (USE THESE TABLES!) The static tables we will be using in this class are in the dataset **san_francisco** :

  * bikeshare_stations

  * bikeshare_status

  * bikeshare_trips

- The dynamic tables are found in the dataset **san_francisco_bikeshare**

### Some initial queries

Paste your SQL query and answer the question in a sentence.  Be sure you properly format your queries and results using markdown. 

- What's the size of this dataset? (i.e., how many trips)

```sql
SELECT count(distinct trip_id) FROM `bigquery-public-data.san_francisco.bikeshare_trips` 
```

**Answer : 983,648**

- What is the earliest start date and time and latest end date and time for a trip?

```sql
SELECT min(start_date), max(end_date)
FROM `bigquery-public-data.san_francisco.bikeshare_trips`
```

Earliest Start Date and Time | 2013-08-29 09:08:00 UTC
Latest End Date and Time | 2016-08-31 23:48:00 UTC


- How many bikes are there?

```sql
SELECT count(distinct bike_number)
FROM `bigquery-public-data.san_francisco.bikeshare_trips` 
```
**Answer : 700**


### Questions of your own
- Make up 3 questions and answer them using the Bay Area Bike Share Trips Data.  These questions MUST be different than any of the questions and queries you ran above.

- Question 1: What were the top 5 station IDs with the highest trips beginning from that station, with trip start dates between Sep 1, 2015 – Aug 31, 2016?

  * **Answer: Station IDs 70, 69, 50, 61, 74**


| Row | COUNT TRIPS | start station id |
|-----|-------------|------------------|
| 1 | 23591 | 70 |
| 2 | 22358 | 69 |
| 3 | 16128 | 50 |
| 4 | 14099 | 61 |
| 5 | 13693 | 74 |



  * SQL query:

```sql
SELECT COUNT(distinct trip_id) AS COUNT_TRIPS, start_station_id
FROM `bigquery-public-data.san_francisco.bikeshare_trips`
WHERE start_date between '2015-09-01 00:00:00' and '2016-08-31 23:59:00'
GROUP BY start_station_id
ORDER BY COUNT_TRIPS DESC LIMIT 5
```

- Question 2: How many trips did each category of subscriber type take from Sep 1, 2015 - Aug 31, 2016?

  * **Answer: Customer types made 33,596 trips and Subscriber types made 280,093 trips.**


Row |COUNT TRIPS|subscriber type
---| --- | ---
1 | 33596 | Customer
2 | 280093 | Subscriber


  * SQL query:

```sql
SELECT COUNT(distinct trip_id) AS COUNT_TRIPS, subscriber_type
FROM `bigquery-public-data.san_francisco.bikeshare_trips`
WHERE start_date between '2015-09-01 00:00:00' and '2016-08-31 23:59:00'
GROUP BY subscriber_type
```
- Question 3: What was the average ride duration from Sep 1, 2015- Aug 31, 2016?

  * **Answer: 828.8138697882232 seconds**

  * SQL query:
```sql
SELECT AVG(duration_sec)
FROM `bigquery-public-data.san_francisco.bikeshare_trips`
WHERE start_date between '2015-09-01 00:00:00' and '2016-08-31 23:59:00'
```


### Bonus activity queries (optional - not graded - just this section is optional, all other sections are required)

The bike share dynamic dataset offers multiple tables that can be joined to learn more interesting facts about the bike share business across all regions. These advanced queries are designed to challenge you to explore the other tables, using only the available metadata to create views that give you a broader understanding of the overall volumes across the regions(each region has multiple stations)

We can create a temporary table or view against the dynamic dataset to join to our static dataset.

Here is some SQL to pull the region_id and station_id from the dynamic dataset.  You can save the results of this query to a temporary table or view.  You can then join the static tables to this table or view to find the region:
```sql
#standardSQL
select distinct region_id, station_id
from `bigquery-public-data.san_francisco_bikeshare.bikeshare_station_info`
```

- Top 5 popular station pairs in each region

- Top 3 most popular regions(stations belong within 1 region)

- Total trips for each short station name in each region

- What are the top 10 used bikes in each of the top 3 region. these bikes could be in need of more frequent maintenance.

---

## Part 2 - Querying data from the BigQuery CLI 

- Use BQ from the Linux command line:

  * General query structure

    ```
    bq query --use_legacy_sql=false '
        SELECT count(*)
        FROM
           `bigquery-public-data.san_francisco.bikeshare_trips`'
    ```

### Queries

1. Rerun the first 3 queries from Part 1 using bq command line tool (Paste your bq
   queries and results here, using properly formatted markdown):

  * What's the size of this dataset? (i.e., how many trips)
  
  
```bq query --use_legacy_sql=false 'SELECT count(*) FROM `bigquery-public-data.san_francisco.bikeshare_trips`'```

Answer : 983,648


  * What is the earliest start time and latest end time for a trip?
  
  ```bq query --use_legacy_sql=false ' SELECT min(start_date), max(end_date) FROM `bigquery-public-data.san_francisco.bikeshare_trips`'```
  
Answer : Earliest start time is 2013-08-29 09:08:00 . Latest end time is 2016-08-31 23:48:00 .

  * How many bikes are there?
  ```bq query --use_legacy_sql=false ' SELECT count(distinct bike_number) FROM `bigquery-public-data.san_francisco.bikeshare_trips`'```
  
  Answer : 700 bikes

2. New Query (Run using bq and paste your SQL query and answer the question in a sentence, using properly formatted markdown):

  * How many trips are in the morning vs in the afternoon?
```bq query --use_legacy_sql=false 'SELECT COUNT(start_date) FROM `bigquery-public-data.san_francisco.bikeshare_trips` WHERE (CAST(start_date AS TIME) between "00:00:00" and "11:59:00" and duration_sec between 61 and 86400)'```

  
  Answer: Trips starting in the morning = 412,197
  

```bq query --use_legacy_sql=false 'SELECT COUNT(start_date) FROM `bigquery-public-data.san_francisco.bikeshare_trips` WHERE (CAST(start_date AS TIME) between "12:00:00" and "18:00:00" and duration_sec between 61 and 86400)'```   
  
  Answer: Trips starting in the afternoon 392,924


### Project Questions
Identify the main questions you'll need to answer to make recommendations (list
below, add as many questions as you need).

- Question 1: What are the top station pairs (start station, end station) for commuter trips? Broken down by customer Type?

- Question 2: What are the top visited landmark (cities)? 

- Question 3: What are the top visited station names? 

- Question 4: Average duration of a trip? 

- Question 5 : What are the top start stations? 

- Question 6 : Percentage of trips made by customer type?

- Question 7 : Docks available and bikes available – by landmark, day of week, time, number of docks?

- Question 8 : Percentage of people who pay overtime fee by customer type, by time, day of week, landmark, station pairs?

- Question 9 : Percentage of people who pay overtime fee by customer type, by time, day of week, landmark, station pairs?

- Question 10 : Seasons with high users?

- Question 11: Lowest and Highest Duration of Trips?

- Question 12 : What’s the top 5 stations with the highest average rides per dock in 2016?
 

### Answers

Answer at least 4 of the questions you identified above You can use either
BigQuery or the bq command line tool.  Paste your questions, queries and
answers below.

- Question 1: Lowest and Highest duration of Trips?
  * Answer:Lowest is 60 seconds. Highest is 17270400 seconds or 200 days.
  * SQL query:
```sql
SELECT duration_sec
FROM `bigquery-public-data.san_francisco.bikeshare_trips`
ORDER BY duration_sec ASC
```


- Question 2: What are the top station pairs (start station, end station) for commuter trips?  Broken down by customer Type?
  * Answer: Station ID Pairs (1) 69,65 , (2) 61, 50 (3) 50, 60 (4) 65, 69 (5) 60,74
 
| Row | start_station_id | end_station_id |
|-----|-------------|------------------|
| 1 | 69 | 65 |
| 2 | 61 | 50 |
| 3 | 50 | 60 |
| 4 | 65 | 69 |
| 5 | 60 | 74 |
 
 
  * SQL query:
```sql
WITH Q1 as (SELECT start_station_id, start_station_name, 
end_station_id, end_station_name,
SUM(sum_trips) as sum_trips
FROM `spring2021w205.project1w205.Final_Proj1_R`
WHERE avghours_dec<=1 and start_weekday_end="Weekday"
GROUP BY start_station_name,start_station_id, end_station_name, end_station_id)
SELECT start_station_id, start_station_name, 
end_station_id, end_station_name,sum_trips FROM Q1
ORDER BY sum_trips desc LIMIT 5
```   

  * Answer : Subscriber : 31,917 ; Customer : 2,849  
  
 * SQL query:
```sql
SELECT subscriber_type,
SUM(sum_trips) as sum_trips
FROM `spring2021w205.project1w205.Final_Proj1_R`
WHERE avghours_dec<=1 and start_weekday_end="Weekday" and 
((start_station_id=69 and end_station_id=65) or (start_station_id=61 and end_station_id=50) or (start_station_id=50 and end_station_id=60) 
or (start_station_id=65 and end_station_id=69) or (start_station_id=60 and end_station_id=74))
GROUP BY subscriber_type
```

- Question 3: What are the rides per dock in 2016 for each stations. (A) List the top 5 and (B) bottom 5 ? (C) Find the average rides per dock, count how many stations are above and below the average?
  * Answer to A: 
  
| Row | start_station_id | ride per dock |
|-----|-------------|------------------|
| 1 | 70 | 856 |
| 2 | 69 | 666 |
| 3 | 60 | 608 |
| 4 | 65 | 478 |
| 5 | 50 | 478 |
  
  * SQL query to A:
```sql
With twoa as(SELECT start_station_id, start_station_name,
SUM(sum_trips) as sum_trips, ROUND(AVG(avg_dockcount)) as avg_dockcount  
FROM `spring2021w205.project1w205.Final_Proj1_R`
WHERE start_year=2016
GROUP BY start_station_id, start_station_name),
twoatwo as(
SELECT start_station_id, start_station_name, sum_trips, avg_dockcount,ROUND(sum_trips/avg_dockcount) as rides_per_dock
FROM twoa)
SELECT start_station_id, start_station_name, sum_trips, avg_dockcount, rides_per_dock 
FROM twoatwo ORDER BY rides_per_dock desc LIMIT 5
```
  
 * Answer to B:
      
| Row | start_station_id | ride per dock |
|-----|-------------|------------------|
| 1 | 21 | 1 |
| 2 | 88 | 1 |
| 3 | 24 | 2 |
| 4 | 91 | 2 |
| 5 | 23 | 2 |
    
  * SQL query to B:  
  
  
```sql
With twoa as(SELECT start_station_id, start_station_name,
SUM(sum_trips) as sum_trips, ROUND(AVG(avg_dockcount)) as avg_dockcount, AVG(avg_bikes_available) as avg_bikes_available , 
AVG(avg_docks_available) as avg_docks_available 
FROM `spring2021w205.project1w205.Final_Proj1_R`
WHERE start_year=2016
GROUP BY start_station_id, start_station_name),
twoatwo as(
SELECT start_station_id, start_station_name, sum_trips, avg_dockcount,ROUND(sum_trips/avg_dockcount) as rides_per_dock, avg_bikes_available,
avg_docks_available
FROM twoa)
SELECT start_station_id, start_station_name, sum_trips, avg_dockcount, rides_per_dock ,ROUND(avg_bikes_available) as avg_bikes_available,
ROUND(avg_docks_available) as avg_docks_available
FROM twoatwo ORDER BY rides_per_dock asc LIMIT 5
```  


   * Answer to C:
    Ans 3c(i): 150 ride per dock on average across all stations in Year 2016
    Ans 3c(ii): 30 stations with above average rides per dock in Year 2016
    Ans 3c(iii): 44 stations with below average rides per dock in Year 2016

  * SQL query to C:
  
  3ci)
  ```sql
  SELECT ROUND(AVG(rides_per_dock)) FROM `spring2021w205.project1w205.part2_2_full`
  ``` 
  
  3cii)
  
  ```sql
  SELECT COUNT(*) FROM `spring2021w205.project1w205.part2_2_full`WHERE rides_per_dock>150 
  ```

  
  3ciii)
  ```sql 
  SELECT COUNT(*) FROM `spring2021w205.project1w205.part2_2_full`
  WHERE rides_per_dock<150
  ```

  
  
- Question 4: (A)What are the top 5 station pairs on weekends and (B) what is the average duration of a ride on a weekend? (C) How does that compare to the average duration of a ride on a weekday?
  
  * Answer to A:
  
| Row | start_station_id | end_station_id |
|-----|-------------|------------------|
| 1 | 50 | 60 |
| 2 | 60 | 50 |
| 3 | 60 | 60 |
| 4 | 50 | 50 |
| 5 | 23 | 60 |
  
  * SQL query to A:
  
```sql 
WITH Q1 as (SELECT start_station_id, start_station_name, 
end_station_id, end_station_name,
SUM(sum_trips) as sum_trips, AVG(avg_dockcount) as avg_dockcount 
FROM `spring2021w205.project1w205.Final_Proj1_R`
WHERE avghours_dec<24 and start_weekday_end="Weekend"
GROUP BY start_station_name,start_station_id, end_station_name, end_station_id)

SELECT start_station_id, start_station_name, 
end_station_id, end_station_name,sum_trips, avg_dockcount, ROUND(sum_trips/avg_dockcount) as rides_per_dock FROM Q1
ORDER BY sum_trips desc LIMIT 5
```
   
     
    *Answer to B: Average duration of a ride on a weekend is 0.5 hours
  
  * SQL query to B:
  
```sql
SELECT AVG(avghours_dec)
FROM `spring2021w205.project1w205.Final_Proj1_R`
WHERE avghours_dec<24 and start_weekday_end="Weekend" 
```

  
    *Answer to C:Average duration of a ride on a weekday is 0.3 hours.
  
  * SQL query to C:
```sql
SELECT AVG(avghours_dec)
FROM `spring2021w205.project1w205.Final_Proj1_R`
WHERE avghours_dec<24 and start_weekday_end="Weekday"
```


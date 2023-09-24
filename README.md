# Bellabeat-Capstone-Project
<img width="381" alt="Screen Shot 2022-07-01 at 7 13 03 PM" src="https://user-images.githubusercontent.com/108308205/176982981-5149a1b2-59c6-40fb-8403-cf757d4b0208.png">

Author: Golriz Shakeri

Date: 23 September 2023

This Case Study from Google Analytics Course provides me the opportunity to showcase my Technical Skills in SQL and Tableau.

## Introduction

Bellabeat is a high-tech manufacturer of beautifully-designed health-focused smart products for women since 2013. Inspiring and empowering women with knowledge about their own health and habits, Bellabeat has grown rapidly and quickly positioned itself as a tech-driven wellness company for females.

The Co-founder and Chief Creative Officer, Urška Sršen is confident that an analysis of non-Bellabeat consumer data (ie.FitBit fitness tracker usage data) would reveal more opportunities for growth.


## Business Task

Analyze FitBit Fitness Tracker App data to gain insights into how consumers are using the app and help guide Marketing Strategy for BellaBeat to grow as a Global organization in Tech industry.

## Stakeholders

Primary Stakeholders:
* Urška Sršen: Bellabeat's Cofounder and Chief Creative Officer.
* Sando Mur: Mathematician and another Bellabeat's Cofounder, key member of the Bellabeat executive team.

Secondary Stakeholders:
* Bellabeat Marketing Analytics Team:  A team of data anlysts responsible for collecting, analyzing, and reporting data that helps guide Bellabeat's marketing strategy.

## Prepare

* First, we have to understand how data is generated and collected in this phase of analyis: Our Data Source is [Kaggle](https://www.kaggle.com/datasets/arashnic/fitbit). Please refer to this [link](https://www.fitabase.com/media/1930/fitabasedatadictionary102320.pdf) for documentation of the dataset.
* Second, we need to make sure that data is unbiased and credibe: Our dataset contains 18 CSV files and data is from 30 FitBit users who consented to the submission of personal tracker data via Amazon Mechanical Turk. Although this dataset is reliable, original, and comprehensive, there are two main concenrs about it. Firstly, the sample size is small and most data is recorded during certain days of the week. Secondly, Data is outdated for 6 years as the study was conducted in 2016. Also, demographic information such as gender was not included, which is critical to this project as Bellabeat's product line caters for women. 

## Process

The tools that were used in the Process phase of this project are Google's BigQuery and Microsoft Excel. Pre-cleaning of the data was done through Microsoft Excel before the data were stored to BigQuery. 
- By going through all 18 CSV files, I decided to structure my analysis based on *DailyActivity*, *SleepDay*, *MinuteMETs*, *HourlyCalories*, *Hourlyintensities*, and *HourlySteps* tables.
- *Dailyintensities*, *DailyCalories*, *DailySteps*, *MinuteCalories*, *MinuteIntensities*, and *MinuteSteps* were not used in this project since all data points from these tables were already included in the *DailyActivity* and *Hourly* tables.
- Although *HearRate* and *WeightLogInfo* tables would have been useful for this anaysis, there were not utilized due to lack of participants. 
- While uploading the selected datasets to BigQuery, an error occured due to the format of datetime columns. 
-  To fix the *Formatting* issue, all the above Datasets converted into Microsoft Excel in order to create new datetime columns with *mm/dd/yyyy hh:mm AM/PM Formt*. 
- Loaded cleaned datasets into new sheets, saved as CSV files, and uploaded into Bigquery.

After uploading the cleaned Datasets to BigQuery, the following were done to ensure that the data is clean before proceeding to the *Analyze Phase*.

```sql
-- View and examine tables

-- 940 records from DailyActivity table
SELECT *
FROM `expanded-bebop-352104.fitbit.DailyActivity` AS DailyActivity


-- 413 records from SleepDay table
SELECT *
FROM `expanded-bebop-352104.fitbit.SleepDay` AS SleepDay

-- 22,099 records from HourlyCalories table
SELECT *
FROM `expanded-bebop-352104.fitbit.HourlyCalories` AS HourlyCalories

-- 22,099 records from HourlyIntensities table
SELECT *
FROM `expanded-bebop-352104.fitbit.HourlyIntensities` AS HourlyIntensities

-- 22,099 records from HourlySteps table
SELECT *
FROM `expanded-bebop-352104.fitbit.HourlySteps` AS HourlySteps

-- 1,325,580 records from MinuteMETs table
SELECT *
FROM `expanded-bebop-352104.fitbit.MinuteMETs` AS MinuteMETS

-- Number of Unique Participants

-- 33 unique participants in DailyActivity table
SELECT COUNT(DISTINCT id) AS UserCount
FROM `expanded-bebop-352104.fitbit.DailyActivity`

-- 33 unique participants in METs table
SELECT COUNT(DISTINCT id) AS UserCount
FROM `expanded-bebop-352104.fitbit.MinuteMETs` AS MinuteMETS

-- 24 unique participants in SleepDay table
SELECT COUNT(DISTINCT id) AS UserCount
FROM `expanded-bebop-352104.fitbit.SleepDay` AS SleepDay

-- 33 unique participants in HourlyCalories, HourlyIntensities, and HourlySteps
SELECT COUNT(DISTINCT id) AS UserCount
FROM `expanded-bebop-352104.fitbit.HourlyCalories` AS HourlyCalories

SELECT COUNT(DISTINCT id) AS UserCount
FROM `expanded-bebop-352104.fitbit.HourlyIntensities` AS HourlyIntensities

SELECT COUNT(DISTINCT id) AS UserCount
FROM `expanded-bebop-352104.fitbit.HourlySteps` AS HourlySteps

-- No duplicates found on DailyActivities table
SELECT Id, 
  ActivityDate, 
  TotalSteps, 
  TotalDistance, 
  TrackerDistance, 
  LoggedActivitiesDistance,	
  VeryActiveDistance,	
  ModeratelyActiveDistance,	
  LightActiveDistance,	
  SedentaryActiveDistance,	
  VeryActiveMinutes,	
  FairlyActiveMinutes,	
  LightlyActiveMinutes,	
  SedentaryMinutes,	
  Calories,
  COUNT(*) AS Dupes
FROM `expanded-bebop-352104.fitbit.DailyActivity` AS DailyActivity
GROUP BY Id, 
  ActivityDate, 
  TotalSteps, 
  TotalDistance, 
  TrackerDistance, 
  LoggedActivitiesDistance,	
  VeryActiveDistance,	
  ModeratelyActiveDistance,	
  LightActiveDistance,	
  SedentaryActiveDistance,	
  VeryActiveMinutes,	
  FairlyActiveMinutes,	
  LightlyActiveMinutes,	
  SedentaryMinutes,	
  Calories
HAVING Dupes > 1

-- Found 3 duplicates from SleepDay table, removed those duplicates.
SELECT
  Id,
  SleepDay,
  TotalSleepRecords,
  TotalMinutesAsleep,
  TotalTimeInBed,
  COUNT(*) AS Dupes
FROM `expanded-bebop-352104.fitbit.SleepDay` AS SleepDay1
GROUP BY Id,
  SleepDay,
  TotalSleepRecords,
  TotalMinutesAsleep,
  TotalTimeInBed
HAVING Dupes = 1
ORDER BY Id,
  SleepDay,
  TotalSleepRecords,
  TotalMinutesAsleep,
  TotalTimeInBed

-- Created user no table to clearly identify unique participant
-- Merged Daily Activity, Sleep Day, and User No tables.
-- Nulls have been set to 0.
SELECT 
  DailyActivity.Id,
  UserNumberTable.UserNo,
  ActivityDate, 
  SUM(TotalSteps) AS Total_Steps, 
  SUM(TotalDistance) AS Total_Distance, 
  SUM(TrackerDistance) AS Total_Tracker_Distance, 
  SUM(LoggedActivitiesDistance) AS Total_LoggedActivitiesDistance,	
  SUM(VeryActiveDistance) AS Total_VeryActiveDistance,	
  SUM(ModeratelyActiveDistance) AS Total_ModeratelyActiveDistance,	
  SUM(LightActiveDistance) AS Total_LightActiveDistance,	
  SUM(SedentaryActiveDistance) AS Total_SedentaryActiveDistance,	
  SUM(VeryActiveMinutes) AS Total_VeryActiveMinutes,
  SUM(FairlyActiveMinutes) AS Total_FairlyActiveMinutes,
  SUM(LightlyActiveMinutes) AS Total_LightlyActiveMinutes,
  SUM(SedentaryMinutes) AS Total_SedentaryMinutes,
  SUM(Calories) AS Total_Calories,
  IFNULL(SUM(TotalSleepRecords),0) AS Total_SleepRecords,
  IFNULL(SUM(TotalMinutesAsleep),0) AS Total_MinutesAsleep,
  IFNULL(SUM(TotalTimeInBed),0) AS Total_TimeInBed,
FROM `expanded-bebop-352104.fitbit.DailyActivity` AS DailyActivity
LEFT JOIN 
(SELECT
  Id,
  SleepDay,
  TotalSleepRecords,
  TotalMinutesAsleep,
  TotalTimeInBed,
  COUNT(*) AS Dupes
FROM `expanded-bebop-352104.fitbit.SleepDay` AS SleepDay1
GROUP BY Id,
  SleepDay,
  TotalSleepRecords,
  TotalMinutesAsleep,
  TotalTimeInBed
HAVING Dupes = 1
ORDER BY Id,
  SleepDay,
  TotalSleepRecords,
  TotalMinutesAsleep,
  TotalTimeInBed) AS SleepDay1
ON DailyActivity.Id = SleepDay1.Id
AND DailyActivity.ActivityDate = SleepDay1.SleepDay
LEFT JOIN `expanded-bebop-352104.fitbit.UserNumberTable` AS UserNumberTable
ON DailyActivity.Id = UserNumberTable.Id
GROUP BY DailyActivity.Id, UserNumberTable.UserNo, DailyActivity.ActivityDate
ORDER BY DailyActivity.Id, UserNumberTable.UserNo, DailyActivity.ActivityDate

--Hourly intensity data, extracted year, month, day, day name, hour
-- No duplicates found
SELECT HourlyIntensities.Id, HourlyIntensities.ActivityHour, SUM(TotalIntensity) AS Total_Intensity, AVG(AverageIntensity) AS Avg_Intensity, 
    EXTRACT(YEAR FROM HourlyIntensities.ActivityHour) AS Year,
    EXTRACT(MONTH FROM HourlyIntensities.ActivityHour) AS Month,
    EXTRACT(DAY FROM HourlyIntensities.ActivityHour) AS Day,
    EXTRACT(DAYOFWEEK FROM HourlyIntensities.ActivityHour) AS DayName,
    EXTRACT(Hour FROM HourlyIntensities.ActivityHour) AS Hour,
    COUNT(*) AS Duplicates
FROM `expanded-bebop-352104.fitbit.HourlyIntensities` AS HourlyIntensities
GROUP BY HourlyIntensities.Id, HourlyIntensities.ActivityHour
ORDER BY HourlyIntensities.Id, HourlyIntensities.ActivityHour

--Hourly calories data, extracted year, month, day, day name, hour
-- No duplicates found
SELECT Id, ActivityHour, SUM(Calories) AS Total_Calories,
  EXTRACT(YEAR FROM ActivityHour) AS Year,
  EXTRACT(MONTH FROM ActivityHour) AS Month,
  EXTRACT(DAY FROM ActivityHour) AS Day,
  EXTRACT(DAYOFWEEK FROM ActivityHour) AS DayName,
  EXTRACT(Hour FROM ActivityHour) AS Hour,
  COUNT(*) AS Duplicates
FROM `expanded-bebop-352104.fitbit.HourlyCalories` AS Hourly_Calories
GROUP BY Id, ActivityHour
ORDER BY Id, ActivityHour

--Hourly steps data, extracted year, month, day, day name, hour
-- No duplicates found
SELECT Id, ActivityHour, SUM(StepTotal) AS Total_Steps,
  EXTRACT(YEAR FROM ActivityHour) AS Year,
  EXTRACT(MONTH FROM ActivityHour) AS Month,
  EXTRACT(DAY FROM ActivityHour) AS Day,
  EXTRACT(DAYOFWEEK FROM ActivityHour) AS DayName,
  EXTRACT(Hour FROM ActivityHour) AS Hour,
  COUNT(*) AS Duplicates
FROM `expanded-bebop-352104.fitbit.HourlySteps` AS HourlySteps
GROUP BY Id, ActivityHour
ORDER BY Id, ActivityHour

--Merged 3 hourly tables into one
SELECT HourlyIntensities.Id, HourlyIntensities.ActivityHour, SUM(TotalIntensity) AS Total_Intensity, AVG(AverageIntensity) AS Avg_Intensity, 
  SUM(HourlyCalories.Total_Calories) AS TotalCalories,
  SUM(HourlySteps.Total_Steps) AS TotalSteps,
  EXTRACT(DATE FROM HourlyIntensities.ActivityHour) AS Date,
  EXTRACT(YEAR FROM HourlyIntensities.ActivityHour) AS Year,
  EXTRACT(MONTH FROM HourlyIntensities.ActivityHour) AS Month,
  EXTRACT(DAY FROM HourlyIntensities.ActivityHour) AS Day,
  FORMAT_DATE('%A', DATE(HourlyIntensities.ActivityHour)) AS DayName,
  CAST(EXTRACT(TIME FROM HourlyIntensities.ActivityHour) AS TIME) AS Time,
  EXTRACT(HOUR FROM HourlyIntensities.ActivityHour) AS Hour
FROM `expanded-bebop-352104.fitbit.HourlyIntensities` AS HourlyIntensities
LEFT JOIN 
  (SELECT Id, ActivityHour, SUM(Calories) AS Total_Calories,
  EXTRACT(YEAR FROM ActivityHour) AS Year,
  EXTRACT(MONTH FROM ActivityHour) AS Month,
  EXTRACT(DAY FROM ActivityHour) AS Day,
  EXTRACT(DAYOFWEEK FROM ActivityHour) AS DayName,
  EXTRACT(Hour FROM ActivityHour) AS Hour,
  FROM `gda-course-4-332812.Capstone.HourlyCalories` AS Hourly_Calories
  GROUP BY Id, ActivityHour) AS HourlyCalories
ON HourlyIntensities.Id = HourlyCalories.Id
AND HourlyIntensities.ActivityHour = HourlyCalories.ActivityHour
LEFT JOIN 
  (SELECT Id, ActivityHour, SUM(StepTotal) AS Total_Steps,
  EXTRACT(YEAR FROM ActivityHour) AS Year,
  EXTRACT(MONTH FROM ActivityHour) AS Month,
  EXTRACT(DAY FROM ActivityHour) AS Day,
  EXTRACT(DAY FROM ActivityHour) AS DayName,
  EXTRACT(Hour FROM ActivityHour) AS Hour,
  COUNT(*) AS Duplicates
  FROM `expanded-bebop-352104.fitbit.HourlySteps` AS Hourly_Steps
  GROUP BY Id, ActivityHour
  ORDER BY Id, ActivityHour) AS HourlySteps
ON HourlyIntensities.Id = HourlySteps.Id
AND HourlyIntensities.ActivityHour = HourlySteps.ActivityHour
GROUP BY HourlyIntensities.Id, HourlyIntensities.ActivityHour
ORDER BY HourlyIntensities.Id, HourlyIntensities.ActivityHour

--METs per minute, extracted year, month, day, day name, hour 
-- No duplicates found 
-- Aggregated into METs per day
WITH Minute_METs AS (SELECT Id, SUM(METs) AS METs,
EXTRACT(DATE FROM ActivityMinute) AS Date,
  EXTRACT(YEAR FROM ActivityMinute) AS Year,
  EXTRACT(MONTH FROM ActivityMinute) AS Month,
  EXTRACT(DAY FROM ActivityMinute) AS Day,
  FORMAT_DATE('%A', DATE(ActivityMinute)) AS DayName,
  CAST(EXTRACT(TIME FROM ActivityMinute) AS TIME) AS Time,
  EXTRACT(HOUR FROM ActivityMinute) AS Hour,
  COUNT(*) AS Duplicates
FROM `expanded-bebop-352104.fitbit.MinuteMETs` AS MinuteMETS
GROUP BY Id, ActivityMinute
ORDER BY Id, ActivityMinute)

SELECT Id, Date, SUM(METs) AS Total_METs, ROUND(AVG(METs), 2) AS Avg_METs,
  EXTRACT(YEAR FROM Date) AS Year,
  EXTRACT(MONTH FROM Date) AS Month,
  EXTRACT(DAY FROM Date) AS Day,
  FORMAT_DATE('%A', DATE(Date)) AS DayName
FROM Minute_METs
GROUP BY Id, Date
```

### Brief Summary:
- DailyActivity and SleepDay tables were combined into one table.
- Hourly tables (*HourlyIntensities, HourlyCalories, and HourlySteps*) were combined into one table.
- HourlyMETSs table was aggregated into daily METs.

## Analyze 
Analyzing the tables by creating summary statistics is the first task in this phase of analysis.

```sql
-- Analyze Phase. Created summary stats

WITH DailyActivity AS (
  SELECT 
    DailyActivity.Id,
    UserNumberTable.UserNo,
    ActivityDate, 
    SUM(TotalSteps) AS Total_Steps, 
    SUM(TotalDistance) AS Total_Distance, 
    SUM(TrackerDistance) AS Total_Tracker_Distance, 
    SUM(LoggedActivitiesDistance) AS Total_LoggedActivitiesDistance,	
    SUM(VeryActiveDistance) AS Total_VeryActiveDistance,	
    SUM(ModeratelyActiveDistance) AS Total_ModeratelyActiveDistance,	
    SUM(LightActiveDistance) AS Total_LightActiveDistance,	
    SUM(SedentaryActiveDistance) AS Total_SedentaryActiveDistance,	
    SUM(VeryActiveMinutes) AS Total_VeryActiveMinutes,
    SUM(FairlyActiveMinutes) AS Total_FairlyActiveMinutes,
    SUM(LightlyActiveMinutes) AS Total_LightlyActiveMinutes,
    SUM(SedentaryMinutes) AS Total_SedentaryMinutes,
    SUM(Calories) AS Total_Calories,
    IFNULL(SUM(TotalSleepRecords),0) AS Total_SleepRecords,
    IFNULL(SUM(TotalMinutesAsleep),0) AS Total_MinutesAsleep,
    IFNULL(SUM(TotalTimeInBed),0) AS Total_TimeInBed,
  FROM `expanded-bebop-352104.fitbit.DailyActivity` AS DailyActivity
  LEFT JOIN 
    (SELECT
      Id,
      SleepDay,
      TotalSleepRecords,
      TotalMinutesAsleep,
      TotalTimeInBed,
      COUNT(*) AS Dupes
    FROM `expanded-bebop-352104.fitbit.SleepDay` AS SleepDay1
    GROUP BY Id,
      SleepDay,
      TotalSleepRecords,
      TotalMinutesAsleep,
      TotalTimeInBed
    HAVING Dupes = 1
    ORDER BY Id,
      SleepDay,
      TotalSleepRecords,
      TotalMinutesAsleep,
      TotalTimeInBed) AS SleepDay1
  ON DailyActivity.Id = SleepDay1.Id
  AND DailyActivity.ActivityDate = SleepDay1.SleepDay
  LEFT JOIN `expanded-bebop-352104.fitbit.UserNumberTable` AS UserNumberTable
  ON DailyActivity.Id = UserNumberTable.Id
  GROUP BY DailyActivity.Id, UserNumberTable.UserNo, DailyActivity.ActivityDate
  ORDER BY DailyActivity.Id, UserNumberTable.UserNo, DailyActivity.ActivityDate
)
SELECT 
  AVG(Total_Steps) AS AvgSteps,
  AVG(Total_Distance) AS AvgDistance,
  AVG(Total_VeryActiveMinutes) AS AvgVeryActiveMins,
  SUM(Total_VeryActiveMinutes) / SUM(Total_VeryActiveMinutes + Total_FairlyActiveMinutes + Total_LightlyActiveMinutes + Total_SedentaryMinutes) AS VeryActivePerc,
  AVG(Total_FairlyActiveMinutes) AS AvgFairlyActiveMins,
  SUM(Total_FairlyActiveMinutes) / SUM(Total_VeryActiveMinutes + Total_FairlyActiveMinutes + Total_LightlyActiveMinutes + Total_SedentaryMinutes) AS FairlyPerc,
  AVG(Total_LightlyActiveMinutes) AS AvgLightlyActiveMins,
  SUM(Total_LightlyActiveMinutes) / SUM(Total_VeryActiveMinutes + Total_FairlyActiveMinutes + Total_LightlyActiveMinutes + Total_SedentaryMinutes) AS LightlyActivePerc,
  AVG(Total_SedentaryMinutes) AS AvgSedentaryMins,
  SUM(Total_SedentaryMinutes) / SUM(Total_VeryActiveMinutes + Total_FairlyActiveMinutes + Total_LightlyActiveMinutes + Total_SedentaryMinutes) AS SedentaryPerc,
  AVG(Total_Calories) AS AvgCalories,
  SUM(Total_MinutesAsleep) / SUM(CASE WHEN Total_MinutesAsleep > 0 THEN 1 END) AS AvgMinsAsleep,
  SUM(Total_TimeInBed) / SUM(CASE WHEN Total_TimeInBed > 0 THEN 1 END) AS AvgBedTime
FROM DailyActivity
```

### Critical Insights based on the *Analyze Phase* :
- According to [NBC news](https://www.nbcnews.com/health/health-news/how-many-steps-day-should-you-take-study-finds-7-n1278853), the fitness goal of 10,000 steps a day is widely promoted, but a new study suggests that logging even 7,000 daily steps may go a long way toward better health. People who walked at least 7,000 steps a day on average were 50 percent to 70 percent less likely to die of any cause over the next decade, compared with those who took fewer steps. As determined by the above analysis, the average daily steps for our 33 participants is **7,637 steps per day**.  
- According to [Sleep Foundation](https://www.sleepfoundation.org/women-sleep/do-women-need-more-sleep-than-men), the average adult needs between 7 to 9 hours of sleep per night to feel refreshed. However, research suggests that women tend to sleep just a little bit longer — 11 minutes, to be exact — than men. Based on the outcome of our analysis, the average sleep time for all participants in this reasearch is **418 minutes (7 hours) per day** and the average time spent in bed is **458 minutes (7 Hours and 36 minutes)**. As it follows, it took around 40 minutes for our participants to fall asleep which is an indicator of unsatisfactory sleep based on recent studies from [Sleep Foundation](https://www.sleepfoundation.org/sleep-hygiene/how-is-sleep-quality-calculated#:~:text=For%20quality%20sleep%2C%20it%20should,minutes%20to%20fall%20asleep%20again.).
- The average Daily Distance covered by all the particiapnts is **5.49 Kilometers** which is shorter than the recommended distance of **8 Kilometers** by [Medical News Today](  https://www.medicalnewstoday.com/articles/how-many-steps-should-you-take-a-day#:~:text=Walking%20is%20a%20form%20of,8%20kilometers%2C%20or%205%20miles.).

The second task of our **Analyze Phase* is to find trends and how strong a relationship is between two variables. The results returned values between -1 and 1:
- 1 indicates a strong postive relationship.
- 0 indicates no relationship.
- -1 indicates a strong negative relationship.
```sql
-- Correlations
WITH DailyActivity AS (
  SELECT 
    DailyActivity.Id,
    UserNumberTable.UserNo,
    ActivityDate, 
    SUM(TotalSteps) AS Total_Steps, 
    SUM(TotalDistance) AS Total_Distance, 
    SUM(TrackerDistance) AS Total_Tracker_Distance, 
    SUM(LoggedActivitiesDistance) AS Total_LoggedActivitiesDistance,	
    SUM(VeryActiveDistance) AS Total_VeryActiveDistance,	
    SUM(ModeratelyActiveDistance) AS Total_ModeratelyActiveDistance,	
    SUM(LightActiveDistance) AS Total_LightActiveDistance,	
    SUM(SedentaryActiveDistance) AS Total_SedentaryActiveDistance,	
    SUM(VeryActiveMinutes) AS Total_VeryActiveMinutes,
    SUM(FairlyActiveMinutes) AS Total_FairlyActiveMinutes,
    SUM(LightlyActiveMinutes) AS Total_LightlyActiveMinutes,
    SUM(SedentaryMinutes) AS Total_SedentaryMinutes,
    SUM(Calories) AS Total_Calories,
    IFNULL(SUM(TotalSleepRecords),0) AS Total_SleepRecords,
    IFNULL(SUM(TotalMinutesAsleep),0) AS Total_MinutesAsleep,
    IFNULL(SUM(TotalTimeInBed),0) AS Total_TimeInBed,
  FROM `expanded-bebop-352104.fitbit.DailyActivity` AS DailyActivity
  LEFT JOIN 
    (SELECT
      Id,
      SleepDay,
      TotalSleepRecords,
      TotalMinutesAsleep,
      TotalTimeInBed,
      COUNT(*) AS Dupes
    FROM `expanded-bebop-352104.fitbit.SleepDay` AS SleepDay1
    GROUP BY Id,
      SleepDay,
      TotalSleepRecords,
      TotalMinutesAsleep,
      TotalTimeInBed
    HAVING Dupes = 1
    ORDER BY Id,
      SleepDay,
      TotalSleepRecords,
      TotalMinutesAsleep,
      TotalTimeInBed) AS SleepDay1
  ON DailyActivity.Id = SleepDay1.Id
  AND DailyActivity.ActivityDate = SleepDay1.SleepDay
  LEFT JOIN `expanded-bebop-352104.fitbit.UserNumberTable` AS UserNumberTable
  ON DailyActivity.Id = UserNumberTable.Id
  GROUP BY DailyActivity.Id, UserNumberTable.UserNo, DailyActivity.ActivityDate
  ORDER BY DailyActivity.Id, UserNumberTable.UserNo, DailyActivity.ActivityDate
)
SELECT CORR(Total_Calories, Total_Steps) AS CorrCaloriesSteps,
  CORR(Total_Calories, Total_Distance) AS CorrCaloriesDistance,
  CORR(Total_Calories, Total_VeryActiveMinutes) AS CorrCaloriesVeryActive,
  CORR(Total_Calories, Total_FairlyActiveMinutes) AS CorrCaloriesFairlyActive,
  CORR(Total_Calories, Total_LightlyActiveMinutes) AS CorrCaloriesLightlyActive,
  CORR(Total_Calories, Total_SedentaryMinutes) AS CorrCaloriesSedentary
FROM DailyActivity
-- high positive correlation between daily calories and steps
-- high positive correlation between daily calories and distance
-- high positive correlation between daily calories and very active minutes
-- low positive correlation between daily calories and fairly active minutes
-- low positive correlation between daily calories and lightly active minutes
-- low negative correlation between daily calories and sedentary minutes

SELECT CORR(Calories, Total_METs) AS CorrCaloriesMETs
FROM (WITH Minute_METs AS (SELECT Id, SUM(METs) AS METs,
EXTRACT(DATE FROM ActivityMinute) AS Date,
  EXTRACT(YEAR FROM ActivityMinute) AS Year,
  EXTRACT(MONTH FROM ActivityMinute) AS Month,
  EXTRACT(DAY FROM ActivityMinute) AS Day,
  FORMAT_DATE('%A', DATE(ActivityMinute)) AS DayName,
  CAST(EXTRACT(TIME FROM ActivityMinute) AS TIME) AS Time,
  EXTRACT(HOUR FROM ActivityMinute) AS Hour,
  COUNT(*) AS Duplicates
FROM `expanded-bebop-352104.fitbit.MinuteMETs` AS MinuteMETS
GROUP BY Id, ActivityMinute
ORDER BY Id, ActivityMinute)

SELECT Id, Date, SUM(METs) AS Total_METs, ROUND(AVG(METs), 2) AS Avg_METs,
  EXTRACT(YEAR FROM Date) AS Year,
  EXTRACT(MONTH FROM Date) AS Month,
  EXTRACT(DAY FROM Date) AS Day,
  FORMAT_DATE('%A', DATE(Date)) AS DayName
FROM Minute_METs
GROUP BY Id, Date
) AS METS
LEFT JOIN `expanded-bebop-352104.fitbit.DailyActivity` AS DailyActivity
ON METS.Id = DailyActivity.Id
AND METS.Date = DailyActivity.ActivityDate
-- High positive correlation between calories and METs
```

### Critical insights based on **Correlation Analyses** :
- At 0.71, there is a high positive correlation between calories and distance.
- At 0.64, there is a moderately high positive correlation between calories and distance.
- At 0.62, there is a moderately high positive correlation between calories and very active minutes.
- At 0.59, there is a moderately high positive correlation between calories and steps.
- At 0.30, there is a low positive correlation between calories and fairly active minutes.
- At 0.29, there is a negligible positive correlation between calories and lightly active minutes.
- At -0.11, there is a negligible negative correlation between calories and lightly active minutes.


## Share 
In this phase of Analysis, Tableau Public was used to visualize the analysis made on this study.

### Correlations

![Correlations](https://user-images.githubusercontent.com/108308205/177237376-3abd0cf2-0a48-41c6-b7ac-b66a361d4b00.png)

- **Calories vs. Distance** : This figure shows a moderately high positive correlation between calories and distance, which means the higher the distance tracked, the higher the calories burnt.
- **Calories vs. Steps** : This figure shows a moderately high positive correlation between calories and steps, which means the higher the steps taken, the higher the calories burnt.
- **Calories vs. METs** : A MET is a ratio of your working metabolic rate relative to your resting metabolic rate. Metabolic rate is the rate of energy expended per unit of time. It’s one way to describe the intensity of an exercise or activity. This figure depicts an upward trajectory on its trendline, indicating that the total METs is highly correlated with calories burned.
- **Calories vs. Very Active Minutes** : It shows that the higher the time allocated in doing vigorous activities and exercises you do, more calories will be slashed.

### Hourly 

![Hourly](https://user-images.githubusercontent.com/108308205/177237694-31a79c77-9f9c-4d02-ae73-e56b216d77f4.png)

- **Average Calories by Hour** : On average, users burn most of their calories from 5PM-7PM.
- **Average Intensity by Hour** : Users recorded their highest intensity levels from 5-7 PM.
- **Average Steps by Hour** : Users walk the most by 5PM-7PM.

### Daily 

![Daily](https://user-images.githubusercontent.com/108308205/177237874-04df95e2-d879-42c5-a75c-20efe87985aa.png)

- **Average Calories by Day** : As we can see in this figure, users on average burn the most calories on a Tuesday and lowest on a Thursday.
- **Average METs by Day** : This figure shows that users have highest METs on a Tuesday and lowest on a Thursday.
- **Average Very Active Minutes by Day** : Users do most of their very active activities on a Tuesday and the lowest on a Thursday.
- **Average Steps by Day** : Users walk the most on a Tuesday and lowest on a Friday.

## Recommendations
- The dataset of this study is already 6 years old, which means Bellabeat will need to make a much updated and comprehensive version of this dataset. Key points for improvement would be an increase in the number of participants and capture of demographic data such as gender, age, location, height, weight, and so on.
- Heart Rate and Weight Log Info tables would’ve been useful in this study had there been a large number of participants with records on these tables and is also an integral part of tracking one’s fitness. One way to improve capture of these data points is better integration of heart rate and weight into Bellabeat’s notification system.
- We saw from the insights of this study that calories burned are positively correlated into steps, distance, METs, and very active minutes. Bellabeat can use these insights to market its product line showing how these variables impact calories burnt and the relevant studies that back it. Bellabeat can also use these insights to create a predictive analytics feature into the product, showing users forecasted calories burnt in accordance with the variables mentioned. This can help motivate users to stay active and achieve their fitness goals, as one insight in this study showed that 81% of the users’ day are spent in a sedentary state.
- On average, users take 40 minutes to fall asleep when they go to bed, which is higher than most studies recommend. Bellabeat should also integrate this into the notification system in order for the users to be reminded to get sufficient sleep.
- As the majority of the users spend most of their day in a sedentary state, Bellabeat can notify the user to do some light to moderate activity 1 hour after being in a sedentary state.
- Users are mostly active from 5-7 PM. In order to ensure that users maintain a healthy lifestyle, Bellabeat can fire up a notification during this time to remind the user to do moderate to intense activities such as walking, running, cycling, etc.
- Bellabeat should create a reward system in order to encourage users to stay active and motivated, rewarding the users on their milestones and the ability to share these on social media.

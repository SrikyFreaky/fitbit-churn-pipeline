# Data Preparation

## Pipeline

All data engineering was performed in Microsoft Fabric Lakehouse using PySpark notebooks.

## Ingestion

18 CSV files uploaded to OneLake Lakehouse (raw_fitbit folder):

| File | Description |
|------|-------------|
| dailyActivity_merged.csv | Steps, distance, calories, active minutes |
| sleepDay_merged.csv | Sleep duration, time in bed, sleep records |
| hourlySteps_merged.csv | Steps per hour |
| hourlyCalories_merged.csv | Calories per hour |
| hourlyIntensities_merged.csv | Intensity levels per hour |
| heartrate_seconds_merged.csv | Second-level heart rate |
| minuteSteps*.csv | Minute-level step counts |
| minuteCalories*.csv | Minute-level calorie burn |
| minuteIntensities*.csv | Minute-level intensity |
| minuteSleep_merged.csv | Minute-level sleep state |
| minuteMETs*.csv | Minute-level metabolic equivalents |
| weightLogInfo_merged.csv | Weight and BMI logs |
| dailyCalories_merged.csv | Daily calorie totals |
| dailyIntensities_merged.csv | Daily intensity breakdown |
| dailySteps_merged.csv | Daily step totals |

## Profiling

- Schema validated with Spark's inferSchema
- 33 distinct users identified
- 940 daily activity rows, 413 sleep rows
- Activity and calorie data nearly complete
- Sleep and heart rate had expected missingness from inconsistent device usage

## Cleaning Steps

**1. Standardized date fields**
All datetime columns parsed and converted using to_date() and from_unixtime().

**2. Removed duplicates and invalid values**
Duplicate records removed on (Id, Date). Negative steps, calories, and sleep durations filtered out.

**3. Aggregated granular data to daily summaries**
- Steps and calories: summed
- Intensity: averaged
- Heart rate: min, max, mean, standard deviation

**4. Joined all tables**
Base table: dailyActivity_merged.csv. All other daily-grain datasets joined on (Id, Date) using left joins. Hourly and minute files merged only after aggregation.

**5. Created engagement label**
Binary column: Engaged = 1 if TotalSteps > 0, else 0. Result: 933 active user-days, 7 inactive days.

## Missing Data Handling

- Sleep metrics: user-wise mean imputation
- Heart rate gaps: flagged with HR_missing_flag column
- Weight data: excluded due to sparsity
- Outliers retained for behavior trend analysis

## Output

Unified daily-level master table (daily_activity_cleaned) stored as Delta table in the Lakehouse, ready for analytics and modeling.

# Fitbit Churn Prediction Pipeline

End-to-end data pipeline for predicting and preventing Fitbit user churn. Built with Microsoft Fabric, PySpark, Delta Lake, and Power BI from raw CSV ingestion to ML-driven activity nudges.

---

## Problem

Fitbit Premium has roughly 7% monthly churn. Users disengage gradually fewer workouts, irregular sleep tracking, skipped logins weeks before they actually cancel. Current systems only react to outright inactivity and miss these subtle signals.

This project builds a two-layer solution:
1. A **predictive model** that identifies users likely to churn using activity and engagement patterns
2. An **adaptive nudging system** that delivers personalized interventions to re-engage at-risk users

Even a 5–10% churn reduction translates to millions in retained recurring revenue.

---

## Architecture

```
Raw CSVs (18 files, Kaggle)
    │
    ▼
Microsoft Fabric Lakehouse (OneLake)
    │
    ▼
PySpark Notebooks — Cleaning, Aggregation, Feature Engineering
    │
    ▼
Delta Lake Tables (ACID-governed storage)
    │
    ├──▶ KMeans Clustering (5 activity states, silhouette 0.648)
    ├──▶ Random Forest Prediction (accuracy 0.836, F1 0.762)
    └──▶ Power BI Dashboards (activity trends, churn risk)
```

---

## Dataset

**Source:** [Fitbit Fitness Tracker Dataset (Kaggle)](https://www.kaggle.com/datasets/arashnic/fitbit)

- 18 merged CSV files (activity, sleep, calories, heart rate, intensity)
- 33 users over a 31-day period
- ~940 daily activity records, ~413 sleep records

---

## Pipeline

### 1. Data Engineering (`notebooks/01_data_engineering.ipynb`)

- Ingested 18 raw CSV files into Microsoft Fabric Lakehouse
- Standardized date fields, removed duplicates, filtered invalid records
- Aggregated minute-level and hourly data into daily summaries
- Joined all tables on (Id, Date) into a unified master table
- Created engagement labels and sleep efficiency features
- Saved cleaned output as Delta Lake tables with ACID governance

### 2. Analytics & Visualization (`notebooks/02_analytics_visualization.ipynb`)

- Converted raw data through CSV → Parquet → Delta format
- Demonstrated Delta Lake governance (delete + DESCRIBE HISTORY)
- Built daily and weekly aggregation views using Spark SQL
- Created 6 visualizations with matplotlib:
  - Distribution of daily steps (histogram)
  - Daily steps by day of week (boxplot)
  - Average steps by hour of day (line)
  - Calories vs active minutes (scatter + trend)
  - Share of step bins by week (stacked bars)
  - Top 10 users by adherence rate (bar)
- Derived actionable insights: nudge timing windows, weekend engagement gaps, adaptive goal thresholds

### 3. Clustering & Prediction (`notebooks/03_clustering_prediction.ipynb`)

- Built a minute-level master table from two OneLake data sources
- Normalized timestamps and engineered temporal features
- Trained **KMeans clustering** to identify 5 distinct activity states
  - Silhouette score: **0.648**
  - States: sedentary, light, moderate, active, high-intensity
- Trained **Random Forest classifier** for next-hour activity prediction
  - Accuracy: **0.836**
  - F1 score: **0.762**
- Tracked all experiments with **MLflow**
- Saved results as Delta tables for downstream consumption

---

## Key Results

| Metric | Value |
|--------|-------|
| Clustering silhouette score | 0.648 |
| Activity prediction accuracy | 0.836 |
| Activity prediction F1 | 0.762 |
| Activity states identified | 5 |
| Projected churn reduction | 5–10% |

---

## Tech Stack

| Layer | Tools |
|-------|-------|
| Storage | Microsoft Fabric Lakehouse, OneLake, Delta Lake |
| Processing | PySpark, Spark SQL, Apache Spark |
| ML | KMeans, Random Forest, XGBoost, FLAML AutoML, MLflow |
| Visualization | Power BI, matplotlib, seaborn |
| Languages | Python, SQL |
| Governance | Delta Lake ACID transactions, DESCRIBE HISTORY |

---

## Project Structure

```
fitbit-churn-pipeline/
├── README.md
├── notebooks/
│   ├── 01_data_engineering.ipynb        # Raw ingestion, cleaning, feature engineering
│   ├── 02_analytics_visualization.ipynb # Delta Lake, Spark SQL views, charts
│   └── 03_clustering_prediction.ipynb   # KMeans, Random Forest, MLflow
├── docs/
│   ├── problem_statement.md             # Business case and analytical questions
│   └── data_preparation.md             # Data sources, cleaning steps, schema
└── results/
    └── screenshots/                     # Power BI dashboards, visualizations
```

---

## How to Reproduce

1. Download the [Fitbit dataset from Kaggle](https://www.kaggle.com/datasets/arashnic/fitbit)
2. Create a Microsoft Fabric workspace and Lakehouse
3. Upload the 18 CSV files to the `raw_fitbit` folder in your Lakehouse
4. Attach the notebooks to the Lakehouse and run them in order:
   - `01_data_engineering.ipynb` -> builds the master table
   - `02_analytics_visualization.ipynb` -> creates views and charts
   - `03_clustering_prediction.ipynb` -> trains models and saves results

---

## Recommendations

Based on the analysis, the pipeline delivers these actionable strategies:

- **Time-window nudges** at low-activity hours (30–60 min before detected troughs)
- **Weekend micro-challenges** to stabilize Sat/Sun engagement variability
- **Adaptive step targets** (baseline +5–10%) instead of rigid 10K goals
- **Micro-workout prompts** triggered when afternoon activity trends below baseline
- **Early retention flags** when weekly step-bin distribution shifts downward
- **Segmented messaging** recognition for high-adherence users, coaching for the tail

---


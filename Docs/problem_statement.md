# Problem Statement

## The Challenge

Fitbit Premium has roughly 7% monthly churn. Users disengage gradually  fewer workouts, irregular sleep tracking, skipped logins  weeks before they actually cancel. Current systems only react to outright inactivity and miss these subtle signals.

## Objective

Build a two-layer solution:

1. A predictive model that identifies users likely to churn within 30 days using activity and engagement data
2. An adaptive nudging system that delivers personalized interventions (micro-challenges, motivational messages, contextual health insights) to re-engage at-risk users

## Analytical Questions

- Can we predict churn risk using 60-day engagement and biometric patterns?
- What nudging strategies work best for different user profiles?
- How do external factors (seasonal changes, goal fatigue) interact with biometric data to influence engagement?

## Business Value

Even a 5-10% churn reduction translates to millions in retained recurring revenue and repositions Fitbit from a passive tracker to a proactive health partner.

## Data Source

Fitbit Fitness Tracker Dataset from Kaggle 18 CSV files covering activity, sleep, calories, heart rate, and intensity data from 33 users over 31 days.

Dataset link: https://www.kaggle.com/datasets/arashnic/fitbit

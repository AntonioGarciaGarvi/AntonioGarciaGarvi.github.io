---
title: "Ride Demand Predictor"
excerpt: "End to end ML Pipeline for Ride demand prediction in NYC 🚕<br/><img src='/images/portfolio/taxi_demand_prediction/end2endTaxiDemandPred.png' style='width: 50%; height: auto;'>"
collection: portfolio
---

## Project Overview
[![GitHub](/images/github-mark-white.png) **GitHub Repository**](https://github.com/your/repo)

This project involves building a complete Machine Learning service to solve a real-world prediction problem for a ride-sharing company. Unlike standard academic projects that stop at model training, this project focuses on building a complete ML service that is operationally viable.

## The Business Problem
Ride-sharing companies (like Uber) need to maximize revenue by ensuring their fleet is distributed efficiently.

* **The Challenge:** Drivers are often not located where user demand is highest, leading to lost revenue and inefficiency.
* **The Goal:** Predict the demand (number of users looking for a ride) for the next hour in specific locations.
* **Operational Impact:** By accurately predicting demand, the operations team can incentivize drivers (e.g., via slight price increases or bonuses) to move to high-demand areas, balancing supply and demand.

## System Architecture (MLOps)
![ML Pipeline Overview](/images/portfolio/taxi_demand_prediction/MLsystem.png)
The project is architected as a modular MLOps system rather than a monolithic script. It utilizes a **Feature Store** to decouple feature engineering from model training and inference.

The system consists of three distinct pipelines:

1. **Feature Pipeline:** Ingests raw data, validates it, processes it into time-series features, and saves them to the Feature Store.
2. **Training Pipeline:** Fetches historical data from the Feature Store, trains the model, and saves the model artifact to the Model Registry.
3. **Inference Pipeline:** Runs periodically to fetch the most recent data, loads the trained model, and generates predictions for the upcoming hour.

## Technical Implementation

### 1. Data Preparation & Feature Engineering
The raw data is transformed from transactional records into a tabular format suitable for Supervised Machine Learning.

* **Time-Series Transformation:** Raw data is aggregated into hourly buckets.
* **Sliding Window Approach:** A slice-and-slide method is used to generate features and targets.
* **Feature Extraction:**
  * **Lag Features:** Rides from 1 hour ago and 24 hours ago.
  * **Window Features:** Average rides from the last 4 weeks (7, 14, 21, 28 days).
  * **Cyclical Features:** Hour and day-of-week extracted to capture temporal patterns.
  * **Categorical Encoding:** Location IDs are encoded to capture spatial variance.

### 2. Model Training
* **Algorithm:** **LightGBM Regressor** was selected for its performance on tabular data.
* **Validation:** A strict chronological split (cutoff at `2022-06-01`) was used to prevent data leakage between training and test sets.
* **Metric:** Performance is evaluated using Mean Absolute Error (MAE).
* **Hyperparameter Tuning:** Bayesian search was performed using **Optuna** to optimize parameters such as `num_leaves`, `bagging_fraction`, and `learning_rate`.

### 3. Operationalization & Monitoring
The project includes a comprehensive monitoring layer to ensure the model remains accurate over time.

* **Dashboard:** A **Streamlit** frontend visualizes the predictions on a map and displays monitoring metrics.
* **Drift Detection:** The system monitors the MAE hour-by-hour. If the error increases significantly (concept drift), it signals that the model needs to be re-trained on recent data.
* **Automation:** **GitHub Actions** are used to schedule the pipelines, ensuring predictions are generated every hour without manual intervention.

## Tech Stack
* **Language:** Python
* **ML Libraries:** Scikit-learn, LightGBM, Optuna
* **MLOps Tools:** Hopsworks (Feature Store), GitHub Actions
* **Visualization:** Streamlit
* **Dependency Management:** Poetry
 

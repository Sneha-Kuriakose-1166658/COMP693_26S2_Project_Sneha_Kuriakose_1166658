# COMP693 Project 


# Air Quality Risk Predictor

Machine learning pipeline that predicts air quality risk levels using weather data (Open-Meteo) and air pollution data (OpenAQ).

# Primary focus:
 New Zealand cities
# Secondary case study: 
Delhi, India

# Week 3 — Data Collection

1. **`fetch_weather.py`** pulls ~9-12 months of hourly weather (temperature,
   humidity, pressure, wind, precipitation) from Open-Meteo's free archive API.
2. **`fetch_air_quality.py`** finds OpenAQ monitoring stations near the city
   coordinates and pulls hourly PM2.5 / PM10 / NO2 readings for the same window.

   ## What's inside

Air_Quality_Predictor/
├── config.py              # Central settings 
├── requirements.txt       # Python package dependencies
├── .gitignore              
├── src/
│   ├── utils.py             # Shared helpers 
│   ├── fetch_weather.py     # Pulls historical weather from Open-Meteo
│   └── fetch_air_quality.py # Pulls historical pollution data from OpenAQ
└── data/
    └── raw/
        ├── auckland/         # Auckland weather + air quality data
        └── delhi/            # Delhi weather + air quality data



# Week 4 — AQI Labeling Logic

Converts raw pollutant concentrations (PM2.5,PM10,NO2) into a standard  EPA Air Quality Index (AQI) value and risk category (Good / Moderate / Unhealthy for Sensitive Groups / Unhealthy / Very Unhealthy / Hazardous).

Each pollutant is scored against its own EPA breakpoint table using linearinterpolation. The overall AQI is the maximum of the individual pollutant scores, matching how real-world AQI is reported. Validated against four official EPA reference points before being applied to real data


# Week 5 - Merge & Clean Pipeline

##steps

Took the separate weather and pollution datasets and turned them into one clean, usable dataset, ready for modelling.

1.Merged : lined up weather and pollution readings onto the same hourly timeline (they didn't originally match up, since pollution readings landed at a different point in the hour than weather readings)
2.Cleaned : filled small gaps, capped extreme outlier readings instead of deleting them, removed clearly broken sensor values
3.Labeled : assigned a risk category (Good → Hazardous) to every row, using the AQI logic built the week before
4.Tested : ran the full process end-to-end for two very different cities, to check it works consistently


##Results
Auckland: consistently "Good" air quality, with no variation across the whole dataset

Delhi: a wide, varying range of risk levels — from Good all the way to Hazardous




## Week 6 - Feature Wngineering

#What Was Done This Week

Gave the model more to actually learn from — up to this point, each row only had raw weather and pollution readings for a single hour, with no sense of context.

--Time context : hour of day (encoded so late night and early morning are treated as close together, not opposite extremes), day of week, season, and a weekend flag — since pollution patterns differ a lot depending on the time of day.
--Rolling averages: what pollution and weather looked like over the last 24 hours, not just the current instant.
--Lag features : what the reading was exactly 24 hours earlier, since pollution levels are often similar to the same time the day before.
--Tested : ran feature_engineering.py end-to-end for both cities via demo_week4.py, which prints a before/after comparison to confirm it works correctly.


# Results

# Delhi

=== BEFORE: merged_clean.csv (week 5's output) ===
Rows: 5161  |  Columns: 13
Column names: ['datetime', 'temperature_2m', 'relative_humidity_2m', 'surface_pressure', 'wind_speed_10m', 'wind_direction_10m', 'precipitation', 'pm25', 'pm10', 'no2', 'source', 'aqi', 'risk_category']

=== RUNNING feature_engineering.py ===
Built 69 columns x 4640 rows -> 

=== AFTER: features.csv ===
Rows: 4640  |  Columns: 69

56 new engineered feature columns added

-- features.csv saved to data/processed/delhi/

# Auckland

=== BEFORE: merged_clean.csv (week 5's output) ===
Rows: 4083  |  Columns: 11
Column names: ['datetime', 'temperature_2m', 'relative_humidity_2m', 'surface_pressure', 'wind_speed_10m', 'wind_direction_10m', 'precipitation', 'pm25', 'source', 'aqi', 'risk_category']

=== RUNNING feature_engineering.py ===
Built 57 columns x 4042 rows -> 

=== AFTER: features.csv ===
Rows: 4042  |  Columns: 57

46 new engineered feature columns added

-- features.csv saved to data/processed/auckland/



## Week 7 - Trained models for both cities (Delhi and Auckland)

#  TRAINING FOR: Delhi
###
Train: 4035 rows (2025-08-15 17:00:00 to 2026-05-31 23:00:00)
Test:  605 rows (2026-06-01 00:00:00 to 2026-06-28 02:00:00)
--- Class balance: TRAIN ---
risk_category
Unhealthy                       0.425
Unhealthy (Sensitive Groups)    0.221
Moderate                        0.193
Very Unhealthy                  0.108
Hazardous                       0.045
Good                            0.009
Name: proportion, dtype: float64
--- Class balance: TEST ---
risk_category
Moderate                        0.478
Unhealthy (Sensitive Groups)    0.377
Unhealthy                       0.129
Hazardous                       0.010
Good                            0.003
Very Unhealthy                  0.003
Name: proportion, dtype: float64
Using 59 features.
Training LogisticRegression ...
  LogisticRegression: F1(macro)=0.177  Precision(macro)=0.210  Recall(macro)=0.200
Training RandomForest ...
  RandomForest: F1(macro)=0.154  Precision(macro)=0.165  Recall(macro)=0.170
Training XGBoost ...
  XGBoost: F1(macro)=0.195  Precision(macro)=0.195  Recall(macro)=0.198
>>> Best model by macro F1: XGBoost (F1=0.195)
=== Model Comparison Table ===
Model               F1 (macro)  Precision   Recall      
LogisticRegression  0.177       0.210       0.200       
RandomForest        0.154       0.165       0.170       
XGBoost             0.195       0.195       0.198        <-- BEST

#  TRAINING FOR: Auckland
###
Train: 3846 rows (2025-08-01 17:00:00 to 2026-05-31 23:00:00)
Test:  196 rows (2026-06-01 00:00:00 to 2026-07-26 22:00:00)
--- Class balance: TRAIN ---
risk_category
Good    1.0
Name: proportion, dtype: float64
--- Class balance: TEST ---
risk_category
Good    1.0
Name: proportion, dtype: float64
======================================================================
NOTE: Auckland's entire dataset contains only ONE risk category (['Good']) across the whole collection window.
Classification models require at least 2 classes to train meaningfully -- there's nothing to distinguish, so model training is skipped for this city rather than fit a model that trivially always predicts the same class.
This is itself a real, reportable finding: Auckland's air quality stayed within a single risk band for the entire period, in contrast to a high-variance city like Delhi. It's direct evidence for why a two-city comparison is worthwhile.
======================================================================
Summary: Delhi produced a real, comparable 3-model result (saved to models/delhi/). Auckland's single-category dataset was correctly identified and training was skipped with an explanation rather than forcing a meaningless or crashing result.

--added Explainability : added a feature-importance report showing which features actually drive the trained model's predictions, instead of treating it as a black box.

-- added Open-Meteo's Air Quality API as a second, model-based source that automatically fills in for any city with no nearby OpenAQ ground station

--AQI cross-check : validated the project's own AQI calculation against this independent third-party source as a sanity check, rather than just trusting it.



## Week 8 -Built streamlit app 

Built the actual app this week  with five tabs ,where you can see past predictions, get a live forecast, see why the model predicted what it did, compare Delhi and Auckland, and compare this project to MetService's pollen forecasting. Checked  options for live pollen data and found couldn't get for chosen cities, so built a comparison instead of a fake live one. Tested the whole app and fixed the bugs I found.
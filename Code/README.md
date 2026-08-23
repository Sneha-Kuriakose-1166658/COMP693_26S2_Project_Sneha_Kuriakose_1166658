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

1.Merged — lined up weather and pollution readings onto the same hourly timeline (they didn't originally match up, since pollution readings landed at a different point in the hour than weather readings)
2.Cleaned — filled small gaps, capped extreme outlier readings instead of deleting them, removed clearly broken sensor values
3.Labeled — assigned a risk category (Good → Hazardous) to every row, using the AQI logic built the week before
4.Tested — ran the full process end-to-end for two very different cities, to check it works consistently


##Results
Auckland: consistently "Good" air quality, with no variation across the whole dataset

Delhi: a wide, varying range of risk levels — from Good all the way to Hazardous




## Week 6 - Feature Wngineering

#What Was Done This Week

Gave the model more to actually learn from — up to this point, each row only had raw weather and pollution readings for a single hour, with no sense of context.

--Time context — hour of day (encoded so late night and early morning are treated as close together, not opposite extremes), day of week, season, and a weekend flag — since pollution patterns differ a lot depending on the time of day.
--Rolling averages — what pollution and weather looked like over the last 24 hours, not just the current instant.
--Lag features — what the reading was exactly 24 hours earlier, since pollution levels are often similar to the same time the day before.
--Tested — ran feature_engineering.py end-to-end for both cities via demo_week4.py, which prints a before/after comparison to confirm it works correctly.


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
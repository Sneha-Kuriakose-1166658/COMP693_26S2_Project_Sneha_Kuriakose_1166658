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

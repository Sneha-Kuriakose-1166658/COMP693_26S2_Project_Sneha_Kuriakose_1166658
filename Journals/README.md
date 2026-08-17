# Journals

Weekly project journal, documenting tasks completed, hours spent, plans,
and reflections for each week.

- ** Weeks 3–5


## Week 3: 27 July 2026 – 2 August 2026
#Tasks Completed and Time Spent on each Task 
	
Project setup (GitHub repository, virtual environment, project structure)
Time : 2 hours	
Studied Open-Meteo and OpenAQ API documentation
Time : 3 hours	                    
Developed Python script to collect historical weather data from Open-Meteo
Time : 4 hours	
Developed Python script to collect historical air quality data from OpenAQ
Time : 6 hours	
Debugging API authentication, timestamp formatting, and rate limit issues
Time : 4 hours	

#Project Hours this week	
19 hours	
#Sum of Total Project Hours (incl. prior week’s hours)	22 hours

#Plans/Goals for Next Week 
	
•	Clean the weather and air quality datasets.
•	Handle missing values and duplicate records.
•	Merge the datasets using timestamps.
•	Convert pollutant measurements into air quality risk categories (Good, Moderate, Unhealthy.)

#Reflections 
	This week focused on building the data collection pipeline, which forms the foundation of the project. Although I expected data collection to be straightforward, I discovered that working with real-world APIs requires careful attention to authentication, timestamp formatting, and API usage limits. I learned how to read API documentation, process JSON responses, and convert them into structured datasets using Pandas. The biggest challenge was the OpenAQ API, where requests initially failed due to authentication requirements and rate limits. After investigating the documentation and testing different approaches, I implemented API key authentication, corrected the timestamp format, limited the number of monitoring stations, and added delays between requests. Successfully solving these issues strengthened my debugging skills. By the end of the week, I had successfully generated weather and air quality datasets 


## Week 4:  03/08/2026 to 09/08/2026

#Tasks Completed and Time Spent on each Task 
	•	Implemented the AQI labeling logic (src/labeling.py) using official EPA breakpoint tables for PM2.5, PM10, and NO2
Time :3 hours
•	Built and ran a demo script (demo_week2.py) to validate the labeling logic against four official EPA reference values
Time: 2 hours
•	Reviewed fetch_air_quality.py in detail and identified that the documented 'auto' fallback mode (switching to Open-Meteo if no OpenAQ station is found) is not yet fully implemented
Time :1 hour
•	Identified that the PM2.5 breakpoints currently used are the pre-2024 EPA table and need updating to the current standard
Time: 1 hour
•	Updated the project README to document Week 2 progress
Time: 1 hour
•	Researched other openly available data sources that could improve prediction accuracy such as traffic density and wildfire/fire activity data
Time : 2 hours
•	Started learning Streamlit, for the planned web application interface
Time: 3 hours


#Project Hours this week	13 hours	Sum of ##Total Project Hours (incl. prior week’s hours)	35 hours

#Plans/Goals for Next Week 
	•	Merge the weather and pollution datasets into a single combined dataset per city
•	Feature engineering — add other necessary columns to the merged table
•	Target output: one row per hour, combining weather and pollution information, covering the past year

#Reflections 
	What has gone well?
 Validating the AQI calculation against official EPA reference values gave confidence that the core labeling logic is mathematically correct.

What has not gone as expected?
 Found that the PM2.5 breakpoints in use are outdated (pre-2024 EPA standard), and that a fallback feature described in the code's documentation isn't actually implemented yet


## Week 5: 10 August 2026 – 16 August 2026

#Tasks Completed and Time Spent on each Task 
	
•	Built src/merge_clean.py — the core pipeline step that joins the weather and air-quality datasets on date time, removes duplicate timestamps, resamples both to hourly frequency, and interpolates small gaps Time: 4 hours
•	Added pollutant cleaning logic — removing negative/invalid sensor readings and clipping extreme outliers using the IQR method — before merging with the weather data
Time: 3 hours
•	Built demo_week3.py to run the pipeline live and print a before/after comparison: raw, separate datasets in, cleaned/merged/labelled dataset out
Time: 2 hours
Time: 2 hours
•	Reviewed the merged output for both cities and found that Auckland's data is 100% labelled "Good" with no risk variation at all, while Delhi's spans the full risk scale
Time: 2 hours

#Project Hours this week	15 hours	
#Sum of Total Project Hours (incl. prior week’s hours)	50 hours

#Plans/Goals for Next Week 
	•	Run the pipeline for the remaining NZ cities (Wellington, Hamilton, Christchurch)
•	Start feature engineering
•	Begin training the model on the merged dataset

#Reflections 
	Successfully built and validated the merge-and-clean pipeline, and confirmed it runs correctly end-to-end for two very different cities.



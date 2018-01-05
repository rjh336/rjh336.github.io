---
layout: single
title: "Exploring MTA Turnstiles Data"
category: Projects
---

As a data science student at [Metis](https://www.thisismetis.com/) my first project involved exploring the MTA's [turnstile data](http://web.mta.info/developers/turnstile.html). The goal was to use this data to recommend placement of street teams for a fictional non-profit organization so they can get interested NYC commuters signed up to their email list.  

## Approach
My approach to this challenge was to recommend a shortlist of stations with the highest amount of foot traffic to the non-profit. For this project I primarily used the MTA turnstile data to come up with our shortlist, but I also considered weather data from the NOAA to see if bad weather (significant precipitation) had an effect on the ranking of certain subway stations.  

## Datasets
I wrote a Python script to grab the last three years of data from the MTA Turnstile repository, which stores the counts of entries and exits for each turnstile in each subway station in the MTA transit system. The data is added as text files to this repository each week on Saturday and contains readings of the turnstile coutners at approximately four-hour intervals. The MTA defines the attributes of these turnstile datasets in a [data dictionary](http://web.mta.info/developers/resources/nyct/turnstile/ts_Field_Description.txt) per the following:
<br>  
<br>  

  
| NAME | DESCRIPTION |
| :------ | :------|
| C/A | Control Area (A002) |
| UNIT| Remote Unit for a station (R051) |
| SCP| Subunit Channel Position represents an specific address for a device (02-00-00) |
| STATION | Represents the station name where the device is located |
| LINENAME | Represents all train lines that can be boarded at this station |
| DIVISION  | Represents the Line originally the station belonged to BMT, IRT, or IND  |  
| DATE      | Represents the date (MM-DD-YY) |
| TIME      | Represents the time (hh:mm:ss) for a scheduled audit event |
| DESC      | Represent the "REGULAR" scheduled audit event (Normally occurs every 4 hours). Audits may occur more that 4 hours due to planning, or troubleshooting activities. Additionally, there may be a "RECOVR AUD" entry: This refers to a missed audit that was recovered.  |
| ENTRIES   | The cumulative entry register value for a device |
| EXIST     | The cumulative exit register value for a device |  
<br>  
<br>  
  
I also created the following columns:  
  
<br>  
<br>  

| NAME | DESCRIPTION |
| :------ | :------ |
| entry_diff | Total entries for a given time interval (difference between rows for "ENTRIES") |
| exit_diff | Total exits for a given time interval (difference between rows for "EXITS") |
| TOTAL_TRAFFIC | Sum of entry_diff and exit_diff |
| DOW | Day of week for a given date of counter reading |

For weather data, I requested the last three years of weather observations from the NOAA's [National Climatic Data Center](https://www.ncdc.noaa.gov/cdo-web/search?datasetid=GHCND), using CENTRAL PARK (Station ID: GHCND:USW00094728) as a proxy for the entire city's weather. I selected the only two attributes in this data set which were relevant to rainfall, which are as follows:  
  
<br>  
<br>  
| NAME | DESCRIPTION |
| :------ |:------ |
| DATE | Date of observation |
| PRCP | Inches of precipitation for the day of observation |  
<br>  
<br>  

## Assumptions
**1. Time Constraints: **  Because the non-profit was trying to garner interest for a gala happening around the beginning of the summer, I assumed the street teams would be out canvassing in the three preceding months: April, May, and June. Thus, I only pulled turnstile data for only those three months in 2015, 2016, and 2017.

**2. Counter Values: **  I assumed the "ENTRIES" and "EXITS" columns reflected cumulative counts that could only increase as time moved forward. Thus, I removed any rows with negative values in my "entry_diff" and "exit_diff" columns (approximately 1.4% of the rows).

**3. Target Metric: **  I did not differentiate between "ENTRIES" and "EXITS" for a station, but rather relied on "TOTAL_TRAFFIC" to determine which station would have the most foot traffic at a given time and thus be the best place for the street teams to visit.

**4. Outlier Counts: ** I assumed that any "entry_diff" or "exit_diff" value over 86,400 (based on total seconds in a day) was an outlier that needed to be removed.


## Findings
Figure A shows the top ten stations ranked in order of most average daily "TOTAL_TRAFFIC". From this chart we can see that the busiest stations are located around midtown Manhattan, as reflected in the map (Figure B).  
  
##### Figure A    
<img src="../images/img/top_ten_overall.png" alt="Fig A" align="left" style="width: 600px; height: 300px;"/>  
<br>
<br>
##### Figure B  
<img src="../images/img/google_map.png" alt="Fig B" align="left" style="width: 250px; height: 310px;"/>  
<br>  
<br>  
Figure C shows the effect of rain on ridership. For this chart, I labeled the data using a binary variable called "BAD_WEATHER". I also set a cutoff for PRCP at its daily mean for the periods in my weather data set which came out to **0.12 inches **of precipitation. For BAD_WEATHER to be true, the PRCP had to be greater than this cutoff value (which was true for approximately **21%** of the dates included). The chart shows that the presence of precipitation tends to coincide with a decrease ridership across the top ten most active stations. Although weather seems to have an effect on overall ridership, it does not necessarily change the ranking of our top ten most active stations as the stations in Figure A and Figure C look to be similar.
<br>  
<br>  
##### Figure C  
<img src="../images/img/top_ten_weather.png" alt="Fig C" align="left" style="width: 700px; height: 400px;"/>   
<br>  
<br>  
Figure D shows a distribution of average daily total traffic across the top ten stations by total traffic in our data set for each day of the week. Intuitively, we would think that Saturday and Sunday would show less traffic than the week days because there will be a higher volume of commuters going to work during the week. Additionally, we can see that Wednesday and Thursday have the most riders entering and exiting turnstiles--a station has an average of around 90,000 exits and entries per day on Wednesday and Thursday. The boxplots in Figure D also indicate that the distributions of traffic across all days are skewed right (i.e. there tends to be days/times that have far far more average total traffic than other days/times). This may also indicate that there are some outliers in my data that need to be removed since this trend is consistent (i.e. my cutoff for detecting error-induced outliers as a result of unclean data needs to be lower).
<br>  
<br>  
##### Figure D  
<img src="../images/img/top_ten_DOW.png" alt="Fig D" align="left" style="width: 700px; height: 400px;"/>  
<br>  
<br>  
Figure E shows the average traffic for our top ten stations at five different time intervals:  

- 00:00:00 - hours between midnight and 4AM
- 04:00:00 - hours between 4AM and 8AM
- 08:00:00 - hours between 8AM and Noon
- 12:00:00 - hours between Noon and 4PM
- 16:00:00 - hours between 4PM and 8PM
- 20:00:00 - hours between 8PM and midnight

Not surprisingly, we see the most foot traffic occur at "peak" or rush-hour time intervals of 8:00AM to Noon and 4:00PM to 8:00PM.

##### Figure E  
<img src="../images/img/top_ten_time_of_day.png" alt="Fig E" align="left" style="width: 700px; height: 400px;"/>  

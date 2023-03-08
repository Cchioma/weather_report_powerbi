# Data Analysis Challenge: Impact of Weather Conditions on Air Quality
(2010–2014)

![](air_quality_intro.jpg)
___
## Introduction
This purpose of this project is to assess the impact of weather conditions on air quality.

### What is air quality?

Air quality is a measure of how clean or polluted the air is. 

### What is PM2.5?
Particulate matter 2.5 (PM2.5 ), refers to tiny particles or droplets in the air that are 2 1/2 microns or less in width. Like inches, meters and miles, a micron is a unit of measurement for distance. It is a mixture of solid and liquid particles that are suspended in the air. These are categorized into coarse, fine and ultrafine. These particles usually come from cars, trucks, buses and off-road vehicle (e.g., construction equipment, snowmobile, locomotive) exhausts, and other operations that involve the burning of fuels such as wood, heating oil or coal and natural sources such as forest and grass fires. 

## Statement Problem
1. What weather factors greatly influence the PM 2.5 value?
2. Which month and year recorded the highest and lowest PM 2.5 value?
3. What is the forecast projection of the pm2.5 value in the next 4 years?

## Skills/ Concepts Demonstrated
The following PowerBI features were incorporated into this project: 
- DAX,
- Bookmarking
- Tooltips

## Data Modelling
I created a one column dataset in excel containing the year values only. I also included the additional years I wanted to calculate pm2.5 values for (2015- 2018). I then uploaded it into my power query editor. It was linked automatically with the weather dataset using the year column.

![](excel.PNG)

![](modelling.PNG)
___

## Data Preparation/Cleaning
The dataset contained 13 columns and 41,757 rows. The dataset can be found [here](https://archive.ics.uci.edu/ml/datasets/Beijing+PM2.5+Data)
I cleaned them as follows:
 
1. I deleted all null values
2.  I created a new measure "short_month" showing the name of the month.- I created this value from the month column provided in the dataset:
 
 ``` short_month = SWITCH('PRSA_data_2010 1 1-2014 12 31 (1)'[month],```
   ```1, "JAN", 2, "FEB", 3, "MAR", 4, "APR", 5, "MAY",6,"JUN",7, "JUL", 8,"AUG",9, "SEP",10, "OCT", 11, "NOV", 12, "DEC", BLANK())```
   
3. I changed the data type of the date column to date
4. I created a new measure "avg_pm2.5 " from the pm2.5 value :

``` avg_pm2.5 = AVERAGE('PRSA_data_2010 1 1–2014 12 31 (1)'[pm2.5])```

5. I created a new measure "CAGR" ( compound annual growth rate ) which calculated the annual growth rate of the
 pm2.5 value over a period of time (2010 -2014).

  ```
  CAGR = 
  VAR Lastyear = [Last year]
  VAR Firstyear  = [First year]
  VAR No_of_year = [Last year]-[First year]
  RETURN

  POWER(
  DIVIDE(
    CALCULATE([avg_pm2.5],Sheet1[Year] = Lastyear),
     CALCULATE([avg_pm2.5], Sheet1[Year] = Firstyear)  ), 1 /No_of_year) - 1 

```
6. I created another measure "forecast2" which calculated the forecast pm2.5 values for years greater than the
  "Last year" variable defined above.

  ```
  forecast2 = 
  VAR Lastyear = [Last year]
  VAR No_of_years = SELECTEDVALUE(Sheet1[Year]) - [Last year]

  RETURN
  IF(SELECTEDVALUE(Sheet1[Year]) >= Lastyear,
  CALCULATE([avg_pm2.5], Sheet1[Year] = Lastyear)
  * POWER((1 + [CAGR]), No_of_years) )
  ```


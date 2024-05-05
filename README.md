# SQL_Projects
select * from test_database;

--Write a code to check NULL values
select * from test_database
where
	province IS NULL OR
	country_region IS NULL OR
	latitute IS NULL OR
	longitute IS NULL OR
	date IS NULL OR
	confirmed IS NULL OR
	deaths IS NULL OR
	recovered IS NULL;

--If NULL values are present, update them with zeros for all columns
UPDATE test_database
SET
	province = COALESCE(province,'Not Applicable'),
	country_region = COALESCE(country_region, 'Not Applicable'),
	latitute = COALESCE(latitute,0.0),
	longitute = COALESCE(longitute,0.0),
	date = COALESCE(Date,'1970-01-01' ::DATE),
	confirmed = COALESCE(confirmed,0),
	deaths = COALESCE(deaths,0), 
	recovered = COALESCE(recovered,0);

--check total number of rows
select count(*) from test_database;

--Check what is start_date and end_date
select MIN(Date) AS start_date,MAX(Date) AS end_date
from test_database;

--Number of month present in dataset
select EXTRACT(MONTH FROM date) AS month_num, COUNT(*) AS month_count
from test_database
GROUP BY month_num
ORDER BY month_num;

--Find monthly average for confirmed, deaths, recovered
SELECT
	EXTRACT(YEAR FROM Date) AS year_num,
	EXTRACT(MONTH FROM Date) AS month_num,
	ROUND(AVG(Confirmed), 2) AS Confirmed_avg,
	ROUND(AVG(Deaths), 2) AS deaths_avg,
	ROUND(AVG(Recovered), 2) AS Recovered_avg
FROM test_database
GROUP BY year_num,month_num
ORDER BY year_num,month_num ASC;

--Find most frequent value for confirmed, deaths, recovered each month 
WITH FrequentValues AS (
	select
		EXTRACT(MONTH FROM Date) AS month_num,
		EXTRACT(YEAR FROM Date) AS year_num,
		confirmed,
		Deaths,
		Recovered,
		RANK() OVER (PARTITION BY EXTRACT (MONTH FROM Date),
					EXTRACT(YEAR FROM Date)
					ORDER BY COUNT(*) DESC) as rank
	FROM 
		test_database
	GROUP BY
		EXTRACT (MONTH FROM Date),EXTRACT(YEAR FROM Date),confirmed,Deaths,Recovered
)
SELECT 
	month_num,
	year_num,
	Confirmed,
	Deaths,
	recovered
FROM
 	FrequentValues
WHERE
	rank = 1
ORDER BY
	year_num,month_num ASC;
	
	
--Find minimum values for confirmed, deaths, recovered per year
SELECT
	EXTRACT(YEAR FROM Date) AS year_num,
	MIN(Confirmed) AS min_Confirmed,
	MIN(Deaths) AS min_Deaths,
	MIN(recovered) AS min_recovered
FROM 
	test_database
GROUP BY
	year_num
ORDER BY
	year_num ASC;

--Find maximum values of confirmed, deaths, recovered per year
SELECT
	EXTRACT(YEAR FROM Date) AS year_num,
	MAX(Confirmed) AS min_Confirmed,
	MAX(Deaths) AS min_Deaths,
	MAX(recovered) AS min_recovered
FROM 
	test_database
GROUP BY
	year_num
ORDER BY
	year_num ASC;
	
--The total number of case of confirmed, deaths, recovered each month
SELECT
	EXTRACT(YEAR FROM Date) AS year_num,
	EXTRACT(MONTH FROM Date) AS month_num,
	SUM(Confirmed) AS min_Confirmed,
	SUM(Deaths) AS min_Deaths,
	SUM(recovered) AS min_recovered
FROM 
	test_database
GROUP BY
	year_num,month_num
ORDER BY
	year_num ASC, month_num ASC;
	
	
--Check how corona virus spread out with respect to confirmed case
--      (Eg.: total confirmed cases, their average, variance & STDEV )	
SELECT
	SUM(confirmed) AS total_confirmed_cases,
	AVG(confirmed) AS avg_confirmed_cases,
	VARIANCE(confirmed) AS variance_confirmed_cases,
	STDDEV(confirmed) AS stddev_confirmed_cases
FROM
	test_database;
	
--Check how corona virus spread out with respect to death case per month
--      (Eg.: total confirmed cases, their average, variance & STDEV )
SELECT
	EXTRACT(YEAR FROM Date) AS year_num,
	EXTRACT(MONTH FROM Date) AS month_num,
	SUM(Deaths) AS min_Deaths,
	ROUND(AVG(Deaths), 2) AS avg_deaths,
	ROUND(VARIANCE(Deaths),2) AS variance_deaths,
	ROUND(STDDEV(Deaths), 2) AS standard_dev_deaths
FROM test_database
GROUP BY year_num,month_num
ORDER BY year_num,month_num ASC;

--Check how corona virus spread out with respect to recovered case
--      (Eg.: total confirmed cases, their average, variance & STDEV )
SELECT
	EXTRACT(YEAR FROM Date) AS year_num,
	EXTRACT(MONTH FROM Date) AS month_num,
	SUM(Recovered) AS total_Recovered,
	ROUND(AVG(Recovered), 2) AS avg_Recovered,
	ROUND(VARIANCE(Deaths),2) AS variance_deaths,
	ROUND(STDDEV(Deaths), 2) AS standard_dev_deaths
FROM test_database
GROUP BY year_num,month_num
ORDER BY year_num,month_num ASC;

--Find Country having highest number of the Confirmed case
SELECT 
	Country_Region,
	SUM(confirmed) AS total_confirmed_cases
FROM test_database
GROUP BY Country_Region
ORDER BY total_confirmed_cases DESC
LIMIT 1;
	
--Find Country having lowest number of the death case	
WITH rankingCountry AS (
	SELECT
		Country_Region AS Country,
		SUM(Deaths) AS total_Death_reported,
		RANK() OVER(ORDER by SUM(Deaths) ASC) AS rank_no
	FROM
		test_database
	GROUP BY
		country
)	
SELECT
	country
	total_Death_reported
FROM 
	rankingCountry
WHERE
	rank_no = 1;
	
--Find top 5 countries having highest recovered case	
SELECT
	Country_Region,	
	SUM(Recovered) AS total_Recovered_cases
FROM
	test_database
GROUP BY
	Country_Region
ORDER BY
	 total_Recovered_cases DESC
LIMIT 5;





 

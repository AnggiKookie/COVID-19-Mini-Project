
# COVID-19 Mini project

This project is about the total confirmed, death, and recovered case of COVID-19 in Worldwide and Southeast Asia. I use BigQuery to perform my data manipulation process and visualize it using Google Data Studio.
Here the query(s) which are used in this project:


## The Trend of Death Case in 2020-2021

    SELECT extract(year from date) year, extract(month from date) month, date, sum(deaths) deaths
    FROM `bigquery-public-data.covid19_jhu_csse.summary`
    GROUP BY date
    ORDER BY year desc, date desc
## The Confirmed, Death, and Recovered Case of COVID-19 in Worldwide from 2020 to 2021
    SELECT a.date, a.country_region, IFNULL(a.recovered, 0) recovered, IFNULL(b.deaths,0) deaths, IFNULL(b.confirmed, 0) confirmed
    FROM(
        SELECT MAX(date) date, country_region, MAX(recovered) recovered
        FROM(
            SELECT date, country_region, SUM(recovered) recovered
            FROM(
                SELECT date, country_region, active, CAST(recovered as INT) recovered
                FROM `bigquery-public-data.covid19_jhu_csse.summary`) table_a
            WHERE recovered IS NOT NUL AND country_region IS NOT NULL 
            GROUP BY country_region, date)
    GROUP BY country_region
    ORDER BY country_region ASC) a
    LEFT JOIN 
    (SELECT date, country_region, SUM(deaths) deaths, SUM(confirmed) confirmed
    FROM `bigquery-public-data.covid19_jhu_csse.summary`
    WHERE date IN (
        SELECT date
        FROM `bigquery-public-data.covid19_jhu_csse.summary`
        ORDER BY date DESC
        LIMIT 1)
    GROUP BY date, country_region
    ORDER BY country_region) b
    ON a.country_region = b.country_region
    ORDER BY country_region ASC
## The Latest Information About Confirmed, Death, and Recovered Case of COVID-19 in Southeast Asia
    SELECT a.date, a.country_region, IFNULL(a.recovered, 0) recovered, IFNULL(b.deaths,0) deaths, IFNULL(b.confirmed, 0) confirmed
    FROM(
        SELECT MAX(date) date, country_region, MAX(recovered) recovered
        FROM(
            SELECT date, country_region, SUM(recovered) recovered
            FROM(
                SELECT date, country_region, active, CAST(recovered as INT) recovered
                FROM `bigquery-public-data.covid19_jhu_csse.summary`) table_a
            WHERE recovered IS NOT NUL AND country_region IS NOT NULL 
            GROUP BY country_region, date)
    GROUP BY country_region
    ORDER BY country_region ASC) a
    LEFT JOIN 
    (SELECT date, country_region, SUM(deaths) deaths, SUM(confirmed) confirmed
    FROM `bigquery-public-data.covid19_jhu_csse.summary`
    WHERE date IN (
        SELECT date
        FROM `bigquery-public-data.covid19_jhu_csse.summary`
        ORDER BY date DESC
        LIMIT 1)
    GROUP BY date, country_region
    ORDER BY country_region) b
    ON a.country_region = b.country_region
    WHERE a.country_region IN ('Indonesia', 'Malaysia', 'Singapore', 'Myanmar', 'Cambodia', 'Laos', 'Vietnam', 'Philippines', 'Thailand', 'Brunei')
    ORDER BY country_region ASC
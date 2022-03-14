## Notes: DE-Zoomcamp 1.2.6

#### SQL Refresher

- Ingest the taxi zone look up data from [here](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

```python
df_zones = pd.read_csv('taxi+_zone_lookup.csv') 
df_zones.to_sql(name='zones', con=engine, if_exists ='replace')
```

- Get back to pgAdmin (via docker compose).

```console
stacy@ ~
$ docker-compose up
```
- Start writing SQL queries. Some examples:

1. Join `yellow_taxi_full_dataset` datset with the `zones` dataset via pickup location ID's and dropoff location ID's.

```SQL
SELECT *
FROM
	yellow_taxi_full_dataset as t,
	zones as zpu,
	zones as zdo
WHERE
	t."PULocationID" = zpu."LocationID" AND
	t."DOLocationID" = zdo."LocationID"
LIMIT 100;
```
Or alternatively,

```SQL
SELECT *
FROM
	yellow_taxi_full_dataset AS t
JOIN
	zones AS zpu ON t."PULocationID" = zpu."LocationID"
JOIN
	zones AS zdo ON t."DOLocationID" = zdo."LocationID"
LIMIT 100;
```

2. Create a subset of the joined data from example 1 and view 5 columns. `CONCAT` combines text from multiple ranges and/or strings.

```SQL
SELECT 
	tpep_pickup_datetime,
	tpep_dropoff_datetime,
	total_amount,
	CONCAT(zpu."Borough", ' / ', zpu."Zone") AS "pickup_Loc",
	CONCAT(zdo."Borough", ' / ', zdo."Zone") AS "dropoff_Loc"
FROM
	yellow_taxi_full_dataset AS t 
JOIN zones AS zpu ON t."PULocationID" = zpu."LocationID"
JOIN zones AS zdo ON t."DOLocationID" = zdo."LocationID"
LIMIT 100;
```

3. Checking if there are any dropoff locations in the `yellow_taxi_full_dataset` datset that are **NOT IN** the `zones` dataset.

```SQL
SELECT 
	tpep_pickup_datetime,
	tpep_dropoff_datetime,
	total_amount,
	"DOLocationID"
FROM
	yellow_taxi_full_dataset AS t 
WHERE
	"DOLocationID" NOT IN (
		SELECT "LocationID" FROM zones)
LIMIT 100;
```

4. Count the number of records for each day and order them by the number of records/day from lowest to highest.

```SQL
SELECT 
	CAST(tpep_pickup_datetime AS DATE) AS day,
	COUNT(1) as count
FROM
	yellow_taxi_full_dataset AS t 
GROUP BY day
ORDER BY count ASC;
```

**Additional Notes** 

- A useful image to remeber the different `JOIN` statements.

![image](https://user-images.githubusercontent.com/54118138/158254756-d137b4cf-8fd7-49ea-9571-eb85af815523.png)

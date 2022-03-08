
### Ingesting NY Taxi Data to Postgres

1. Run docker code to create a container with prostgres image.

![image](https://user-images.githubusercontent.com/54118138/157337524-6d787014-fa2e-4157-904c-0bc1133cc491.png)

  - -e: sets environment parameters.
  - -v: maps a folder of our host machine to a folder in the container.
  - -p: maps a port in our host machine to a port in the container. 

2. Use pcgli (see other blog post about my journey to get pgcli to work). This is a way to connect to the database. You need to specify the host (-h), port (-p), user (-u), and database (-d).

```consule
pgcli -h localhost -p 5432 -u root -d ny_taxi
```

Note: I need to run this in a windows terminal or a terminal in VS code (does not work in GitBash). When you hit enter, it will prompt you for your password. 

![image](https://user-images.githubusercontent.com/54118138/157325156-0dc718e0-4d96-454c-9fb2-38c373d99e19.png)

3. Next use Jupyter Notebook and Python to import the NY Taxi Data to the database. 

```python
import pandas as pd #importing pandas module
```


```python
df = pd.read_csv('yellow_tripdata_2021-01.csv', nrows = 100) #reading in the first 100 rows of the dataset
```


```python
print(pd.io.sql.get_schema(df, name ='yellow_taxi_full_dataset')) #creating a schema (specifies what columns and what type they are) coverting dataframe to DDL (language used for specifying schemas); notice pickup and dropoff times are TEXT
```

```python
df.tpep_pickup_datetime = pd.to_datetime(df.tpep_pickup_datetime) #changing pickup and drop off time as timestamp
df.tpep_dropoff_datetime = pd.to_datetime(df.tpep_dropoff_datetime)
```


```python
import sqlalchemy #importing sqlachemy module
```


```python
from sqlalchemy import create_engine #importing create_engine from the sqlalchemy module
```


```python
engine = create_engine('postgresql://root:root@localhost:5432/ny_taxi') #creating the 'engine' that connects to postgres.
```


```python
engine.connect() #checks that the connection is there and the database is up and running
```

```python
print(pd.io.sql.get_schema(df, name ='yellow_taxi_full_dataset', con = engine)) #sending schema to postgres
```

```python
df_iter = pd.read_csv('yellow_tripdata_2021-01.csv', iterator = True, chunksize = 100000) #Splitting dataset into chunks. Will read chunks into postgres 1 at a time
```


```python
df = next(df_iter) #redefining the df from above (that only contained the first 100 rows)
```


```python
df.tpep_pickup_datetime = pd.to_datetime(df.tpep_pickup_datetime) #changing pickup and drop off time as timestamp
df.tpep_dropoff_datetime = pd.to_datetime(df.tpep_dropoff_datetime)
```


```python
df.head(n=0).to_sql(name='yellow_taxi_full_dataset', con=engine, if_exists ='replace') #creating a table in postgres with just the column names and no data
```


```python
df.to_sql(name='yellow_taxi_full_dataset', con=engine, if_exists ='append') #adding data to the table (the first 100000 rows)
```


```python
while True:
    df = next(df_iter)
    
    df.tpep_pickup_datetime = pd.to_datetime(df.tpep_pickup_datetime) #changing pickup and drop off time as timestamp
    df.tpep_dropoff_datetime = pd.to_datetime(df.tpep_dropoff_datetime)
    
    df.to_sql(name='yellow_taxi_full_dataset', con=engine, if_exists ='append') #adding the next 100000 rows
    
    print('inserted another chunk...')
```

4. Checking to see if data is in the databse.

```consule
\dt
```

```consule
SELECT count(1) FROM yellow_taxi_full_dataset;
```

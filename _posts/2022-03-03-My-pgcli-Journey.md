### My pgcli Journey

**Finding solutions took more time than the post might suggest**

Today, my plan was to tackle the second video for week 1 in the DE Zoompcast series. I first ran into issues when running the docker code to create a container with postgres as the image. I ran:

```console
docker run it \
  -e POSTGRES_USER="root" \
  -e POSTGRES_PASSWORD="root" \
  -e POSTGRES_DB="ny_taxi" \
  -v C://Users//stacy//Documents//DE-Zoomcamp//ny_taxi_postgres_data:/var/lib/postgresql/data \
  -p 5432:5432 \
  postgres:13
```
And received an error of `Error response from daemon: invalid mode: \Program Files\Git\var\lib\postgresql\data.`. Fellow learners pointed out that window folders need to use a  quisi-linux path in the WSL (windows subsystem for linux). Notice the `//` in my host machines file path.
  
```console
docker run it \
  -e POSTGRES_USER="root" \
  -e POSTGRES_PASSWORD="root" \
  -e POSTGRES_DB="ny_taxi" \
  -v C://Users//stacy//Documents//DE-Zoomcamp//ny_taxi_postgres_data:/var/lib/postgresql/data \
  -p 5432:5432 \
  postgres:13
```

That was a relatively easy solution to find. The next issue I ran into was with installing pgcli. (Pgcli is a command line interface for Postgres with auto-completion and syntax highlighting. - [source](https://www.pgcli.com/)). When I tried to install pgcli with `pip install pgcli` I received the error:

![image](https://user-images.githubusercontent.com/54118138/156664774-c2e3a6f8-a920-4e7f-a32a-a5f055cf3b5d.png)

Here is what I did:
  - Download Microsoft Visual Studio (different than Visual Studio Code). Go [here](https://visualstudio.microsoft.com/) to do so. If you already have it you can go [here](https://visualstudio.microsoft.com/visual-cpp-build-tools/). Note: You want to download 'build tools' and not redistributions. 
  - Go to Visual Studio Installer. Click modify. You may need to change workloads or select additional boxes in the option section on the far right under Desktop development with C ++. I used this [sight](https://docs.microsoft.com/en-us/answers/questions/136595/error-microsoft-visual-c-140-or-greater-is-require.html) as a referene of what I needed to do. Here is a screenshot from my computer.

![image](https://user-images.githubusercontent.com/54118138/156666574-7a6cca70-b566-4cb5-9ba2-ea40643bb2fb.png)

### Update (next day):

This morning I used docker to create a container with postgres. I then typed the following in GitBash

```console
stacy@ ~
$pgcli -h localhost -u root -p 5432 -d ny_taxi
```
I received a prompt to enter my password (yes!). I entered my password and nothing happened. 

![image](https://user-images.githubusercontent.com/54118138/156851397-977da0f9-40e0-43fa-963b-2379efedaa59.png)

When I  tried to close the window I received this message.

![image](https://user-images.githubusercontent.com/54118138/156851479-f4ee0032-315e-4d9d-8232-74540726ea4b.png)

This tells me that something is happening. The question is what?

### Update (a few days later):

Others (in the DataTalks #course-data-engineering channel) had the same issue as me and suggested I run the `pgcli -h localhost -u root -p 5432 -d ny_taxi' code in a windows terminal or the terminal in VS code. Both of these worked!

I also, tried out the alternative method suggested by Alexey Grigorev in [this video](https://www.youtube.com/watch?v=3IkfkTwqHx4&list=PL3MmuxUbc_hJed7dXYoJw8DoCuVHhGEQb). The alternative route suggests using the sqlalchemy module (via juptyer notebook with anaconda). I need to install the psycopg2 module. I did this by typing `conda install psycopg2` in an anaconda prompt. 

Here is my code following along with the video:

```python
import pandas as pd #importing pandas module
```


```python
import sqlalchemy #importing sqlachemy module
```


```python
from sqlalchemy import create_engine #importing create_engine from the sqlalchemy module
```


```python
engine = create_engine('postgresql://root:root@localhost:5432/ny_taxi') #creating the 'engine' that connects to postgres. "The above engine creates a Dialect object tailored towards PostgreSQL, as well as a Pool object which will establish a DBAPI connection at localhost:5432 when a connection request is first received."
```


```python
engine.connect() #checks that the connection is there and the database is up and running
```




    <sqlalchemy.engine.base.Connection at 0x23894a45940>




```python
# Example of how to send a query to postgres and produces the results
query = """
SELECT 1 as number;
"""

pd.read_sql(query, con=engine)
```





```python
# Didn't output anything because there is no table...
query = """
SELECT *
FROM pg_catalog.pg_tables
WHERE schemaname != 'pg_catalog' AND
    schemaname != 'information_schema';
"""

pd.read_sql(query, con=engine)
```

```python
df = pd.read_csv('yellow_tripdata_2021-01.csv', nrows = 100) #reading in the first 100 rows of the dataset
```

```python
df #viewing the dataset
```

```python
print(pd.io.sql.get_schema(df, name ='yellow_taxi_data')) #notice pickup and dropoff times are TEXT
```

    CREATE TABLE "yellow_taxi_data" (
    "VendorID" INTEGER,
      "tpep_pickup_datetime" TEXT,
      "tpep_dropoff_datetime" TEXT,
      "passenger_count" INTEGER,
      "trip_distance" REAL,
      "RatecodeID" INTEGER,
      "store_and_fwd_flag" TEXT,
      "PULocationID" INTEGER,
      "DOLocationID" INTEGER,
      "payment_type" INTEGER,
      "fare_amount" REAL,
      "extra" REAL,
      "mta_tax" REAL,
      "tip_amount" REAL,
      "tolls_amount" REAL,
      "improvement_surcharge" REAL,
      "total_amount" REAL,
      "congestion_surcharge" REAL
    )
    


```python
df.tpep_pickup_datetime = pd.to_datetime(df.tpep_pickup_datetime) #changing pickup and drop off time as timestamp
df.tpep_dropoff_datetime = pd.to_datetime(df.tpep_dropoff_datetime)
```


```python
print(pd.io.sql.get_schema(df, name ='yellow_taxi_data'))
```

    CREATE TABLE "yellow_taxi_data" (
    "VendorID" INTEGER,
      "tpep_pickup_datetime" TIMESTAMP,
      "tpep_dropoff_datetime" TIMESTAMP,
      "passenger_count" INTEGER,
      "trip_distance" REAL,
      "RatecodeID" INTEGER,
      "store_and_fwd_flag" TEXT,
      "PULocationID" INTEGER,
      "DOLocationID" INTEGER,
      "payment_type" INTEGER,
      "fare_amount" REAL,
      "extra" REAL,
      "mta_tax" REAL,
      "tip_amount" REAL,
      "tolls_amount" REAL,
      "improvement_surcharge" REAL,
      "total_amount" REAL,
      "congestion_surcharge" REAL
    )
    


```python
df.to_sql(name='yellow_taxi_data', con=engine) #sending the subsetted data to postgres
```


```python
# Creating another query, results in a pandas data frame.
query = """
SELECT *
FROM yellow_taxitrip
LIMIT 10;

"""

pd.read_sql(query, con=engine)
```





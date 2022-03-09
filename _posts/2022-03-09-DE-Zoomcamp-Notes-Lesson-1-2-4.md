### Dockerizing the ingestion script

Basically in this video we are combining the information from the first three videos into a single process. 

- The first thing that we need to do is to convert the jupyter notebook that ingested the data to postgres to a python script.
  - In VS code go to the `.ipynd` file -> `...` -> `Export` -> `Python`.
  - Spend some time updating and cleaning the code.
      - To test the new python script I needed to download wget ([source)[https://eternallybored.org/misc/wget/]), then move the `weg.exe` file to `C:\Program Files\Git\mingw64\bin`.
      - This will be unecessary when dockerizing the process.

- Dockerize the process:
  - Build a docker image that looks for any Docker file in our current directory.
  - Use docker to 
    - Specify the network we're using.
    - Run the image we just built
      - Specify the parameters associated with the python script.

**Overall the new steps are:**

1. Create a network. (Only need to do this once)

```console
stacy@ ~
$ docker network create pg-network
```

2. Create a postgres docker container. (May need to remove container first: `docker rm <container ID>`; `docker ps - aq` - list of container IDs)

```console
stacy@ ~
$ docker run -it \
  -e POSTGRES_USER="root" \
  -e POSTGRES_PASSWORD="root" \
  -e POSTGRES_DB="ny_taxi" \
  -v C://Users//stacy//Documents//DE-Zoomcamp//ny_taxi_postgres_data:/var/lib/postgresql/data \
  -p 5432:5432 \
  --network=pg-network \
  --name pg-database \
  postgres:13
 ```
 3. Create a pgAdmin docker container. (May need to remove container first: `docker rm <container ID>`; `docker ps - aq` - list of container IDs)

```console
stacy@ ~
docker run -it \
  -e PGADMIN_DEFAULT_EMAIL="admin@admin.com" \
  -e PGADMIN_DEFAULT_PASSWORD="root" \
  -p 8080:80 \
  --network=pg-network \
  --name pgadmin \
  dpage/pgadmin4
```

4. To build our docker image we need: 
  - Docker file.

![image](https://user-images.githubusercontent.com/54118138/157543950-b7c515a2-a698-4fa9-91c4-3e67f7760c18.png)

  - Python script.

 ```python
 import pandas as pd #importing pandas module
import sqlalchemy #importing sqlachemy module
import argparse #importing argparse module
import os #importing the os module
from email import parser
from pydoc import describe
from sqlite3 import paramstyle
from sqlalchemy import create_engine #importing create_engine from the sqlalchemy module

def main(params):
    user = params.user
    password = params.password
    host = params.host
    port = params.port
    db = params.db
    table_name = params.table_name    
    url = params.url    
    csv_name = 'output.csv'

    # download the csv
    os.system(f"wget {url} -O {csv_name}")

    engine = create_engine(f'postgresql://{user}:{password}@{host}:{port}/{db}') #creating the 'engine' that connects to postgres.
    
    df_iter = pd.read_csv(csv_name, iterator = True, chunksize = 100000) #Splitting dataset into chunks. Will read chunks into postgres 1 at a time
    df = next(df_iter) #redefining the df from above (that only contained the first 100 rows)
    df.tpep_pickup_datetime = pd.to_datetime(df.tpep_pickup_datetime) #changing pickup and drop off time as timestamp
    df.tpep_dropoff_datetime = pd.to_datetime(df.tpep_dropoff_datetime)

    df.head(n=0).to_sql(name=table_name, con=engine, if_exists ='replace') #creating a table in postgres with just the column names and no data

    df.to_sql(name=table_name, con=engine, if_exists ='append') #adding data to the table (the first 100000 rows)

    while True:
        try:
            df = next(df_iter)
        
            df.tpep_pickup_datetime = pd.to_datetime(df.tpep_pickup_datetime) #changing pickup and drop off time as timestamp
            df.tpep_dropoff_datetime = pd.to_datetime(df.tpep_dropoff_datetime)
        
            df.to_sql(name=table_name, con=engine, if_exists ='append') #adding the next 100000 rows
        
            print('inserted another chunk...')
        except StopIteration:
            print("Finished ingesting data into the postgres database")
            break


if __name__ == '__main__':
    parser = argparse.ArgumentParser(description='Ingest CSV data to Postgres')

    # user
    # password
    # host
    # port
    # database name
    # table name
    # url of the csv

    parser.add_argument('--user', required = True, help='user name for postgres')
    parser.add_argument('--password', required = True, help='password for postgres')
    parser.add_argument('--host', required = True, help='host for postgres')
    parser.add_argument('--port', required = True, help='port for postgres')
    parser.add_argument('--db', required = True, help='database for postgres')
    parser.add_argument('--table_name', required = True, help='name of the table where we will write the results to')
    parser.add_argument('--url', required = True, help='url of the csv file')

    args = parser.parse_args()

    main(args)
 ```
5. Build docker image.

```console
stacy@ ~
$ docker build -t taxi_ingest:v001 .
```

6. Run docker image to ingest data into postgres.

```console
stacy@ ~
$ URL="https://s3.amazonaws.com/nyc-tlc/trip+data/yellow_tripdata_2021-01.csv"

$ docker run -it \
  --network=pg-network \
  taxi_ingest:v001 \
  --user=root \
  --password=root \
  --host=pg-database \
  --port=5432 \
  --db=ny_taxi \
  --table_name=yellow_taxi_test2_full_dataset \
  --url=${URL}
 ```
 
 7. Use pgAdmin to run SQL queries. 
 
 












   
  



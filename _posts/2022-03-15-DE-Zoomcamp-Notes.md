# Data Engineering Zoomcamp Notes

## Notes: DE Zoomcamp 1.2.1

#### Introduction to Docker

**What is Docker?** - "Docker is a set of platform as a service (PaaS) products that use OS-level virtualization to deliver software in packages called containers...Containers are isolated from one another and bundle their own software, libraries and configuration files; they can communicate with each other through well-defined channels." - [wikipedia](https://en.wikipedia.org/wiki/Docker_(software))

**What is Docker useful for?**
- Local Experiments
- Integrated Tests (CI/CD)
- Reproducibility
- Running Pipelines on the cloud
- Spark
- Serverless

**Some Useful tidbits**
- To test that Docker is working, we can tell docker to run the image `hello-world`. To do this, type the following in MINGW64 (Git Bash). 

```console
stacy@ ~ 
$ docker run hello-world
```
![Output](https://user-images.githubusercontent.com/54118138/156458872-8eefb197-b092-4aba-b5c7-a808e7f7707d.png)

- Docker suggests we try something more ambitious and run an Ubuntu container with 

```console
stacy@ ~
$ docker run -it ubuntu bash
```
Here `ubuntu` is the image. `-it` stands for interactive terminal or interactive mode. This allows us to type a command and interact with docker. `bash` lets docker know we want to excecute bash on the image `ubuntu`. For example, we can use the bash command `ls`. 

![Output](https://user-images.githubusercontent.com/54118138/156460822-3cc9d7c1-aafa-4c14-9466-cab629e9a2de.png)

- To highlight the usefulness of docker containers, we can (do something stupid and) delete everthing from our image.

```console
stacy@ ~
root@9a961c4381ab:/# rm -rf / --no-preserve-root
```

If you type `ls` and hit enter, you will receive a message that says "No such file or directory". But if we exit the container and re-run the code we get back to the state we were before. 

![Output](https://user-images.githubusercontent.com/54118138/156463116-0f43be26-9003-4410-95a2-fb4c2a8abe9b.png)

- We can also run python by typing

```console
stacy@ ~
$ docker run -it python:3.9
```

Again, we are asking docker to run an interactive image (python version 3.9). If you haven't run this image before, then the image may take time to download. Once it runs, we can run python code.

**How to use Docker to create a data pipeline**

- In your directory of choice, go to VS code.

```console
stacy@ ~
$ code .
````

- Create a Dockerfile. This will allow us to tell Docker how to create the image we want.
    - `FROM` tells docker what image to build on (the base image).
    - `RUN` tells docker to run the following code on the image.
    - `WORKDIR` tells docker where to put the working directory within the container.
    - `COPY` tells docker to copy our `pipeline.py` python script (located on our host machine's working directory) to a file in the container's working directory (in the /app directory specified above) called pipeline.py. Note that each file has the same name. This isn't required.
    - `ENTRYPOINT` tells docker to run the pipeline.py script in python.

![image](https://user-images.githubusercontent.com/54118138/156614473-c2745799-fbb8-4e07-a81b-55bf9aad27cf.png)

- Create a python script (eg, pipeline.py). This script will be run in the docker container.
    - `import sys` is importing the sys module.
    - `import pandas as pd` is importing the pandas module and giving it the alias of 'pd'.
    - `print(sys.argv)` will list the number of arguments.
    - `day = sys.argv[1]` sets the `day` variable as the second argument.
    - `print(f'job finihsed successfully for day = {day}')` will print the statement "job finished successfully for day (whatever we list as the second argument)".

![image](https://user-images.githubusercontent.com/54118138/156616461-8b47ca73-9319-4e3f-b2b3-9a9148ea3ed4.png)

- Now in our terminal we will use docker to build an image (test:pandas) in our current director and looks for a docker file in this directory to run( .).

```console
stacy@ ~
$ docker build -t test:pandas .
```

![image](https://user-images.githubusercontent.com/54118138/156623340-5196fcf6-1083-4446-a769-524069954933.png)

- We can pass arguments to a python script while running it in a terminal. So in the terminal (-it) we can run the image we built (test:pandas, argument 1) and specify a second argument (2022-03-02 - a date).

![image](https://user-images.githubusercontent.com/54118138/156629288-f33aff66-5972-464b-9a75-38f5682b6b9e.png)

Notice that the first line of output is from the `print(sys.argv)` statement in the pipeline.py script, and the second line of output is from the `print(f'job finished successfully for day = {day}') statement in the pipeline.py script.

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

## Notes: DE Zoomcamp 1.2.2

#### Ingesting NY Taxi Data to Postgres

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

![image](https://user-images.githubusercontent.com/54118138/157337630-37dc2e53-5323-43c0-82b6-b93f5bfbb98b.png)

## Notes: DE Zoomcamp 1.2.3

#### Connecting pgAdmin and Postgres

**What is pgAdmin?** - pgAdmin is a web based administration tool for the Postgres database.

**Why is it useful?** - It is more convenient to use pgAdmin than a terminal to write SQL quesries to interact with a Postgres database.

**Side Note:**

When I tried to run this again the next day I received the error: `docker: Error response from daemon: Conflict. The container name "/pg-database" is already in use by container "ccf547d86f1dab18590b7c40fd0d9bb9fd42e1c9fadd3d4a12c9d3df290a4196". You have to remove (or rename) that container to be able to reuse that name.` 
  - I can either rename the containers OR
  - Remove the containers (both the postgres and pgAdmin containers)
    - Get a list of containers with `docker ps -aq`. 
    - Find the correct containers (hint look at the error messages or realize that the most recent containers are at the top of the list).
    - Remove the containers with `docker rm <container ID>`
  - Re-run the docker code.

**Steps**

1. We don't actually need to install pgAdmin (although we could). Instead we can use docker to pull an image that contains pgAdmin. However, since Postgres and pgAdmin will be in seperate containers we need to create a network allowing the containers to connect. We will create a network with docker.

```console
stacy@ ~
$ docker network create pg-network
```
Note: you only need to do this once. 

We've created a network called `pg-network`.

2. Run updated docker code for postgres. This code now includes information describing the network we created and giving the container a name. This allows the pgAdmin container to locate the postgres container.

```console
stacy@ ~
$docker run -it \
  -e POSTGRES_USER="root" \
  -e POSTGRES_PASSWORD="root" \
  -e POSTGRES_DB="ny_taxi" \
  -v C://Users//stacy//Documents//DE-Zoomcamp//ny_taxi_postgres_data:/var/lib/postgresql/data \
  -p 5432:5432 \
  --network=pg-network \
  --name pg-database \
  postgres:13
 ```
 
3. Run docker code for pgAdmin. Just like with the postgres container there are enviroment parameters that need to be specified, the correct port mapping, network, and container name.

```console
stacy@ ~
$docker run -it \
  -e PGADMIN_DEFAULT_EMAIL="admin@admin.com" \
  -e PGADMIN_DEFAULT_PASSWORD="root" \
  -p 8080:80 \
  --network=pg-network \
  --name pgadmin \
  dpage/pgadmin4
  ```
  
4. In a web brower, go to localhost:8080.

![image](https://user-images.githubusercontent.com/54118138/157492224-109d7eaf-29d9-45d9-8900-9ded3aa3fc77.png)

5. Login using the email and password specified in the docker code.

6. Right click on Server then click Create -> Server...

![image](https://user-images.githubusercontent.com/54118138/157493747-5dd3d519-4696-4591-a820-dea8dd3e12cb.png)

7. Name the server.

![image](https://user-images.githubusercontent.com/54118138/157494349-d4cd9b34-30a6-44b9-a683-dadaa31d645b.png)

8. Specify the connection.
  - Host name/address - the name of the postgres container.
  - Port - postgres port.
  - Maintenance database - postgres.
  - username - postgres username.
  - password - postgres password.


![image](https://user-images.githubusercontent.com/54118138/157494742-00d684e6-5e1a-457d-86fd-12bbea31f001.png)

9. You can find and view the dataset (yellow_taxi_full_dataset) we imported. 

![image](https://user-images.githubusercontent.com/54118138/157496181-884a20de-9088-4f15-9284-5d777c780944.png)

To view a subset of the data you can right click on the dataset -> View/Edit data -> First 100 rows. Notice pgAdmin also provides the SQL query.

10. To write your own SQL code, go to Tools -> Query Tool. This will open a Query editor. 

## Notes: DE Zoomcamp 1.2.4

#### Dockerizing the Ingestion Script

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

## Notes: DE-Zoomcamp 1.2.5

#### Running Postgre and pgAdmin with Docker Compose.

In this video we learn how to use just one file (YAML) to run both our containers (postgres and pgAdmin) in the same network using Docker Compose. 
  
1. Create a YAML file in VS code (in the correct directory).

2. We basically use the same parameters as provided in the individual docker code. Since we are using docker-compose we don't need to create or specifiy a network beforehand.

![image](https://user-images.githubusercontent.com/54118138/157764493-d335be2a-f9a3-4d68-b28d-b5b6a584f16a.png)


3. (optional) check if any containers are running with `docker -ps`.
  
4. To get both containers running (and talking to each other) run `docker-compose up`.
  
5. Go to `localhost:8080` and sign into pgAdmin just like before. 

6. You will need to create a new server.

7. To shut down you can either
    - `ctl + c` OR
    - `docker-compose down`
 
 8. (optional) You can run docker-compose in detached mode (`docker-compose up -d`). This will allow you to use the terminal. 
 
 
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

## Introduction to Terraform Concepts and GCP Pre-Requisites.

This lesson was very straight forward. We went through these [steps](https://github.com/DataTalksClub/data-engineering-zoomcamp/blob/main/week_1_basics_n_setup/1_terraform_gcp/2_gcp_overview.md#initial-setup) for the initial setup in GCP.
  - For step 4 (Download SDK for local setup) I needed to follow these [step](https://github.com/DataTalksClub/data-engineering-zoomcamp/blob/main/week_1_basics_n_setup/1_terraform_gcp/windows.md) because I am working with windows.



 
 












   
  




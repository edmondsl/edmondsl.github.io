### Dockerizing the ingestion script

Basically in this video we are combining the information from the first three videos into a process. 

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

4. Build our docker image. 
  - Docker file

   
  



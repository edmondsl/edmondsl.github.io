### Connecting pgAdmin and Postgres

**What is pgAdmin?** - pgAdmin is a web based administration tool for the Postgres database.

**Why is it useful?** - It is more convenient to use pgAdmin than a terminal to write SQL quesries to interact with a Postgres database.

1. We don't actually need to install pgAdmin (although we could). Instead we can use docker to pull an image that contains pgAdmin. However, since Postgres and pgAdmin will be in seperate containers we need to create a network allowing the containers to connect. We will create a network with docker.

```console
stacy@ ~
$ docker network create pg-network
```

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

7. 


 

### Connecting pgAdmin and Postgres

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

 

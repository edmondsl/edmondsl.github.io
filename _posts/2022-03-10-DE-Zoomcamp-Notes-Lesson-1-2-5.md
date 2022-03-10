## Notes: DE-Zoomcamp 1.2.5

#### Running Postgre and pgAdmin with Docker Compose.

In this video we learn how to use just one file (YAML) to run both our containers (postgres and pgAdmin) in the same network using Docker Compose. 
  
1. Create a YAML file in VS code (in the correct directory).

2. We basically use the same parameters as provided in the individual docker code. Since we are using docker-compose we don't need to create or specifiy a network before-hand.

3. (optional) check if any containers are running with `docker -ps`.
  
4. To get both containers running (and talking to each other) run `docker-compose up`.
  
5. Go to `localhost:8080` and sign into pgAdmin just like before. 

6. You will need to create a new server.

7. To shut down you can either
    - `ctl + c` OR
    - `docker-compose down`
 
 8. (optional) You can run docker-compose in detached mode (`docker-compose up -d`). This will allow you to use the terminal. 
  

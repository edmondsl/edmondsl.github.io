Today, my plan was to tackle the second video for week 1 in the DE Zoompcast series. I first ran into issues when running the docker code to create a container with postgre as the image. I ran:

```console
docker run it \
  -e POSTGRES_USER="root" \
  -e POSTGRES_PASSWORD="root" \
  -e POSTGRES_DB="ny_taxi" \
  -v C://Users//stacy//Documents//DE-Zoomcamp//ny_taxi_postgres_data:/var/lib/postgresql/data \
  -p 5432:5432 \
  postgres:13
```
And received an error of `Error response from daemon: invalid mode: \Program Files\Git\var\lib\postgresql\data.`. Fellow learners pointed out that window folders need to use a  quisi-linux path in the WSL (windows subsystem for linux). 
  
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


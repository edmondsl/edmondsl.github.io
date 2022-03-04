### My pgcli Journey

**Finding solutions took more time than the post might suggest**

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

This morning I used docker to create a container with postgres. I typed

```console
stacy@ ~
$pgcli -h localhost -u root -p 5432 -d ny_taxi
```
I received a prompt to enter my password (yes!). I entered my password and nothing happened. 

  




## Introduction to Docker

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

**How to use Docker in your data pipeline?**

- In your directory of choice, go to VS code.

```console
stacy@ ~
$ code .
````

- Create a Dockerfile. This will allow us to tell Docker how to create the image we want.
    - `FROM` tells docker what image to build on (the base image).
    - `RUN` tells docker to run the following code on the image.
    - `WORKDIR` tells docker where to put the working directory within the container.
    - `COPY` tells docker to copy our `pipeline.py` python script (located in our current working directory - in this case on my harddrive) to a file in the container's working directory (in the /app directory specified above) called pipeline.py. Note that each file has the same name. This isn't required.
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



Notice that the first line of output is from the `print(sys.argv)` statement in the pipeline.py script, and the second line of output is from the `print(f'job finished successfully for day = {day}') statement in the pipeline.py script.


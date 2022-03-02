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
- To test that Docker is working, we tell docker to run the image `hello-world`. To do this, type the following in MINGW64 (Git Bash). 

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




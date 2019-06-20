+++
author = "Jayson Ryter"
categories = ["Containers"]
tags = ["tutorial"]
date = "2014-09-28"
description = "Container? Like a bottle? What's with the Whale?"
featured = "pic03.jpg"
featuredalt = "Pic 3"
featuredpath = "date"
linktitle = ""
title = "Docker Getting Started - What you need to know"
type = "post"

+++
# Straight To The Point

Docker is a pretty mysterious thing to a lot of people these days and to be honest, it went right over my head the first time I heard about it. 
The idea of a container seemed not so special to me until I started to use it and understand the power this simple tool had to offer. My deployments got faster, 
my configurations were easier to manage, and using it was easier than expected. By the end of this tutorial, you will be able to do the following: 

- Pull images from a repo and run containers
- Package an app into a container using a Dockerfile and publish to a registry
- Use docker-compose to spin up multiple containers at once

These three things were the building blocks that got me started on my path with Docker. There are a lot of Docker tutorials out there so I'm going to skip a lot of the fluff
and give you the info which I think is most relevant to get you started using Docker as quick as possible. Everything we do will also be in my public github located here

# Our Environment
For this lab I will be using the **Centos7** OS installed on **Virtualbox**. Everything we will be doing 
will be OS agnostic for maybe the exception of some commands that are translated differently if you are not using Centos as your OS. I will also be using a user with sudo privileges and not the root
account. I think it is important to realize what commands and actions you can execute without sudo and vice versa in regards to docker. 

Let's make sure we have Docker installed first...

**All files for this lab can be found in my public git repo:**
https://github.com/RyterINC/docker-getting-started

Docker already has documentation on the process which can be found [here](https://docs.docker.com/install/linux/docker-ce/centos/), but I have included in the root of our repo a script 
called `install-docker.sh` that will install Docker and docker-compose for you. I encourage you to go through these commands and understand what is happening under the hood.
I will be installing the latest version of docker and docker-compose which at the time of this writing is:

```
[dev@localhost docker-getting-started]$ docker --version
Docker version 18.09.6, build 481bc77156
[dev@localhost docker-getting-started]$ docker-compose version
docker-compose version 1.24.0, build 0aa59064
docker-py version: 3.7.2
CPython version: 3.6.8
OpenSSL version: OpenSSL 1.1.0j  20 Nov 2018
```
Be sure to run the script with sudo: `sudo bash install-docker.sh`

Now that we have docker installed, we can start doing the fun stuff. The first thing I want to do is run our first container just to make sure everything is working ok. Run the following command: 
`sudo docker-run hello-world`
You should get the following output:
```
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.
```

We had to use `sudo` in order to run this docker command because the docker daemon runs as root and is bound to a Unix socket that is owned by root as well.
In order to get our user to run docker commands without having to `sudo`, we have to add our user to the docker group. Execute the following command:

`sudo usermod -aG docker $USER`

From here, you will have to logout and then log back in to your terminal in order to receive the new group assignment permissions. Let's now run `docker ps -a` to see if it worked. 

```
[dev@localhost ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
cd070d2203ee        hello-world         "/hello"            38 minutes ago      Exited (0) 38 minutes ago                       festive_swartz
```

Great! Now let's talk about this command and what happened we ran it. The `docker ps` command shows us a list of all the containers running on our host and the `-a` shows all
the containers including the ones that aren't running. From left to right we see:

- A Container ID that has been assigned to our container 
- We derived the container from the hello-world image 
- The command to run within our container is `/hello` 
- It tells us when the container is created
- The status of the container, in our case "Exited" because the hello-world container exits after it runs
- The ports that are exposed for the container
- A name that has been randomly assigned to us since we did not specify one

As the tutorial goes on, you will see more of this info and the commands and data will become more familiar to you. For now, since we have our docker client running and everything seems to be
working fine, let's get into the first topic of our lesson...

# Running Containers From Images




   


+++
author = "Jayson Ryter"
categories = ["Containers"]
tags = ["tutorial"]
date = "2019-07-05"
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
my configurations were easier to manage, and using it was easier than expected. So what is a container then? The best definition can be found right on the [docker website](https://www.docker.com/resources/what-container):

>Containers are an abstraction at the app layer that packages code and dependencies together. Multiple containers can run on the same machine and share the OS kernel with 
>other containers, each running as isolated processes in user space. Containers take up less space than VMs (container images are typically tens of MBs in size), 
>can handle more applications and require fewer VMs and Operating systems.
 
 By the end of this tutorial, you will be able to do the following: 

- Pull images from a repo and run containers
- Package an app into a container using a Dockerfile
- Publish a container to a registry

These three things were the building blocks that got me started on my path with Docker. There are a lot of Docker tutorials out there so I'm going to skip a lot of the fluff
and give you the info which I think is most relevant to get you started using Docker as quick as possible.

# Our Environment
For this lab I will be using the **Centos7** OS installed on **Virtualbox**. Everything we will be doing 
will be OS agnostic for maybe the exception of some commands that are translated differently if you are not using Centos as your OS. I will also be using a user with sudo privileges and not the root
account. I think it is important to realize what commands and actions you can execute without sudo and vice versa in regards to docker. 

**All files for this lab can be found in my public git repo:**
https://github.com/RyterINC/docker-getting-started

Let's make sure we have Docker installed first...

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

So, with your new found knowledge of docker you may have noticed we actually have already 
done this. we ran `docker run hello-world`, which pulled the image and created an instance of that image known as 
a docker container which runs as a process on your operating system. Let's try running an Nginx image from dockerhub with a couple of options:

`docker run -p 8080:8080 -d nginx:stable-alpine`

And here is the result:

```
[dev@localhost ~]$ docker run -d -p 8080:80 nginx:stable-alpine
Unable to find image 'nginx:stable-alpine' locally
stable-alpine: Pulling from library/nginx
e7c96db7181b: Pull complete
8985e402e050: Pull complete
Digest: sha256:270bea203d2fc3743fb9ce0193325e188b7e6233043487e3d3cf117ea4d3f337
Status: Downloaded newer image for nginx:stable-alpine
4bb1f473b1aa562ae6dd9f0f3e3ba0ae4ef6a386eb8edc56a845df2afd22e613
```

since this is the first time we have tried running a container from this image, docker once again has pulled the image for us from dockerhub and stored it on disk. Dissecting the command, 
we have added the `-p` option which allows a container to publish a port to the host. This is a bit confusing however, as the layout is host port, then container port. In our example we specify port 8080 on the host 
to connect to port 80 on the container. Since Nginx publishes content to port 80 which is an http port, we can forward that content to 8080 on the host and our Nginx page will be reachable.

![alt text](https://ryterincsitesimages.s3.amazonaws.com/thedevopsreport/docker-getting-started-imgs/nginx-port-example.PNG "ngxinx-port-example")

Since I am using a bridged NAT type for my virtualbox setup, I can view my container by connecting to my VM's IP address at the port we specified for Nginx to forward to and as you can see from the image above we were successfully able to reach our Nginx page that is running in docker. Cool stuff huh?

For the other option `-d`, it runs the container in detached mode which means the container runs in the background. If you didn't specify this option, all the container information would be written to the
terminal. This can be useful for debugging or better understanding of what's happening inside your container. 

The last new content we introduced was the image tag that we specified. For this image, we told the dockerhub that we wanted the image with the tag `stable-alpine`.If that image exists in the Nginx dockerhub repo, then 
it would be pulled by our docker daemon. If you want to see what images Nginx has available, you can visit their repo [here](https://hub.docker.com/_/nginx?tab=tags). I encourage you to find some other docker images of 
software you might use and install them via docker instead of having to do a full install and configure. Instead, you could run a container and take ten extra minutes for lunch. 

# Packaging A Container

So now that we have pulled a pre-built image and have gotten a small taste of docker, let's build our own image and see if we can host an app inside. In my humble opinion, packaging an app with a `Dockerfile` is the 
biggest barrier to entry for most people. Luckily for us, there are a ton of examples on the web on how to do this in case this isn't clear to you. We will be using a simple flask app in order to accomplish our task.

In our [docker-getting-started](https://github.com/RyterINC/docker-getting-started) repo I have included a script called `hello.py` (our flask app) and a `Dockerfile`. Let's go ahead and create a virtualenv
so we can run our app and see what it looks like. You can find instructions on how to install virtualenv [here]()

```
virtualenv .flaskenv 
source .env/bin/activate
```

After we create our environment and activate it, we can then install our dependencies. 

`pip install -r requirements.txt`

The only dependency we need to install is flask. From here, we can run our app with `python hello.py`

If you are having issues connecting to your app, ensure your firewall is either down or you punched a hole for port 5000. Since this is for development purposes, I just turned mine off. 

`sudo service firewalld stop`

If everything has gone well, you should be able to see our app running by accessing your VM's IP or hostname followed by port 5000. 

![alt text](https://ryterincsitesimages.s3.amazonaws.com/thedevopsreport/docker-getting-started-imgs/flask-app-docker-tutorial.PNG "flask-app-docker-tutorial")

Since the app works, let's take a look at the Dockerfile:

```
FROM python:3.7-alpine

COPY . /app
WORKDIR /app
RUN pip install -r requirements.txt`

ENTRYPOINT [ "python" ]
CMD [ "hello.py" ]
```

This is about as simple as they come. Let's go through this line by line so we really understand what's going on...

- `FROM python:3.7-alpine`

The `FROM` command sets the base image for the rest of the commands you will be executing. I've decided to use the python image, and the version is `3.7-alpine`. Alpine images are smaller OS's that only have the dependencies
you need. This can be useful if you are trying to reduce your image size or if you have a really small app and you don't want all of the overhead that a full OS comes with. 

- `COPY . /app`

We then run a `COPY` command that will take everything in the current directory and it's subfolders and put it into our `app` directory. This will create the directory for us if it doesn't exist (which it doesn't).

- `WORKDIR /app`

This line will set the working directory for subsequent commands. 

- `RUN pip install -r requirements.txt`

Then, we run a pip install on our requirements file to install dependencies in our working directory, which we set as `/app` in the last command. 

- `ENTRYPOINT [ "python" ]`

With `ENTRYPOINT`, we set the container as an executable and docker will start the application with the command you give it. in our case, it's `python`. 

- `CMD [ "hello.py" ]`

We will pass a default value of `hello.py` to `python`. This cen be overriden in the event you need to run another script, you can find more info about this on docker's site. 

### Time to build

`docker build -t docker-getting-started:latest .`

Here's what it looks like:

```
(.flaskenv) [dev@localhost docker-getting-started]$ docker build -t docker-getting-started:latest .
Sending build context to Docker daemon   12.3MB
Step 1/6 : FROM python:3.7-alpine
 ---> 2caaa0e9feab
Step 2/6 : COPY . /app
 ---> d5392902206a
Step 3/6 : WORKDIR /app
 ---> Running in e28b73325325
Removing intermediate container e28b73325325
 ---> f38633d98fae
Step 4/6 : RUN pip install -r requirements.txt
 ---> Running in 433e7e4c6f1d
Collecting Flask==1.0.2 (from -r requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/7f/e7/08578774ed4536d3242b14dacb4696386634607af824ea997202cd0edb4b/Flask-1.0.2-py2.py3-none-any.whl (91kB)
Collecting click>=5.1 (from Flask==1.0.2->-r requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/fa/37/45185cb5abbc30d7257104c434fe0b07e5a195a6847506c074527aa599ec/Click-7.0-py2.py3-none-any.whl (81kB)
Collecting Jinja2>=2.10 (from Flask==1.0.2->-r requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/1d/e7/fd8b501e7a6dfe492a433deb7b9d833d39ca74916fa8bc63dd1a4947a671/Jinja2-2.10.1-py2.py3-none-any.whl (124kB)
Collecting itsdangerous>=0.24 (from Flask==1.0.2->-r requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/76/ae/44b03b253d6fade317f32c24d100b3b35c2239807046a4c953c7b89fa49e/itsdangerous-1.1.0-py2.py3-none-any.whl
Collecting Werkzeug>=0.14 (from Flask==1.0.2->-r requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/9f/57/92a497e38161ce40606c27a86759c6b92dd34fcdb33f64171ec559257c02/Werkzeug-0.15.4-py2.py3-none-any.whl (327kB)
Collecting MarkupSafe>=0.23 (from Jinja2>=2.10->Flask==1.0.2->-r requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/b9/2e/64db92e53b86efccfaea71321f597fa2e1b2bd3853d8ce658568f7a13094/MarkupSafe-1.1.1.tar.gz
Building wheels for collected packages: MarkupSafe
  Building wheel for MarkupSafe (setup.py): started
  Building wheel for MarkupSafe (setup.py): finished with status 'done'
  Stored in directory: /root/.cache/pip/wheels/f2/aa/04/0edf07a1b8a5f5f1aed7580fffb69ce8972edc16a505916a77
Successfully built MarkupSafe
Installing collected packages: click, MarkupSafe, Jinja2, itsdangerous, Werkzeug, Flask
Successfully installed Flask-1.0.2 Jinja2-2.10.1 MarkupSafe-1.1.1 Werkzeug-0.15.4 click-7.0 itsdangerous-1.1.0
Removing intermediate container 433e7e4c6f1d
 ---> 26984e5524fc
Step 5/6 : ENTRYPOINT [ "python" ]
 ---> Running in 62bd7ec52738
Removing intermediate container 62bd7ec52738
 ---> dc608ac31ec1
Step 6/6 : CMD [ "hello.py" ]
 ---> Running in 0852ad5ccf7a
Removing intermediate container 0852ad5ccf7a
 ---> 8b6077690ec2
Successfully built 8b6077690ec2
```

That's great and all, but remember, just because an image builds doesn't mean the app inside is working. lets run an instance of this image and see if we can see our app. 

`docker run -p 5000:5000 -d --name flaskapp docker-getting-started:latest`

In a nutshell, we are executing a `docker run` to create a container from the image `docker-getting-started:latest` that we have included in our command at the end of the line. The `-p 5000:5000` 
means we port forward port 5000 on the container to port 5000 on the host and as before we use `-d` to run in detached mode. Last but not least we name our container `flaskapp` so we can reference it easily. 
Here's what it looks like: 

```
(.flaskenv) [dev@localhost docker-getting-started]$ docker run -p 5000:5000 -d --name flaskapp docker-getting-started:latest
fe94a278805b89adeba54b535105c555d9da512e0e2673995615a17512591cd6
(.flaskenv) [dev@localhost docker-getting-started]$ docker ps
CONTAINER ID        IMAGE                           COMMAND             CREATED             STATUS              PORTS                    NAMES
fe94a278805b        docker-getting-started:latest   "python hello.py"   3 seconds ago       Up 1 second         0.0.0.0:5000->5000/tcp   flaskapp
```

And just like that our app is up and running. If you go to the address you used before, you will be able to see the same `Hello, World!` and your app will be serving.


# Pushing the image

Now that we have a working image that can run a working container, let's push it to a repo

go to [docker hubs website](https://hub.docker.com/) and create an account. Once you do that, run `docker login` on your terminal and log in with your dockerhub creds. We then need to `docker tag` our image in order to associate it with our account. 

`docker tag docker-getting-started:latest ryterinc/docker-getting-started:latest`
"ryterinc" in this case is my dockerhub username, you would replace this with yours.

Then, we finally push our image to dockerhub:

`docker push ryterinc/docker-getting-started:latest`

```
(.flaskenv) [dev@localhost docker-getting-started]$ docker push ryterinc/docker-getting-started:latest
The push refers to repository [docker.io/ryterinc/docker-getting-started]
75e6227e3fb1: Pushed
d6b34dc333eb: Pushed
cd4e1fdc4397: Pushed
c3fe1900fecd: Pushed
ef486fa3cf07: Pushed
9e1a6a67b678: Pushed
256a7af3acb1: Pushed
latest: digest: sha256:4e57df65cd28ffaa69cf27d577f57e35f0cd279e647e73b58b4ea33775d3cce6 size: 1790
```

If you navigate to your docker hub account and go to the "Repositories" link, then you should be able to see your new image in docker hub. 

Let's run our container from the new image. After all, that was the whole point of all this work right? Run this command to remove all images from your docker daemon

`docker rmi $(docker images -a -q)`

Run a new instance of our image but from dockerhub:

`docker run -p 5000:5000 -d ryterinc/docker-getting-started:latest`

Here's the result:
```
(.flaskenv) [dev@localhost docker-getting-started]$ docker run -p 5000:5000 -d ryterinc/docker-getting-started:latest
Unable to find image 'ryterinc/docker-getting-started:latest' locally
latest: Pulling from ryterinc/docker-getting-started
921b31ab772b: Pull complete
1a0c422ed526: Pull complete
ec0818a7bbe4: Pull complete
b53197ee35ff: Pull complete
8b25717b4dbf: Pull complete
bbb31df7bb5d: Pull complete
d1afcc1eef84: Pull complete
Digest: sha256:4e57df65cd28ffaa69cf27d577f57e35f0cd279e647e73b58b4ea33775d3cce6
Status: Downloaded newer image for ryterinc/docker-getting-started:latest
019b66c8bd7295c983f52aa7dcbc99cf9898d3877915b9b65f9e2fb3902c326e
(.flaskenv) [dev@localhost docker-getting-started]$ docker ps
CONTAINER ID        IMAGE                                    COMMAND             CREATED             STATUS              PORTS                    NAMES
019b66c8bd72        ryterinc/docker-getting-started:latest   "python hello.py"   17 seconds ago      Up 16 seconds       0.0.0.0:5000->5000/tcp   boring_wing
```

# In Conclusion

In this lesson, we were able to run a docker container from docker hub, package our own app, run that app in a container, and publish that container to dockerhub to use it ourselves. Docker has allowed us to 
package an application and its dependencies into an easy to run process that is able to run in one command. This was a pretty quick lesson and it only skims the surface of docker. I encourage you to try and package more complex images, as well as research any concepts you didn't quite understand. Go forth and package all the things!





   


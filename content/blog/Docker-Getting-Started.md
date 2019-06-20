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
Docker is a pretty mysterious thing to a lot of people these days and to be honest, it went right over my head the first time I heard about it. 
The idea of a container seemed not so special to me until I started to use it and understand the power this simple tool had to offer. My deployments got faster, 
my configurations were easier to manage, and using it was easier than expected. By the end of this tutorial, you will be able to do the following: 

- Pull images from a repo and run containers
- Package an app into a container using a Dockerfile and publish to a registry
- Use docker-compose to spin up multiple containers at once

These three things were the building blocks that got me started on my path with Docker. There are a lot of Docker tutorials out there so I'm going to skip a lot of the fluff
and give you the info which I think is most relevant to get you started using Docker as quick as possible. Everything we do will also be in my public github located here

# Let's Begin

Because we are leaving out the fluff, I will assume you already have docker installed. For this lab I will be using the Centos7 OS installed on Virtualbox. Everything we will be doing 
will be OS agnostic for maybe the exception of some commands that are translated differently if you are not using Centos as your OS as well. 

Let's make sure we have Docker installed first...

   


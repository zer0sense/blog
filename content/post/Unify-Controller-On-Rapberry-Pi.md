---
title: "Unify Controller on Rapberry Pi with Docker"
date: 2020-08-10T21:39:22+09:00
Description: "Running Unify Controller on Raspberry Pi with Docker"
Tags: [Raspbery Pi,Docker,Unifi]
Categories: [Projects,Tutorials]
DisableComments: true
draft: False
---
Docker has intrigued me since I first found out about it. The ability to run an application and dependencies without having to use a full operating system for every application. A lot of VM stacks will have a single application on a VM, but also have to install a full operating system __for every VM__. That is a lot of overhead in my opinion.

The mobility of being able to migrate a container from one host to another seems pretty amazing. I have one raspberry pi that I am using, so I haven't experience this first hand. From what I understand, its amazingly efficient and easy.

That got me thinking, how can I get started with docker? I bought a raspberry pi 4 to run my WIFI controller for my unify wireless access points. Is it possible to run the controller through docker? The short answer is yes, and it was a great learning experience. I have two wireless access points, so its not like I am running an enterprise full of hundreds of access points. Lets try it, and if it doesn't work out I can always just run it on the pi directly.

## Installing Docker

First thing first! Lets install docker on the raspberry pi. I am currently using Raspberry Pi OS (formally Raspbian) which is a Debian based linux distribution. If you are familiar with Ubuntu, or perhaps Linux Mint, this guide should be easy enough to follow. 

The docs on docker's [website](https://docs.docker.com/engine/install/ubuntu/) has installation guide to use depending on your operation system if you want to follow their guide for other distributions. 

I will be using the convenience script from the docker docs.

First use curl to download the script.
```
curl -fsSL https://get.docker.com -o get-docker.sh
```
Then run the script.
```
sudo sh get-docker.sh
```

If you want to have docker run on startup, I suggest using system control to have it start automatically.
```
sudo systemctl enable docker
sudo systemctl start docker
sudo systemctl status docker
```
You should see that it is active (running)

### Pulling and Creating the Unify Container

When I was researching if this was possible, I came across this [reddit](https://www.reddit.com/r/Ubiquiti/comments/7po0jr/installing_unifi_controller_on_ubuntu_with_docker/) post that had all the information to get this up and running on Ubuntu. With a few small alterations we can get this running on our raspberry pi.

The installation guide goes through the recommendation to create a user and group just for docker. Why? To prevent your container from running as root. 

I will start by creating the group docks
```
sudo adduser docks
```
You will want to take note of the UID/GUID that is created when you complete this.
{{< figure src="/images/Posts/001/uid.jpg" >}}

#### Creating Persistent Data for the Controller

One lingering question I had when looking into docker was what happens if the container is destroyed? Does all the data go with it? This might not be a huge concern for something as small as a Unify controller, but there is a lot of configuration that I don't want to have to re-accomplish if something happened to the software.

Thankfully you can keep your configuration persistent. This is great news! I can upgrade and possibly break a container with less risk. Lets set that up next.

First we will create the folder, and you will want this owned by the user and group you created earlier.
```
sudo mkdir -p /var/docks/unifi
sudo chown docks:docks /var/docks/unifi
```

#### Pulling and Creating Your Unify Container

You can find multiple options of containers on [Docker Hub](https://hub.docker.com/), but for this we will be using Jacob Alberty's [container](https://hub.docker.com/r/jacobalberty/unifi/). I have been using it for months, and it is updated frequently. Once you get to the docker hub page you will need to pay attention to which container you are trying to pull from docker. I make it a habit to check the Tags tab if there is one. Make sure you are pulling the container that is compatible with arm. If you try to pull the Amd64 container it will not work. 

With that being said lets get the version that is most up to date when you are reading this, but for now it is.
```
sudo docker pull jacobalberty/unifi:arm32v7
```
After the download we will create the container. 
```
sudo docker run -d \
    --restart=unless-stopped \
    --net=host \
    --name=unifi \
    -e TZ='America/New_York' \
    -e RUNAS_UID0=false \
    -e UNIFI_UID=1001 \
    -e UNIFI_GID=1001 \
    -v /var/docks/unifi:/unifi \
    jacobalberty/unifi:arm32v7
```
To check on the startup process you will want to run the command
```
sudo docker ps
```
You will want to look at the status and wait for it to say healthy
{{< figure src="/images/Posts/001/dockerps.jpg" >}}

Now you can log in to your unify controller on your raspbery pi's ip on port 8443, and start configuring everything. 

I hope you enjoyed this walk through. It was a fun project for me to get started with docker, and I hope you found it enjoyable as well. 
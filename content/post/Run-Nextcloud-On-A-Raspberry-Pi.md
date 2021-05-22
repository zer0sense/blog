---
title: "Run Nextcloud on a Raspberry Pi"
date: 2021-05-22T10:35:49+09:00
Description: "How to Setup NextCloud on Your Raspberry Pi"
tags: ["Docker,Raspberry Pi,Linux,NextCloud"]
categories: [Tutorials,Linux]
comments: true
draft: false
---
Nextcloud hit my radar of interesting technology to try quite a long time ago. This collaboration tool is free and open source, and gives a decent point to store your contacts and photos. There are an incredible amount of apps that really expand the capability of Nextcould. Mail,calendar,a chat client just to name a couple.

There are plenty of cloud services that allow you to back up your precious photos like Dropbox,Onedrive,Apple,and Google. There is nothing wrong with having these as one point of backup, but what does irritated me is that they can change their terms and conditions at any time. If they raise their prices, or impose restrictions on upload size. I wanted to add an additional point of backup for my data.

The setup I am using for this is a Raspberry Pi 8GB and an external USB 3 hard drive. This is not very scalable, but for a couple of users I don't see why this cant be an additional point to backup your data. We will also be using docker-compose to pull the docker containers and build them. This will also have persistent data on the hard drive, so all the important data will stay no matter what happens to the containers.

## Docker-Compose File
The /pathtoyourpc portion of this dockerfile is where you want your data to be saved so that it is persistent. 
```
# Nextcloud and MariaDB
version: '2'

services:
  db:
    image: mariadb
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW
    restart: always
    volumes:
       - /PATHTOYOURPC/db:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=NextCloud
      - MYSQL_PASSWORD=nextcloud
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud

  app:
    image: nextcloud
    ports:
      - 38080:80
    links:
      - db
    volumes:
      - /PATHTOYOURPC/config:/var/www/html/config/
      - /PATHTOYOURPC/html:/var/www/html
    environment:
      - MYSQL_PASSWORD=nextcloud
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_HOST=db

    restart: always
```
Lets take a look at some of this docker file and break it down.
```
volumes:
  - /PATHTOYOURPC/db:/var/lib/mysql  <<<< where your database will be saved here
```
```
volumes:
      - /PATHTOYOURPC/config:/var/www/html/config/ <<<your Nextcloud config will be saved locally to your PC. 
      - /PATHTOYOURPC/html:/var/www/html <<< The Nextcloud webserver 
```
The config being saved on your local PC will save you a ton of time. For Nextcloud you might have to edit the php files to [add trusted domains](https://help.nextcloud.com/t/howto-add-a-new-trusted-domain/26) if you have troubles accessing your Nextcloud. 

I have multiple docker instances that are trying to access port 80, so I have set this up to listen on port 38080. This is the port that you type in to access your Nextcloud container.
```
 ports:
 - Listening Port>>> -38080:80
```

## Pulling and Building the Container
You want to put the docker-compose file into a directory. For ease of keeping it simple I create a file named docker-compose.yml. 

To pull the docker containers make sure you're in the same directory of the docker-compose.yml file and use the following.
```
sudo docker-compose pull
```
This will look at the docker file and pull the Nextcloud container and MariaDB container.

Once the containers have been downloaded to start the containers.
```
sudo docker-compose up --build -d
```
You will see when its done when the task has both names of the containers saying ok. 

Now type the IP:Port to access your Nexcloud instance. You will be walked through creating an username and password. To sync your phone data, there are apps in your phones app store to select what gets backed up and sync'd.

I wouldn't solely rely on this self-hosted solution as my sole way of backing up my data, but it does offer a local solution for syncing that I control the terms and conditions.
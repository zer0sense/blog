---
title: "MediaWiki on Docker"
date: 2021-01-19T20:21:55+09:00
Description: "Running MediaWiki with Docker"
Tags: [Docker,Raspberry Pi,YAML]
Categories: [Tutorials]
Comments: false
draft: false
---

I consume a lot of information about technology, and I am horrible about taking notes to reference later. This blog is a public manifestation of that. I want to share the information I find interesting, and it consolidates a place of reference for me as well. I came across MediaWiki, and thought that it would be a great tool for me to self-host and have for all the information I don't find blog worthy. 

This was the trickiest docker container I got running. The easiest solution was to get started learning some of the simplicity and power of docker-compose. Docker-compose elaborates more of the docker run command with the flags that you would use. I have barely scratched the surface of the capabilities of docker-compose, but for this docker image it made things immensely easier to get running and troubleshoot.

## Setting Up The Docker-Compose File

After numerous tries of getting everything to work my own way I looked into the example on the [Media Wiki Docker Hub](https://hub.docker.com/_/mediawiki) and made some very small changes. I created a YAML file for the output below called stack.yml (original I know). This will be important later in the blog post.

```
# MediaWiki with MariaDB
#
# Access via "http://localhost:8080"
#   (or "http://$(docker-machine ip):8080" if using docker-machine)
version: '3'
services:
  mediawiki:
    image: mediawiki
    restart: always
    ports:
      - 28080:80
    links:
      - database
    volumes:
      - /var/www/html/images
      # After initial setup, download LocalSettings.php to the same directory as
      # this yaml and uncomment the following line and use compose to restart
      # the mediawiki service
      # ./LocalSettings.php:/var/www/html/LocalSettings.php
  database:
    image: mariadb
    restart: always
    volumes:
        - /PATHONYOURPC:/var/lib/mysql
    environment:
      # @see https://phabricator.wikimedia.org/source/mediawiki/browse/master/includes/DefaultSettings.php
      MYSQL_DATABASE: my_wiki
      MYSQL_USER: wikiuser
      MYSQL_PASSWORD: example
      MYSQL_RANDOM_ROOT_PASSWORD: 'yes'
```

Some things you want to note is that the MediaWiki image has a different port number. The reason for this is I run multiple containers off the same raspberry pi. Port 8080 is pretty popular, and was already taken. 

The database image is MariaDB image. I added the volume parameter, so that the data I create is persistent. The docker image can be torn down or updated without me worrying about me losing everything after putting work into creating a wiki.

## Getting MediaWiki Started

Now we are ready to get this container up and going. Go into the directory that has the YAML file you saved earlier and type the following: (Remember to use whatever you called your YAML file)

```
sudo docker-compose -f stack.yml up -d
```
You will get the downloading and extracting output in your terminal for a little while. Once its complete you should get a message saying the database and mediawiki are done. Great! Thats it right. No....No sadly it is not. 

Fire up your browser of choice and go to the webpage for your new wiki _ipaddress:2808__(If you followed the example above). You should see a splash page saying LocalSettings.php was not found and please set up the wiki first. The first couple of clicks are simple enough; chose your language and wiki language. On the Welcome To MediaWiki page you want to make sure The enviroment has been checked. You can install MediaWiki in green text, and then hit continue.

The Connect to database page is what threw me for a loop for a long, long, long time. 

{{<figure src="/images/Posts/006/connect_to_database.jpg">}}


Choose MariaDB because that is what we are running as the database

The Database Host is what kept me from getting the wiki up and running. Since you are running it in a docker container localhost will not work. The network address for localhost (127.0.0.1) wont work either. You will need the IP Address for the database. The way I did this was to log into the database container

```
sudo docker exec -it mediawiki_database_1 /bin/bash
```

Running that command should get you a root shell on your docker container. To get the IP:

```
ip a
```
Do not use the lo IP address because that is the loopback address for that container. You should see an address with eth0. That is the IP that you will need to put in the database host box.

Now you will get the database name from the stack.yml address from earlier:

```
MYSQL_DATABASE: my_wiki
```
The last boxes are also in the stack.yml:

```
MYSQL_USER: wikiuser
MYSQL_PASSWORD: example
```
You should get a check mark and a link to start using your wiki, so lets go there. All the information here is totally up to the user. Once you have completed your setup it should prompt you to save your LocalSettings.php. Save this or you will have to set up the wiki portion again. Since I ssh'd into my raspberry pi I used scp to copy it over to my server and moved it with the stack.yml file I made earlier, so I can keep everything together. 

The last part is to edit your stack.yml (or whatever you named it) to use your LocalSettings.php 

```
# MediaWiki with MariaDB
#
# Access via "http://localhost:8080"
#   (or "http://$(docker-machine ip):8080" if using docker-machine)
version: '3'
services:
  mediawiki:
    image: mediawiki
    restart: always
    ports:
      - 28080:80
    links:
      - database
    volumes:
      - /var/www/html/images
      # After initial setup, download LocalSettings.php to the same directory as
      # this yaml and uncomment the following line and use compose to restart
      # the mediawiki service
      - ./LocalSettings.php:/var/www/html/LocalSettings.php
  database:
    image: mariadb
    restart: always
    volumes:
        - /PATHONYOURPC:/var/lib/mysql
    environment:
      # @see https://phabricator.wikimedia.org/source/mediawiki/browse/master/includes/DefaultSettings.php
      MYSQL_DATABASE: my_wiki
      MYSQL_USER: wikiuser
      MYSQL_PASSWORD: example
      MYSQL_RANDOM_ROOT_PASSWORD: 'yes'
```
The line you want to edit is under volumes in the mediawiki portion. You want to un-comment it and ad a -

If you ever have to start your docker images again it will use that LocalSettings.php. One thing to note is you want to use the docker-compose command where the LocalSettings.php and stack.yml files exist.

I hope you enjoyed this blog post. It took me way to long to figure out why I couldn't get the database going.
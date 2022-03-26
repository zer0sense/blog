---
title: "Metasploit With Docker"
date: 2022-03-26T23:18:41+09:00
description: "Run Metasploit with Docker"
tags: [Docker,CTF,Linux,Metasploit]
categories: [Pentesting,Tutorial,Ethical Hacking]
comments: false
draft: false
---

I have a goal to be able to choose my own Linux distro and be able to use common pentesting tools. One of the most well known pentesting tools is Metasploit. This whole framework has such a variety of tools and exploits built in and is updated regularly. This, for me, is a must have no matter what OFFSEC thinks. I do understand that there are other difficulties that can be introduced by running docker, but I think we can overcome those and realize then benefits. What benefits? I am glad you asked. Running docker images includes all dependencies required to run the binary. I am a huge fan of docker for this. Opinions aside lets get this installed and running. You can see if this is a solution for you.

### Installation

Rapid 7 has a github that includes the docker files included. The first thing we want to do is clone that to our machine. I will be cloning it to my /opt directory.
```bash
git clone https://github.com/rapid7/metasploit-framework
```

Now we will change into that directory and make some changes. I don't make a habit of switching to the root user to run anything, but this was the only way I was able to get these text files to work. To do this I typed ```su root```. If you are unable to switch because you dont know the password you can change it with ```sudo passwd root```. I found this through the metasploit repo and was able to test out a [Tryhackme](https://tryhackme.com) room I recently did to test a meterpreter shell successfully. We are going to create a file for docker first. 

We will create this file as root with ```touch docker-compose.local.override.yml```. Open with your favorite text editor and paste in the following.

```yaml
version: '3'
services:
  ms:
    environment:
      # example of setting LHOST
      LHOST: 192.168.1.5
    # example of adding more ports
    ports:
      - 8080:8080
```

Please pay attention to the __LHOST__ portion of this. I set this to my physical machine's IP address. When trying the exploit with metasploit earlier without this enviroment variable set I was not able to get a meterpreter session to connect back to me. 

While still root we are going to do a little docker magic to get the container to see this information.

```bash
echo "COMPOSE_FILE=./docker-compose.yml:./docker-compose.override.yml:./docker-compose.local.override.yml" >> .env
```

You can leave your root shell now by typing exit.

### Using Metasploit

While you are in the metasploit-framework dirctory you can start metasploit with ```./docker/bin/msfconsole```. You will see it buidling the container and you will get your regular metasploit console ```msf6>```. If you get permission issues you may need to run docker commands with sudo if you are not in the docker group on your host machine. This will also change your path for msfvenom as well ```./docker/bin/msfvenom```.

One way to make using this docker file a little more user friendly and type less is to set up aliases. I definitely dont want to type in the full path for running metasploit. You can do this by adding this to your $PATH, but I chose to set an alias for these. To set a permanent alias you will want to add it to your ```.bashrc``` or ```.zshrc``` file. I have a little different setup where I have an alias file that will work with bash or zsh. That is a whole different article for another day.

To add the alias edit your file and edit it to where you have your metasploit-framework folder that you cloned earlier.
```bash
alias msfconsole='sudo /opt/metasploit-framework/docker/bin/msfconsole'
alias msfvenom='sudo /opt/metasploit-framework/docker/bin/msfvenom'
```
Save your changes and you can reload your shell by typing ```bash``` or ```zsh``` depending on what shell you are using. Now by typing ```msfconsole``` or ```msfvenom``` it will point to the docker image and save you some keystrokes. If it is not working correctly check your spelling on your command and your path.

I hope you find this article enjoyable and give a look at docker to simplify more of your workflow.
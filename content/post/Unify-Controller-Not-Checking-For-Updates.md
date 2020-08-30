---
title: "Unify Controller Not Checking for Updates"
date: 2020-08-23T08:22:22+09:00
Description: ""
Tags: [Raspberry Pi,Docker,Unifi]
Categories: [Troubleshooting]
DisableComments: true
draft: False
---
{{<figure src="/images/Posts/002/updatefail.jpg">}}
I have ran into an issue where the update check has failed on my unifi controller. This has been after a container upgrade, or when I have restored from a backup. It didnt send a notification saying that it failed, but I was going through the controller setting and found out it failed. This took some time and tinkering to figure out how to get this up and running. A lot of remedies I found online was to just reinstall your os/distro. This didnt seem viable due to the fact I would either have to reconfigure everything on the fresh install, or restore from backup and roll my 20 sided dice hoping it would work.

However, I found a [reply](https://community.ui.com/questions/Firmware-files-are-not-loaded-after-Controller-upgrade-to-5-10-19/52c63313-f302-45e6-b6eb-f61daa737c0c) to a post on the Unifi commuinity form that got me going in the right direction. Removing and updating the ssl certs that java uses for the update check should fix it. How though? This is a docker container and I need access to that container's operating system. A few momemts later, I came across the command of docker exec. This command does exactly what I needed. You have access to the container's operating system.

### Using Docker Exec

You will need some grab the name of your controller's docker container.
{{<figure src="/images/Posts/002/dockername.jpg">}}
You will see in the example that the name of mine is unifi. Next you will use docker exec to log into the container.
```
sudo docker exec -it unifi /bin/bash
```
You're in! Now lets navigate to the folder where the ssl certs are store on this container.
```
cd /etc/ssl/certs/java/
```
Next we will remove the cert that is stored in that folder.
```
rm cacerts
```
To update the certs stored you will use:
```
/usr/sbin/update-ca-certificates -f
```
Once you that was successfull, you will type exit to log out of the docker container.

Now that we have updated the new certs, we will reboot the container. You do not have to restart the raspberry pi if you do not want to. 
```
sudo docker restart unifi
```
If you are overly paranoid, like I am, and want to make sure you didnt just trash your container you can check the status of your docker container until it says healthy.
```
sudo docker ps
```
{{<figure src="/images/Posts/002/dockerreboot.jpg">}}
Once you see that it says healthy you should be able to log back in, and confirm your controller is able to check for updates. 
{{<figure src="/images/Posts/002/finalgui.jpg">}}
I hope this troubleshooting walk through has been helpful. 
---
title: "Nahamcon2022 Prisoner"
date: 2022-05-01T09:05:47+09:00
description: "Nahamcon2022 CTF Prisoner Challenge"
tags: [Nahamcon2022,CTF,Python]
categories: [CTF,Ethical Hacking,Pentesting]
comments: false
draft: false
---
This Nahamcon2022 easy challenge was fun to figure out. When you log into the challenge box you are greeted with {{<figure src="/images/Posts/013/breakout.jpg">}}

My first instinct was to do Control + C. That did not work....Not at all. Alright, lets try typing quit...nope...Lets try exit..Not going to be that easy. Lets try another keyboard shortcut Control + D. BAM!!! It was a python script that was locking you in. Now that you are in a python shell on the system what can you do? Lets see if the CTF creators allow us to spawn a shell by using the python command we usually use when we get a reverse shell ```pty;pty.spawn("/bin/bash")```. That gives you a bash shell on the machine. List the directory with ```ls``` and get the flag thats right there in the directory for you to cat out. 

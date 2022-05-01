---
title: "Nahamcon2022 Steam Locomotive"
date: 2022-05-01T22:18:40+09:00
description: "Nahamcon 2022 Steam Locomotive"
tags: [Nahamcon2022,CTF,SSH]
categories: [CTF,Ethical Hacking,Pentesting]
comments: false
draft: True
---

This box is very short and sweet. It made me smile figuring it out. When you SSH into the box you have absolutely no control to do anything, but it does bring this great art running through your terminal.

{{<figure src="/images/Posts/015/steamlocomotive.jpg">}}

What I did to get around this is to use SSH to run commands on the server I am connecting to. The way I did this was by running ```ssh username@password 'command'```. I tried it out using the ls command first then using cat to get the flag. A short,sweet,and fun box.
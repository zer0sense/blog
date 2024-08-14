---
title: "THM Pickle Rick"
date: 2021-06-30T19:22:56+09:00
description: "Try Hack Me - Pickle Rick"
tags: [THM,CTF]
categories: [Ethical Hacking,CTF,TryHackMe,Linux,Pentesting]
comments: false
draft: false
---
I have been going through the TCM Security Academy [Practical Ethical Hacking Course](https://academy.tcm-sec.com/p/practical-ethical-hacking-the-complete-course) and I wanted to take a break from videos, and try a [Try Hack Me](https://tryhackme.eu) room. 

I would love to say that I searched around to find a worthy opponent, but the truth is I sorted by easy Linux boxes. I then clicked on the first one on the list, Pickle Rick. I did enjoy the Rick and Morty episode, so why not. 

## The Scans ##

The first thing I did after acquiring the IP address is firing off an nmap scan (Do note the IP in this scan is probably different than what you have.)
```
nmap -A -T 4 -p- -v -oA nmap/picklerick 10.10.175.24
```
I know what you're thinking. What...Why...This isn't the way to scan you must be using the wrong flags. 

Well let me take this opportunity to let you know why. Most scans that I have been privy to see do -sC and -sV and -O. What are you doing rookie. That's is where the first lesson in the course I'm taking comes into play. I can cover all of that with -A. Here's a snippet from nmap --help
> -A: Enable OS detection (-O), version detection(-sV), script scanning(-sC), and traceroute

I put the equivalent flag next to the description to help relate the substitution.

Now what is the -oA nmap/picklerick? I found this while watching Ippsec's videos on [Youtube](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA) That is a very convenient way to have nmap generate an output in an nmap directory I have made. You can also have something similar to this using the tee command.

```
nmap -A -T 4 -p- -v 10.10.175.24 | tee nmap.txt
```
That will put the output into a text file in the directory you are in.

Great! What results did you get. I'm glad you asked.
```
Nmap 7.91 scan initiated Wed Jun 30 03:34:05 2021 as: nmap -A -T 4 -p- -v -oA nmap/picklerick 10.10.175.241  
Nmap scan report for 10.10.175.241  
Host is up (0.27s latency).  
Not shown: 65533 closed ports  
PORT   STATE SERVICE VERSION  
__22__/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)  
| ssh-hostkey: 
|   2048 de:76:0c:b5:f2:44:62:ef:7a:a3:a7:02:b2:68:c0:c7 (RSA)  
|   256 55:53:6a:d9:8c:2e:54:63:81:a6:36:59:03:87:51:63 (ECDSA)  
|_  256 e9:18:a3:05:1c:c9:01:d0:dd:ab:92:ce:7a:63:52:a2 (ED25519)  
__80__/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))  
| http-methods: 
|_  Supported Methods: OPTIONS GET HEAD POST  
|_http-server-header: Apache/2.4.18 (Ubuntu)  
|_http-title: Rick is sup4r cool  
>Service Info: OS: Linux; CPE: cpe:/o:linux:linux kernel  
Read data files from: /usr/bin/../share/nmap  
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done at Wed Jun 30 03:48:36 2021 -- 1 IP address (1 host up) scanned in 871.17 seconds  
```

Alright I can see that I have SSH open and http open. Choices choices. I'm going to start with http and kick off a gobuster scan.
```
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x php,sh,txt,cgi,html,js,css,py -u 10.10.175.241
```
Yes. I used the small list. Why? Start small, right?

## Enumerating/Exploring ##

While I am waiting for my blazing fast gobuster scan to run I figured I could at least take a look around and see if I can find something in the meantime. I navigated to the websites homepage and viewed the page source. Everything was looking normal, but then I saw the note to self in the html comment. It had a username.
```
R1ckRul3s
```
Wonderful. How else can I get some more information about this website. I know! I went to look at the robots.txt and found the helpful hint of
```
Wubbalubbadubdub
```
I went back to look at my gobuster and saw that it picked up a /index.html and a /login.php and /assets. Alright lets take a look.

The /index.html is the main website, and I've already seen that. Lets try the /login.php. I mean we do have the username from before, and a random robots.txt. I find myself at the login page and put the R1ckRul3s in for the username and the Wubbalubbadubdub as the password.

Well...I wasn't expecting to get in, but I'm in. I'm greeted with this wonderful website that has a very tasty Command Panel. 

{{<figure src="/images/Posts/008/command.jpg">}}

This is exciting. Before I go through the obvious command panel, I'm going to poke around the different tabs at the top. Well that didn't give me anything. 

Lets play with the command panel. I start off with an id command. 
```
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```
Alright I'm www-data. now what can we do. Let's try ```ls```. 

>Sup3rS3cretPickl3Ingred.txt  
>assets  
>clue.txt  
>denied.php  
>index.html  
>login.php  
>portal.php  
>robots.txt  

Alright, let's cat that out and get a clue.
```Command disabled to make it hard for future PICKLEEEE RICCCKKKK.```

Just when you thought it was going to be a quick night. Alright lets try the less command on that clue.txt ```less clue.txt```. We got something. It's not much but its something.

>Look around the file system for the other ingredient.

Let's hope we can look around a bit without commands being disabled. Lets look at the root directory
```
ls /
```
It does give us the linux file system. Great! Lets look for at the /home directory.
```
ls /home
```
We have two users rick and ubuntu. Lets try rick first.
```
ls /home/rick
```
We have something called second ingredients. Is it a directory or file. I'm going to try to use the less command again to see if its a file and if not; I will ls it to see the contents.
```
less /home/rick/"second ingredients"
```
It was a file and a flag! take the output and put it in the right flag section. Also, add an hour to your room if you need to. 

This was the second ingredient though. What was the first one? Lets look around and find out. Well if you are smarter than me and remembered the first time we did the ls command there was 

>Sup3rS3cretPickl3Ingred.txt

Yes...That..That was the first ingredient. 
```
less Sup3rS3cretPickl3Ingred.txt
```
Now lets find the final flag. I am trying to find it the best way I know how. I try frantically using ls -la to find hidden files. I look in both home directories. I throw a hail mary and 
```
ls -la /root
```
Nope! Got nothing but disappointment. Let's go back and see what we can do. Obviously, I cant ls the root folder as www-data. Let's see what we can do as sudo.
```
sudo -l
```
```
Matching Defaults entries for www-data on ip-10-10-175-241.eu-west-1.compute.internal:  
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin  

>User www-data may run the following commands on ip-10-10-175-241.eu-west-1.compute.internal:
>    (ALL) NOPASSWD: ALL

```
I can do all commands with NO PASSWORD!!! Yup let's try to get a directory listing now.
```
sudo ls -al /root
```
There is a 3rd.txt. Let's see if we can get an output for that.
```
sudo less /root/3rd.txt
```
That's it! We got the last flag.

Even if there were no amazing reverse shells or custom python scripts I hope you enjoyed this write up. I will continue to put out more of these rooms in the future. 
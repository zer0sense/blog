---
title: "Nahamcon2022 EXtravagant"
date: 2022-05-01T10:30:06+09:00
description: "Nahamcon2022 "
tags: [Nahamcon2022,CTF,XML]
categories: [CTF,Ethical Hacking,Pentesting]
comments: false
draft: false
---
When looking at some web challenges for Nahamcon 2022 I saw EXtravagant that mentioned XML parsing. I recently went over some XML content on tryhackme that I thought would help, and it did. Once you fire up the box and navigate to the website you can click around on the website to see what is available. Well when clicking on trial there is a nice surprize.

{{<figure src="/images/Posts/014/uploadxml.jpg">}}

The room gave a hint of where the root flag was. Putting together XML with an upload and we have the directory location lets try some XXE. XXE can use xml to read files on the system. Lets craft an xml file to upload and see what we can do from there. 

```xml
<?xml version="1.0"?>
<!DOCTYPE root [<!ENTITY read SYSTEM 'file:///var/www/flag.txt'>]>
<root>&read;</root>
```

Once its saved upload it to the website it will tell you upload successful. Now we have to find a way to get the server to read that file we uploaded. Thankfully for us the CTF has a View XML tab where you type the name of the XML file you want ot view.

{{<figure src="/images/Posts/014/viewxml.jpg">}}

Type the name of the XML file that you uploaded earlier and Bob's you uncle you have a flag.

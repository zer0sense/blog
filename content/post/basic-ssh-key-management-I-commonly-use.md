---
title: "Basic SSH Key Management I Commonly Use"
date: 2020-08-29T14:47:23+09:00
Description: "Basic SSH Key Management I Commonly Use"
Tags: [SSH]
Categories: [Tutorials]
DisableComments: true
draft: true
---
I have only scratched the surface when it comes to the power and convenience of SSH. I am not managing a slew of servers that I have SSH connections for, but it has become more and more an integral part of tech routine. Using github and gitlab, logging into my raspberry pi at home, and an easy way to go from my Linux laptop to desktop has made SSH a very valuable tool. 

During my daily YouTube content intake, I came across a video from one of the channels I subscribe to, [Lawrence Systems](https://www.youtube.com/channel/UCHkYOD-3fZbuGhwsADBd9ZQ), put out a few videos on SSH. After watching these video, the veil was lifted on the potential of how to use more and more features of SSH. 

### Generating SSH Keys

I will be using ED25519 to generate my keys in this blog post. Tom, the host of the YouTube video, goes into detail and leaves reference links for using this to generate the key, and it easier to follow along when you use the same methodology. 
Lets start the key generation:
```
ssh-keygen -a 100 -t ed25519 -f ~/.ssh/pi_ed25519 -C "Kalifornia909@pi"
```

We can break this command down a little to explain some of the options
> -a 100
This option is the math randomness while its being generated. 
> -t ed25519
The -t is to choose what you are going to use to generate your key.
> -f ~/.ssh/pi_ed25519
This is where you will save your SSH key and it also gives you the option of choose the filename if you want to change it. You can choose to leave this as the default if you want, but I generate a new key for the different connections I use. Overkill, possibly, but it also gives me options to grow and fail. One thing I learned the hard way is to type out the full path of the ~./ssh/. You have to possibility of throwing ssh keys around everywhere and not knowing where they were generated. Then what happens, you generate it again. Only to have the same result. Yes....yes....I have done this.

> -C "Kalifornia909@pi"
The -C is for comment. I believe its common practice to put an email address, but I keep this kind of random. 

You should receive an output similar to this:
{{<figure src="/images/Posts/003/sshkeygen.jpg">}}

Most of this is self explanatory, and the only thing you will be asked for is your password. You can choose to use a password with this or not, but I do add passwords to my SSH keys. 

Now you can verify that the files are there.
```
ls ~/.ssh
```
You should see at least two files that look familiar. In this example I have pi_ed25519 and pi_ed25519.pub. The file name that is the .pub is the public key. This key can be utilized everywhere without putting the key pair at risk. This is what you will use for site like github and gitlab. Anyone can have this __.pub__ file. You can use whatever you like to see whats in it, but I keep it simple.

```
cat ~/.ssh/pi_ed25519.pub
```

The file that does __not__ end in .pub is your private key, and its meant to be private to that computer. If you cat the wrong file you will get an output that beings with:
> -----BEGIN OPENSSH PRIVATE KEY-----
This tells you right away that it is your private key. Do not give this to anyone.


---
If you would like a more in-depth explanation, and some great tech videos I have posted the videos that inspired this blog post

#### SSH Keygen Video
{{<youtube vINn1MIrf7o>}}


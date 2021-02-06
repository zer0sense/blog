---
title: "Make Any Linux Distro Yours With Ansible"
date: 2021-02-06T11:39:29+09:00
Description: "Using Ansible Pull to Install Programs and configurations"
Tags: [Ansible,YAML]
Categories: [Tutorial,Project,Linux]
Comments: false
draft: false
---

During my consumption of tech YouTube content I came across a video talking about Ansible Pull. I am familiar with Ansible, so I queue it up. As soon as I finished the video this project came to mind.

I know a lot of linux desktop users use git to store their rc files and config files. There is nothing wrong with doing this, but I thought of doing this using a small amount of power from Ansible. The goal of this is to pull a base deployment of programs I like and pull my personal config files and rc files (bash.rc, zsh.rc, and so on). 

This is my first project with Ansible and pretty much my first project that is very coding dependent. 

## How It Works ##

The way I am using Ansible pull is by using a public [github repo](https://github.com/zer0sense/ansible_desktop). You will need to install Ansible before attempting to use this, but it is pretty standard in any package manager you use. 

You can see if the PC you are at has Ansible installed by typing
```
ansible --version
```
Ansible pull will use the local.yml in the github repository to run the plays. For the files I want to create there is a Files directory in the github repo as well. The files I have included are for my bashrc, zshrc, powerlevel 10k zsh theme, alias file, and others. This was key to me because no matter what my linux distro I definitely want these to be the same. 

The programs I want when I do a fresh install of linux are also in this playbook.

> - htop
> - tmux
> - neovim
> - terminator
> - neofetch
> - zsh
> - timeshift
> - syncthing
> - spice-vdagent 
> - curl
> - Sublime Text Editor

The most rewarding part of this playbook was figuring out that I could use git and clone any repo through Ansible as well. I used this for ohmyzsh, powerlevel10k theme, and nerd fonts. By using the git module and file copy module I have a standard desktop no matter where I call my linux home. 

## Nuances ##

With this being a learn as I go project there were some obstacles I had to think of creative ways to work around.

The first one was user variables. This was by far the most frustrating to figure out. The solution was to use sudo to update your package manager and chain the command for the Ansible pull after that.

```
sudo apt update && ansible-pull -U https://github.com/zer0sense/ansible_desktop
```
If the whole playbook was run as root then all the files paths and rc files are for the root user. This wasn't what I wanted. I wanted to be able to copy them as the current user no matter the user name. What the sudo use gives us though is for the plays that require sudo the authentication has already happened, and it shouldn't time out or require the sudo password again. Then running the Ansible pull command not being privileged the current user is the variables being used. I tested this on multiple distro's and package managers with multiple different usernames. To say I was ecstatic when it work is an understatement. 

Another nuance is the nerd font install script. There is something with the install script that comes with the repo, and the git module in Ansible pull that does not get along. I couldn't find a way in the playbook to make this work, so I included the path to the install script on the readme in the github repo. 

There is an error with neovim, but it is not reproducible on all the distros I tested. If you do a checkhealth in neovim and get a shada error I included the fix I found while googling. 

```
nvim - NORC
```
I found one issue with sublime text editor that the desktop file does not populate a picture in the application icon in Ubuntu only. This worked fine in other Debian based distros, but for some reason Ubuntu didn't pull the icon to populate the picture. If you would like to use your package manager to install sublime text instead; you can visit the sublime text webpage [here](https://www.sublimetext.com/3)

Last little nuance about this playbook is that it this is meant for a one time pull from a fresh install. I found that if you run the playbook again after oh my zsh is installed it will cause an error that the directory already exists, and it will stop the playbook. If you do want to run it again you will have to remove the directory.

```
rm -rf ~/.oh-my-zsh
```

## How To Deploy ##

First, make sure you have ansible installed. This will include ansible pull, and is available in the package managers I have tried.

Second, copy and paste this into the command line. There are two variants; one for distros using APT and the other is PACMAN. If you are using another package manager substitute the update command to whatever your package manager syntax.

__For Apt__
```
sudo apt update && ansible-pull -U https://github.com/zer0sense/ansible_desktop
```
__For PACMAN__
```
sudo pacman -Sy && ansible-pull -U https://github.com/zer0sense/ansible_desktop
```

This playbook might take a while, and it might not display a proper progress bar, but believe in the process. You will get a report once the playbook has finished, and you want to make sure you see no errors.

{{<figure src="/images/Posts/007/playbook_results.jpg">}}

I had to move some of the plays around to make sure that all the plays that required elevated privileges are at the top to take advantage of the cached authentication window. 

## Conclusion ##

This was an amazing first project, and the community I met across multiple discords were extremely helpful. This is just a fraction of the power of Ansible and automation. I know there might be people reading this saying well I can just do that with _insert the way you do it_, and that is great. However, once you start looking at everything that you can do with all the modules of Ansible, the creative gears just start cranking away.

I have another follow up project to this one in mind, and I want to do the same idea but make it penetration testing themed. The goal is no matter what linux distro you are on you have all your pentesting tools and personal configurations no matter the distro you are on. Kali is a great distro, but I want to have that type of collection and other tools found across github to be installed no matter what I choose to use. 

I hope you find this useful, and that if you do please fork it in github. Make it your own personal playbook.

The Video below is what sparked this project. Please take a look, and see if this can inspire you to play with Ansible and automation too.

{{< youtube gIDywsGBqf4 >}}

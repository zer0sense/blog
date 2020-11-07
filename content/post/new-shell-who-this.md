---
title: "New Shell Who This"
date: 2020-09-07T14:55:37+09:00
Description: "Switching to the ZSH shell from BASH"
Tags: [ZSH,Bash,Shells]
Categories: [Linux,Tutorials]
DisableComments: false
draft: false
---

One of the release notes that I noticed in the new Kali Linux version is that they are including ZSH as a shell. I can barely tap into the power of bash as a shell, but I wanted to see what some of the features of this ZSH shell was. I was extremely impressed on the plugin features and themes you can apply to the ZSH shell. With that being said, I figured that I should at least give it an install and configure to test it out. That is what today's blog post is about. How I installed and configured my ZSH shell, and some of the things that I enjoy while using it.

### Installing and Initial Setup of ZSH

ZSH is a pretty common package and actually came installed with my version of Manjaro Linux, and as discussed earlier, with Kali Linux as well. If it is not installed use your package manger to install it.

Once you have it installed you simply type:

```
zsh
```

You are greeted with this initial dialog. 

{{<figure src="/images/Posts/004/zshinitial.jpg">}}

I am not going to go through the user prompts of setting up the initial .zshrc file because I knew after watching a couple of videos that I wanted a theme. The theme that I will be using is Powerlevel 10k. Lets get that process started.


### Install Oh-My-ZSH

[Oh-My-ZSH](https://github.com/ohmyzsh/ohmyzsh) gives the customizability that I really liked while researching ZSH and how to make it mine.  

We are going to use curl to get the install script and install Oh-My-ZSH

```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

One thing I highly encourage is to cd into the .oh-my-zsh/plugins directory and take a look at all the plugins this comes with. Each plugin will come with a README.md file that you can look at and see how the plugin is used.

### Installing Our Theme

Now lets get the Powerlevel 10k [theme](https://github.com/romkatv/powerlevel10k#oh-my-zsh) installed

```
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

We will install the font dependency to go with this theme. To follow with your distribution go to this [page](https://github.com/romkatv/powerlevel10k#meslo-nerd-font-patched-for-powerlevel10k), and scroll down until it talks about Meslo Nerd Font. There are install instructions on there. I also installed the awesome font terminal pack from [here](https://github.com/gabrielelana/awesome-terminal-fonts)

Then get that theme to your .zshrc file. You will want to open the .zshrc file in a text editor and make sure the line is like the line below.

```
ZSH_THEME="powerlevel10k/powerlevel10k"
```

Now you will want to relaunch the ZSH shell. Just type:

```
zsh
```
You should be greeted with this if you are successful. 

{{<figure src="/images/Posts/004/powerlvl10kinitial.jpg">}}

Answer the questions and pay attention to the descriptions. If you ever want to go through this prompt again to reconfigure your shell's appearance just type

```
p10k configure
```

###  Plugins

I wanted to try some of the plugins. A lot of them include really great aliases to interact with things such as git,aws,docker, and many many more. If you want to add them you simply add it to the line that has 

> Plugin=(git)

You can add all the plugins you want, but please read the README.md to check for dependencies and usage. Add a space in between the plugin names to add them. One of my favorites so far is the syntax [highlighting](https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/INSTALL.md) plugin. This adds color to any command and command syntax that you use directly in the terminal. 

### Alias File

If you spent a lot of time doing aliases in BASH and cant find the plugin that gives you the same level of usage you want, you might want to look into making an alias file. This file can be used by both ZSH and BASH. 

First I created a .alias file in my home directory.

```
touch .alias
```

I then copied all my bash aliases into this file. To have your .bashrc file look at this alias file add this line in your .bashrc

```
if [ -f ~/.alias ]; then
        . ~/.alias
fi
```

To add this to your .zshrc file add the following line.

```
source $HOME/.alias
```

Don't forget that you need to reload the shell you edited by typing. 

```
bash
```
or 

```
zsh
```

This will reload your shell with the changes you made.

### Make ZSH Your Default Shell

If you want to make this newly customized shell your default when you launch your terminal you will want to edit your 

>/etc/passwd

Find your user name and make sure at the end of the line that it says.

```
/usr/bin/zsh
```
This will take effect the next time you logout or restart your machine. You can always go back in forth between them by launching the shells name.

>Bash

```
bash
```

>ZSH

```
ZSH
```

I hope you enjoy this and can see the power of the customizing your shell and play with the plugins. I know I have added this to all my Linux distros and will keep looking into it. 

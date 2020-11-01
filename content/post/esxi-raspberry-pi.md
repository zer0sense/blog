---
title: "Esxi Raspberry Pi"
date: 2020-11-01T13:01:50+09:00
Description: "Running ESXI on Raspberry Pi"
Tags: [ESXI,Virtualization,Rapsberry Pi]
Categories: [Projects,Tutorials]
DisableComments: false
draft: True
---

I would of never thought that I could set up a lab for ESXI on something as simple as the Raspberry Pi, but I am glad that I was wrong. I currently have a 4gb Raspberry Pi 4 and an 8gb version as well, and my goal is to set up an EXSI cluster using those two devices. That alone will be a great bit of experience to have under my belt. 

The end goal of this project is to setup this cluster under ESXI, and to also incorporate Kubernetes and docker wit this cluster. First thing first, lets get ESXI on the Raspberry Pi's.

I followed the [blog](https://networkchuck.com/vmware-raspberrypi/) post go go along with the video. The accompanying video will be at the end of the blog post, and it is fantastic to follow along with. I will accomplish some of these options differently with more of accomplishing everything in Linux.

## Raspberry Pi Out-of-the-box-Prep

You will need the [Raspberry Pi OS](https://www.raspberrypi.org/downloads/raspberry-pi-os/) to update your Raspberry Pi. I would suggest using the Raspbery Pi OS Lite version, as we are not going to use anything in the graphical desktop, and it is much smaller.

I prefer to use [Balena Etcher](https://www.balena.io/etcher/) due to its simplicity and it is cross-platform to flash ISO's to microSD cards.

[The Raspberry Pi CanaKit](https://www.amazon.com/CanaKit-Raspberry-8GB-Extreme-Kit/dp/B08B6F1FV5/ref=sr_1_5?dchild=1&keywords=raspberry+pi+4+8gb+canakit&qid=1604204877&sr=8-5) comes with everything you need to get started; including a microSD card and adapter. You will need to format that microSD card, and can use whatever method you like, but I use the disk option in Manjaro. GParted is another very powerful tool for disk management that you may use. I format mine to fat32 no matter what distribution I plan to put on my Pi.

We will use the option to select to flash from file. 

{{<figure src="/images/Posts/005/etcher.jpg">}}

Once selected, click the select target button, and choose your drive. Step 3 is flash. You now have your OS ready for your Pi. 

Give it a while to boot, and once you are at your command prompt you are going to run the following commands to make sure your Raspberry Pi is up to date and ready.

```
sudo rpi-eeprom-update
```
```
sudo rpi-eeprom-update -a
```
Once updated you can shut down your Raspberry Pi and pull the card back out.
```
sudo shutdown -now
```
You will now erase the card of Raspberry Pi OS using Disk management or GParted if back to nothing.

The whole point of doing this to update your Raspberry Pi is to make sure that you are able to boot off of USB, which was an update after the Raspberry Pi was originally released.

## Prepping the Pi for ESXI

You will need to download the [Latest Raspberry Pi Firmware](https://bit.ly/2HpIaG6) and the [UEFI](https://bit.ly/3jota8D). Once you download those extract it to whichever folder you want. 

You will want to copy the __firmware-master/boot/__ folder and copy all the usbdrive that you just recently formatted. Once they are finished copying, if you have any files that are _kernel.img_ you should delete the files. 

Now open the __RPi4_UEFI__ folder and copy all of it's content to the SD card as well. You will be prompted if you want to replace the file and yes you want to replace all the files. 

Once those have all copied you will want to open the file name __config.txt__ and add this line.
```
gpu_mem=16
```
Save your changes to the file, eject your microSD card, and you can remove it from your PC.

## ESXI Download and Installation

We will need an [ESXI Image](https://flings.vmware.com/esxi-arm-edition) to download for the installation. If you do not have a VMWare account you can go ahead and sign up. The account is free.

Once you have downloaded the image we will jump back into Etcher, and when we select the file we will use a separate usbdrive to flash the image on to.


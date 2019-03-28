---
layout: post
title: Arch Linux Install: Standalone
subtitle: Install Arch Linux on a standalone system
gh-repo: daattali/beautiful-jekyll
gh-badge: [star, fork, follow]
tags: [arch,install,linux,tutorial]
comments: true
---
# Arch linux install
You should always follow the install located at the [Arch wiki](https://wiki.archlinux.org/index.php/installation_guide) but if for some reason you are havingtrouble I hope this tutorial can help in some way.
## Table of Contents
* [Arch Linux Install](#Arch-Linux-Install)
  * [Standalone Install](#Standalone-Install)
  * [Installing Arch](#Installing-Arch)
  * [Internet Setup](#Internet-Setup)


## Standalone Install
### Prep the install media
Prep the install media anyway you would prefer. I will not go over this in detail as there are many tutorials out there that already cover this. You will need to download the Arch ISO and use a bootable usb or cd if you prefer. 

### Installing Arch
With the USB plugged into the computer boot up your computer.

**NOTE**:
Make sure your boot sequence includes the USB first if you already have another Operating system on the computer. If you are having trouble most computers have a select boot sequence during the initial bios splashscreen and you should be able to select the USB.
**ENDNOTE**

You should be presented with the Arch live install which should look like this:
```
Arch Linux 4.20.13-arch1-1-ARCH (tty1)

archiso login: root (automatic login)
root@archiso ~ #
```
This is the live install session to Install Arch Linux:

#### Internet Setup
You will need to have a internet connection to install some packages. You can use an ethernet port or you can use wifi.

**NOTE** 
In some circumstances your wifi drivers may not work out of the box with the install. In this instance use ethernet or a usb -> ethernet adapter
**ENDNOTE**

To connect via wifi simply run:
``` root@archiso ~ # wifi-menu```



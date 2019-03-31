---
layout: post
title: How to Install Arch Linux
toc: true
subtitle: Install Arch Linux as a standalone system or dual boot with windows
tags: [linux,tutorial,install,arch,dual boot,windows,How to]
comments: true
---

Learn how to install Arch Linux on your own computer in either a standalone system or in a dual boot system. Also install some packages and also a windows manager

## How to Install Arch Linux Install

You should always follow the install located at the [Arch wiki](https://wiki.archlinux.org/index.php/installation_guide) but if for some reason you are having trouble I hope this tutorial can help in some way.
## Table of Contents
* [How to Install Arch Linux](#How-to-Install-Arch-Linux)
  * [Standalone Install](#Standalone-Install)
  * [Installing Arch](#Installing-Arch)
  * [Internet Setup](#Internet-Setup)
  * [System Clock](#System-Clock)
  * [Disk Partition](#Disk-Partition)
  * [Drive Encryption and LVM](#Drive-Encryption-and-LVM)
  * [Mirrors](#Mirrors)
  * [Install Base System](#Install-Base-System)
  * [Generate Fstab](#Generate-Fstab)
  * [Chroot](#Chroot)
  * [Time Zone](#Time-Zone)
  * [Localization](#Localization)
  * [Network Config](#Network-Config)
  * [Initramfs](#Initramfs)
  * [Root Passwd](#Root-Passwd)
  * [Create User](#Create-User)
  * [Bootloader](#Bootloader)
  * [Reboot](#Reboot)
  * [Configuring Arch](#Configuring-Arch)
  


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

To connect via ethernet run:

```dhpcd```

Test your internet conncection by pinging a website:

```ping -c 4 www.google.com```
 
If your connection is working you should be getting packets back

#### System Clock

Set the system clock by running the following command

```timedatectl set-ntp true```

#### Disk Partition
We will need to partition the disks in order to properly install Arch Linux.
Check your current disk setup with ```fdisk -l```

Your harddrive should be named something like /dev/sda or it may be another letter if you have mutliple hardrives.

You can then partition your disks with the tool ```cfdisk /dev/sda```

![archbootpartition](https://s3-us-west-2.amazonaws.com/kevinblogpictures/archbootpartition.png)

Create a boot partition sized ```500M``` and with the type ```EFI system```
![bootsizetype](https://s3-us-west-2.amazonaws.com/kevinblogpictures/bootsizetype.png)

Go ahead and use the rest of the space for your install. We will split it up later into ```root``` and ```home``` partitions later. Choose the type Linux filesystem
![rootpartition.png](https://s3-us-west-2.amazonaws.com/kevinblogpictures/rootpartition.png)

You can now write the changes to disk and quit afterwards. Make sure to check your setup by running the ```fdisk -l``` command. Your setup should look similar to this.
![checkdisksetup.png](https://s3-us-west-2.amazonaws.com/kevinblogpictures/checkdisksetup.png)

#### Drive Encryption and LVM

We are going to encrypt our harddrive and add a passcode to unlock the drive as a secure measure during bootup. We are not encrypting the boot drive so make sure to select the correct partition number. For me that was ```/dev/sda2```.

You can run ```cryptsetup benchmark ``` to check sppeds on different ciphers and key-sizes for your encryption.

We are going to run the below command to encrypt our drive.

```cryptsetup --cipher aes-xts-plain64 \```
```--key-size 512 \```
```--hash sha256 \```
```--iter-time 3000 ```
```--use-random \```
```--verify-passphrase \```
```luksFormat /dev/sda2```


Type the uppercase YES and choose a good passphrase and enter it in twice.

Next time to setup the LVM with the volumes and filesystems.

open the encrypted partition with the following command ```cryptsetup luksOpen /dev/sda2 lvm```

What this command does is map the encrypted partition to the file location ```/dev/mapper```
The next step is to create physical and logical volumes for the ```root``` and ```home``` directories. I gave the ```root``` directory here ```20GB``` but you can choose to do whatever you think will be sufficient for you.

Physical Volume :```pvcreate /dev/mapper/lvm```

Create Volume with name ```arch``` you can choose to name it whatever you would like: ```vgcreate arch /dev/mapper/lvm```

Create Logical Volumes ```20GB``` for the ```root``` and the rest allocated for ```home```:

```lvcreate --size 20G --name root arch```

```lvcreate --extents +100%FREE --name home arch```

The partitions created are now in ```/dev/mapper/arch-root``` and ```/dev/mapper/arch-home```. We will need to format them to a particular file system. Here I will use the ```ext4``` filesystem.

```mkfs.ext4 /dev/mapper/arch-root```

```mkfs.ext4 /dev/mapper/arch-home```

Now mount those partitions

```mount /dev/mapper/arch-root /mnt```

```mkdir -p /mnt/home```

```mount /dev/mapper/arch-home /mnt/home```

Also boot the mount partition after formatting it to ```ext.2```

```mkfs.ext2 /dev/sda1```

```mkdir -p /mnt/boot```

```mount /dev/sda1 /mnt/boot```

To read more about the encryption I used you can find that here : [Encryption](https://wiki.archlinux.org/index.php/Dm-crypt/Device_encryption#Encryption_options_for_LUKS_mode)

Some installs will require a swap partition but with enough RAM 16GB or so you should be fine without it.
If you do want to read more about the partition schemes you can find that here : [Partition](https://wiki.archlinux.org/index.php/Installation_guide#Partition_the_disks)

We are now ready to install Arch Linux to the disk

#### Mirrors

You can select mirrors if you would like, delete or comment out the mirrors you do not want to use:

```vi /etc/pacman.d/mirrorlist```

Arch has a conveinant mirror list generator that comes in handy: ![mirror list](https://www.archlinux.org/mirrorlist/)
#### Install Base system

Install Arch Linux:

```pacstrap -i /mnt base base-devel```

You can install with additional packages in this command if you would like. The live CD has more packages than the base install comes with. You can find all the packages in the live CD ![here](https://git.archlinux.org/archiso.git/tree/configs/releng/packages.x86_64)

**Example**:
```pacstrap -i /mnt base base-devel dialog wpa_supplicant wireless_tools```

#### Generate fstab

We will use labels for our disks because the encryption will create random IDs

```genfstab -L -p /mnt >> /mnt/etc/fstab```

For UUID fstab you can run

```genfstab -U /mnt >> /mnt/etc/fstab```

Make sure to check the generated fstab for any errors, mine looked like this:

```#```  

```# /etc/fstab: static file system information```

```#```

```# <file system>    <dir><type><options>             <dump><pass>```

```# /dev/mapper/arch-root```

```/dev/mapper/arch-root    /           ext4   discard,rw,relatime,data=ordered    0 1```

```# /dev/mapper/arch-home```

```/dev/mapper/arch-home    /home       ext4   discard,rw,relatime,data=ordered    0 2```


**Note**:If using solid state drive make sure discard is there in the options for optimizing the speed of SSD's**ENDNOTE**

#### Chroot

Change root in the new system: ```arch-chroot /mnt```

#### Time Zone

Set the time zone: ```# ln -sf /usr/share/zoneinfo/Region/City /etc/localtime``` for me this was ```# ln -sf /usr/share/zoneinfo/America/Los_Angeles /etc/localtime```

```# hwclock --systohc```

#### Localization

Uncomment the locale that you would like to use in the file /etc/locale.gen .I am located in the US so I am going to use these

```en_US.UTF-8 UTF-8```
```en_US ISO-8859-1```
 Generate the locale: ```locale-gen```
 
 Set Default: ```echo LANG=en_US.UTF-8 > /etc/locale.conf```

#### Network Config
```echo hostname > /etc/hostname```

Add hostname to ```/etc/hosts```


```#```

```# /etc/hosts: static lookup table for host names```

```#```

```#<ip-address>   <hostname.domain.org>   <hostname>```

```127.0.0.1   localhost.localdomain   localhost   macbook```

```::1     localhost.localdomain   localhost   macbook```

If you have a static IP you can put that in place of 127.0.0.1 

Use this link to learn more about network configuration in Arch linux [Network Config](https://wiki.archlinux.org/index.php/Network_configuration)

#### Initramfs

We need to add kernel modules to make sure the kernel knows how to decrypt our partition. Edit your ```/etc/mkinitcpio.conf``` and add the modules **BEFORE** filesystems in the HOOKS line.

```HOOKS="base udev autodetect modconf block keyboard usbinput encrypt lvm2 filesystems fsck"```

Then run

```mkinitcpio -p linux```

#### Root Passwd

Set the root passwd with the command ```passwd```

#### Create User

Create a new non root user.

```useradd --create-home --groups wheel --shell /bin/bash USER```
```passwd USER```

This adds user to the wheel group. We will need to run the command ```visudo```
And uncomment the following line:

```%wheel ALL=(ALL) ALL```

#### Bootloader

We will go over a couple boot loaders here. I have been using systemd and grub. There are many out there. This chart from the official wiki is useful when looking at chooing an option [bootloaders](https://wiki.archlinux.org/index.php/Arch_boot_process#Boot_loader).

Run the command ```pacman -S systemd ``` to install the systemd bootloader. Then run the command ```mkdir -p /boot/loader/entries```

Setup the loader to default to Arch and set the number of seconds before auto boot. Do this in the file ```/boot/loader/loader.conf``` 

```default arch```

```timeout 3```

Make sure the boot partition is currently mounted. ```findmnt /boot```

We have to create an entry for the bootloader in the file ```/boot/loader/entries/arch.conf```

```# /boot/loader/entries/arch.conf```

```title   Arch Linux```

```linux   /vmlinuz-linux```

```initrd  /initramfs-linux.img```

```options cryptdevice=/dev/sda2:arch:allow-discards root=/dev/mapper/arch-root rw```

Then run the command ```bootctl install```

#### Reboot

**NOTE** If you didnt install the networking drivers for wifi on reboot into the new install you will not have wifi access as you did in the live cd **ENDNOTE**

type ```exit``` then ```umount -R /mnt```  check to make sure the drives are volumes are good to go ```cryptsetup close vgcrypt```

You can double check your install to see if the encryption unlocks 

```cryptsetup open /dev/sda2 arch```

```mount /dev/mapper/arch /mnt```

If everything has gone well we can reboot with the command ```reboot```

The bootloader should be presented with the option Arch Linux and 3 seconds before auto booting.

#### Configuring Arch






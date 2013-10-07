---
layout: post
title: Installing Minix 3 using VirtualBox in Linux 
category: Minix VirtualBox
comments: true
---

This article will help you in setting up Minix 3 using VirtualBox on a Linux Host for development.
Enabling ssh in virtualBox


##Downloading Minix 3

Download Minix 3 from the official website

[]http://www.minix3.org/download/(http://www.minix3.org/download/)

Note: I used the Minix version 3.2.1(265 MB), Minix 3.1 versions have some issues with VirtualBox installation so please use a version > 3.2 .

##Creating a new VirtualBox Image

Make sure you have a VirtualBox install in your system.

Open VirtualBox -> Click on the New button seen at the top left.

Select the type and version as others, name the virtualbox image as minix.

Click next, allot a RAM ( 512 MB) 

Create a virtual hard disk ( preferably VDI, dynamic size, 1 GB)


##Installing Minix 3 

Assuming you have downloaded and decompressed a MINIX 3 ISO image from the download page, you can mount the ISO file:

In VirtualBox, select minix on the list on the left.
In the menu on the right, press CD/DVD-ROM.
In the next menu, tick Mount CD/DVD Drive, and select ISO Image File.
Browse, select the .iso Minix image we downloaded earlier and press OK

Now boot the newly created virtual Image minix by clicking on the start button.

Select the option 1 for installation and press enter.

When the login prompt appears, login as root. Press Enter when prompted for a password.

To start installation type,
    
    setup

After this and all other commands, be sure to type ENTER (RETURN). When the installation script ends a screen with a colon, hit ENTER to continue.

###Select keyboard type

When you are asked to select your national keyboard, do so. This and other steps have a default choice, in square brackets. If you agree with it, just hit ENTER. In most steps, the default is generally a good choice for beginners. The us-swap keyboard interchanges the CAPS LOCK and CTRL keys, as is conventional on UNIX systems.

###Create or select a partition for MINIX

You will first be asked if you are an expert in MINIX disk partitioning. If so, you will be placed in the part program to give you full power to edit the Master Boot Record (and enough rope to hang yourself). If you are not an expert, press ENTER for the default action, which is an automated step-by-step guide to formatting a disk partition for MINIX.

###Select a disk

An IDE controller may have up to four disks. The setup script will now look for each one. Just ignore any error messages. When the drives are listed, select one. and confirm your choice.

If you have two hard disks and you decide to install MINIX to the second one and have trouble booting from it, see Installation Troubleshooting.

###Select a disk region

Now choose a region to install MINIX into. You have three choices:

Select a free region
Select a partition to overwrite
Delete a partition to free up space and merge with adjacent free space
For choices (1) and (2), type the region number. For (3) type:


    delete

then give the region number when asked. This region will be overwritten and its previous contents lost forever.

###Confirm your choices

You have now reached the point of no return. You will be asked if you want to continue. If you do, the data in the selected region will be lost forever. If you are sure, type:


    yes

and then ENTER. To exit the setup script without changing the partition table, hit CTRL-C.

###Reinstall choice

If you chose an existing MINIX partition, in this step you will be offered a choice between a Full install, which erases everything in the partition, and a Reinstall, which does not affect your existing /home partition. This design means that you can put your personal files on /home and reinstall a newer version of MINIX when it is available without losing your personal files.

###Select the size of /home

The selected partition will be divided into three subpartitions: root, /usr, and /home. The latter is for your own personal files. Specify how much of the partition should be set aside for your files. You will be asked to confirm your choice.

###Select a block size

Disk block sizes of 1-KB, 2-KB, 4-KB, and 8-KB are supported, but to use a size larger than 4-KB you have to change a constant and recompile the system. Use the default (4 KB) here.

###Wait for files to be copied

Files will be automatically copied from the CD-ROM to the hard disk. Every file will be announced as it is copied.

###Select your Ethernet chip

You will now be asked which (if any) of the available Ethernet drivers you want installed. Network settings can be changed after installation. 
Since we are using VirtualBox "AMD LANCE" (option 8) as your Ethernet driver, VirtualBox is capable of emulating the AMD LANCE and it has nothing to do with your computers network card.

###Restart

When the copying is complete, MINIX is installed. Shut the system down by typing:

    shutdown

You can now remove/unmount the iso image that we attached to virtual machine(so that it wont again boot into the installation ISO). When you boot up again, you will be running MINIX.

##How to enable SSh

Enabling ssh in minix and host is good option to do some development over the guest minix operating system.

To enable ssh in the guest minix operating system you have to install openssh server in MINIX.

###Making VirtualBox to listen to a particular port (Port forwarding) 

We will have to change some settings in virtualbox using VBoxManage 

In you host operating systems terminal (Linux terminal) type,

    VBoxManage modifyvm "minix" --natpf1 "guestssh,tcp,,2222,,22"

It is important that you chose a port number larger than 1024 for host since administrative rights are required by virtualbox to listen for ports below 1024 (here we choose 2222). Also please dont use 22 for host since it will only end up in looping back into your own system.

###Installing openssh in MINIX

Restart your virtualBox application. Boot into minix and install openssh.

You can install openssh using pkgin 

Update pkgin type,

    pkgin update 

Install openssh type,

    pkgin install openssh

Installing from the CD (In case you are not able to install with pkgin, you failed to do the above steps)

Many packages are available directly from the CD. This can be helpful in some circumstances, and is generally faster than downloading from the online repository.
To install packages from the CD, you can use pkgin\_cd. This command uses the CD-ROM as the package repository. It is a wrapper for pkgin and therefore supports the same commands.

To begin using pkgin\_cd:

While your virtual image is running you can attach your iso disk to minix.To do so please click on the Devices menu that you see in the VitualBox window.

Now select the iso.

To install openssh type,
        
    pkgin_cd install openssh

Make sure that ssh daemon is running type,

    ps ax | grep ssh

Now come back to Linux Terminal(host terminal)

Check if ssh is running in your host machine type,
    
    ps aux | grep ssh

if not please install and enable ssh 

    sudo apt-get install openssh && sudo start ssh

To ssh into minix from host machine type(in host machine)

    ssh -l root -p 2222 localhost 

Enter your minix password(if didnt set any just press ENTER).

Links:
[http://wiki.minix3.org/en/UsersGuide](http://wiki.minix3.org/en/UsersGuide)

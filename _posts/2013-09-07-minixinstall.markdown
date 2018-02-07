---
layout: post
title: Installing Minix 3 using VirtualBox in Linux 
category: Minix VirtualBox
comments: true
tags: [Minix 3 installation, ssh, minix 3, virtualbox, without internet, installing packages, linux ]
---

This article will help you in setting up Minix 3 using VirtualBox on a Linux Host for development and enabling ssh connection between Minix and host Linux machine.     

## But why, but why??

Minix is a micro kernel, unlike Linux which is monolithic Minix is a operating system which is Reliable. Being a micro kernel good sepration around the Kernel Space and User Space, Minix Kernel is only 6000 lines of code compared to millions of lines of code in Linux Kernel.      

This makes Minix perfect system to study for understanding operating systems, [Andrew Tanenbaum](https://en.wikipedia.org/wiki/Andrew_S._Tanenbaum) has [book](https://mcdtu.files.wordpress.com/2017/03/tanenbaum_woodhull_operating-systems-design-implementation-3rd-edition.pdf) detailing all of Minix/Operating Systems, so if you are a under graduate looking to do something around operating systems as your major project is not a bad idea.

Teststed with Ubuntu 13.04 and Arch Linux.    

## Downloading Minix 3

Download Minix 3 from the official website.

[Official download page](http://www.minix3.org/download/)   
Note: I used the Minix version 3.2.1 (~256 MB), Minix 3.1 versions have some issues with VirtualBox installation so please use a version > 3.2 .

## Creating a new VirtualBox Image

* Make sure you have a VirtualBox installed in your system.
* Open VirtualBox -> Click on the New button(top left).
* Select the type and version as "other", name the virtualbox image "minix".
* Click next, allot a RAM ( 512 MB).
* Create a virtual hard disk ( preferably VDI, dynamic size, 1 GB).


## Installing Minix 3 

* Assuming you have downloaded and decompressed a MINIX 3 ISO image, attach the ISO file to VirtualBox:
* In VirtualBox, select minix on the list on the left.
* In the menu on the right, press CD/DVD-ROM.
* In the next menu, tick Mount CD/DVD Drive, and select ISO Image File.
* Browse, select the .iso Minix image we downloaded earlier and press OK
* Now boot the newly created virtual Image minix by clicking on the start button.
* Select Option 1 for installation and press ENTER.
* When the login prompt appears, login as root, press Enter when prompted for a password.

To start installation type,
    
`setup`

After this and all other commands, be sure to type ENTER (RETURN). When the installation script ends a screen with a colon, hit ENTER to continue.

### Select keyboard type

When you are asked to select your national keyboard, do so. This and other steps have a default choice, in square brackets. If you agree with it, just hit ENTER. In most steps, the default is generally a good choice for beginners. The us-swap keyboard interchanges the CAPS LOCK and CTRL keys, as is conventional on UNIX systems.

### Create or select a partition for MINIX

You will first be asked if you are an expert in MINIX disk partitioning. If so, you will be placed in the part program to give you full power to edit the Master Boot Record (and enough rope to hang yourself). If you are not an expert, press ENTER for the default action, which is an automated step-by-step guide to formatting a disk partition for MINIX.

### Select a disk

An IDE controller may have up to four disks. The setup script will now look for each one. Just ignore any error messages. When the drives are listed, select one. and confirm your choice.   
If you have two hard disks and you decide to install MINIX to the second one and have trouble booting from it, see Installation Troubleshooting.

### Select a disk region

Now choose a region to install MINIX into. You have three choices:

Select a free region
Select a partition to overwrite
Delete a partition to free up space and merge with adjacent free space
For choices (1) and (2), type the region number. For (3) type:

`delete`

then give the region number when asked. This region will be overwritten and its previous contents lost forever.

### Confirm your choices

You have now reached the point of no return. You will be asked if you want to continue. If you do, the data in the selected region will be lost forever. If you are sure, type:

`yes`

and then ENTER. To exit the setup script without changing the partition table, hit CTRL-C.

### Reinstall choice

If you chose an existing MINIX partition, in this step you will be offered a choice between a Full install, which erases everything in the partition, and a Reinstall, which does not affect your existing /home partition. This design means that you can put your personal files on /home and reinstall a newer version of MINIX when it is available without losing your personal files.

### Select the size of /home

The selected partition will be divided into three subpartitions: root, /usr, and /home. The latter is for your own personal files. Specify how much of the partition should be set aside for your files. You will be asked to confirm your choice.

### Select a block size

Disk block sizes of 1-KB, 2-KB, 4-KB, and 8-KB are supported, but to use a size larger than 4-KB you have to change a constant and recompile the system. Use the default (4 KB) here.

### Wait for files to be copied

Files will be automatically copied from the CD-ROM to the hard disk. Every file will be announced as it is copied.

### Select your Ethernet chip

You will now be asked which (if any) of the available Ethernet drivers you want installed. Network settings can be changed after installation. 
Since we are using VirtualBox select "AMD LANCE" (option 8) as your Ethernet driver, VirtualBox is capable of emulating AMD LANCE and it has nothing to do with your computers network card.

### Restart

When the copying is complete, MINIX is installed. Shut the system down by typing:

`shutdown -h now`

You can now remove/unmount the iso image that we attached to virtual machine(so that it wont again boot into the installation ISO). When you boot up again, you will be running MINIX.

## Enabling SSH

Enabling ssh in minix and host is good option to do some development over the guest minix operating system.    
To enable ssh in the guest minix operating system you have to install openssh in MINIX and in your host machine.

### Making VirtualBox to listen to a particular port (Port forwarding) 

We will have to change some settings in virtualbox using VBoxManage(a commandline tool to tweak virtualbox settings)   
It is important that you chose a port number larger than 1024 for host since administrative right is required by virtualbox to listen for ports below 1024 (here we choose 2222). Also note that using port 22 will only result in looping back into your own system.   

In you host operating systems terminal (Linux terminal) run the following command,

`VBoxManage modifyvm "minix" --natpf1 "guestssh,tcp,,2222,,22"`

"guestssh" is just a name for the port forwarding rule. "minix" - name of the virtualbox image 

### Set a password for root account in Minix

In minix machine you will have to set a password for root user.A password for the root user can be set using the command 
    
`passwd`

Now enter a desired password for the root account

### Installing openssh in MINIX

Restart your virtualBox application. Boot into minix and install openssh.
<br />  
**Method 1, using pkgin and internet ftp access required**
<br />
You can install openssh using pkgin 
<br />
Update pkgin type,

`pkgin update`
    
Install openssh type,

`pkgin install openssh`

**Method 2, using installation iso as a source (Internet not required)**  
Many packages are available directly from the CD. This can be helpful in some circumstances, and is generally faster than downloading from the online repository.   
To install packages from the CD, you can use pkgin\_cd. This command uses the CD-ROM as the package repository. It is a wrapper for pkgin and therefore supports the same commands.   
While your virtual image is running you can attach your iso disk to minix.To do so please click on the Devices menu that you see in the VitualBox window, select the minix installation iso and load it(this disk will be used as source for the software package). In case if you are not able to view *Add CD/DISK* device option under devices menu you might be missing [Guest additions extenssion](https://help.ubuntu.com/community/VirtualBox/GuestAdditions) for VirtualBox. 
<br />  
To install openssh type,
        
`kgin_cd install openssh`

**Starting SSh daemon**   
When you have successfully installed openssh start the ssh daemon in minix type(starting ssh will create your keys),

`sh /usr/pkg/etc/rc.d/sshd start`

Make sure that ssh daemon is running in minix type,

`ps -ax | grep ssh`

###Now come back to Linux Terminal (host terminal)

Check if ssh is running in your host machine,
    
`ps aux | grep ssh`

If not please install and enable ssh(depends on your distro)   
Ubuntu / Debian users
    
`sudo apt-get install openssh-server1`     
`sudo start ssh`

Arch Linux users
    
`sudo pacman -S openssh-server`     
`sudo systemctl start sshd`   

To ssh into minix from host machine type,

`ssh -l root -p 2222 localhost` 

Enter your minix password.  
<br /> 
Links:   
[Users Guide](http://wiki.minix3.org/en/UsersGuide)

---
layout: post
title:  Linuxdcpp, Dc++ client for fedora
category: Linux fedora dcpp
---

This tutorial is to install linuxdcpp in fedora 17.I'm trying to compile all information required to install and run linuxdcpp in fedora 17.

You will require rpm-sphere repo to install linuxdcpp in fedora, so lets add this repo first by creating a file rpm-sphere.repo in /etc/yum.repos.d

Now copy the following content into the file

<pre>
[rpm-sphere]

name=RPM Sphere

baseurl=http://download.opensuse.org/repositories/home:/zhonghuaren/Fedora_17/

gpgkey=http://download.opensuse.org/repositories/home:/zhonghuaren/Fedora_17/repodata/repomd.xml.key

enabled=1

gpgcheck=1
</pre>

save it and run the following commands as a root user to install linuxdccp

<pre>
yum update

yum install linuxdcpp
</pre>



Possible bugs

 1. There may be issues in selecting the folder that you want to share in dcpp(like you get home always selected as the folder ).To overcome this bug you will have to edit the xml  file (before emitting the file close the dccp) in ~/.dc++/DCPlusPlus.xml

you will find something like this at the end of the file


<pre>
"
<Share>

  <Directory Virtual="Sharename">"path to your share directory"</Directory>

  </Share>
"
</pre>

  change the path and sharename as you wish.
  save the file, make the file read only using "chmod -w filename" before opening the dccp again 

  2. You wont be able to download file list.

  Try changing the firewall setting in the file > preference > connection > incoming port forwarding , by default its active make it passive restart the linuxdccp


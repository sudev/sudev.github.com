---
categories:
- posts
comments: true
date: 2013-03-22T00:00:00Z
tags:
- Fedora Nitc mirror
- fosscell mirror
- configuration
- nit calicut
title: Configuring to Fedora 17 Local Mirror of NITC
---

This will help you to configure your system to download packages from NITC fosscell fedora mirror.      


Login as root in your system and use the following command to create a file inside the folder `/etc/yum.repos.d`   


{{< highlight csv >}}
touch /etc/yum.repos.d/fosscellfedora
{{< / highlight >}}

Copy and paste the folowing code into the created file using your favourite editor.      

{{< highlight csv >}}

## Nitc fosscell fedora local mirror for fedora 17 and 18 
[NITCFedora-updates]
name=Fedora $releasever - $basearch - Updates
failovermethod=priority
baseurl=http://fosscell.nitc.ac.in/fedora/updates/$releasever/$basearch
mirrorlist=http://mirrors.fedoraproject.org/mirrorlist?repo=updates-released-f$releasever&arch=$basearch
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-fedora-$basearch 


[NITCFedora]
name=Fedora $releasever - $basearch
failovermethod=priority
baseurl=http://fosscell.nitc.ac.in/fedora/releases/$releasever/Everything/$basearch/os/
mirrorlist=http://mirrors.fedoraproject.org/mirrorlist?repo=fedora-$releasever&arch=$basearch
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-fedora-$basearch

{{< / highlight >}} 
   
Update packages.    

{{< highlight csv >}}
sudo yum install update
{{< / highlight >}}

     

Reference:
[Fedora docs](http://docs.fedoraproject.org/en-US/Fedora/16/html/System_Administrators_Guide/sec-Managing_Yum_Repositories.html)


---



[jekyll]: https://github.com/mojombo/jekyll
[zh]: http://sudev.github.com
[twitter]: https://twitter.com/sudev

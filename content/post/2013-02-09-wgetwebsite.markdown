---
categories:
- posts
comments: true
date: 2013-02-09T00:00:00Z
tags:
- wget to mirror website
- mirror website
- wget
- recursice downloads
title: Offline mirror a website using wget
---

Use the following wget command to mirror a website 

{{< highlight bash >}} 
wget -mkpb some-website-url
{{< / highlight >}}

* -m  mirrors the entire website
* -k  converts all links to suitable web viewing.
* -p  downloads all required files like that of the css, js ...
* -b  wget will run in background 

This method won't work for many websites as their server will block wget from downloading. I will update this post soon, we can use user agents in wget to mock wget as a browser.

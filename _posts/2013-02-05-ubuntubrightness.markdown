---
layout: post
title: Permanently adjust screen brightness in Ubuntu / Linux Mint 
category: posts
tags: [ubuntu brightness problem, permenantly set ubuntu brightness, ubuntu brightness reset error, Linux Mint]
comments: true
---

This details a fix to reset the screen brightness in Ubuntu / Linux Mint during each boot.       

This idea will work only if you have a file named brightness in the folder `/sys/class/backlight/acpi_video0` and the hack is relevant for the Ubuntu (version < 12.04) where they had a bug in which the brightness is too high during each restart.       


To know your systems current brightness level.

{% highlight bash %}
cat /sys/class/backlight/acpi_video0/brightness
{% endhighlight %}

Change brightness by changing the value 

{% highlight bash %}
echo 0 > /sys/class/backlight/acpi_video0/brightness
{% endhighlight %}

To permanently set the brightness you can use the rc.loacal script file to change brightness which is executed at each reboot

{% highlight bash %}
sudo gedit /etc/rc.local
{% endhighlight %}


add the following line above "exit 0" seen at the bottom of the document 

{% highlight bash %}
echo 0 > /sys/class/backlight/acpi_video0/brightness
{% endhighlight %}

restart your system after saving the file.



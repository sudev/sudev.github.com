---
layout: post
title: Permanently adjust screen brightness in Ubuntu / Linux Mint 
category: posts
tags: [Sudev Ambadi, Sudev,ubuntu brightness problem, permenantly set ubuntu brightness, ubuntu brightness reset error, Linux Mint]
comments: true
---

This is a fix to  adjust the screen brightness in Ubuntu / Linux Mint during each boot automatically.     
This idea will work only if you have a file named brightness in the folder /sys/class/backlight/acpi_video0  
<br />
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

---



[jekyll]: https://github.com/mojombo/jekyll
[zh]: http://sudev.github.com
[twitter]: https://twitter.com/sudev

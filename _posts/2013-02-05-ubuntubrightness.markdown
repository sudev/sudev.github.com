---
layout: post
title: Permanently adjust screen brightness in Ubuntu 12.04 / Linux Mint 13 
category: posts
---

This tutorial will help you to adjust the screen brightness in Ubuntu 12.04 / Linux Mint 13 during each boot automatically.

Tested in Intel i7 laptop with Nvidia Graphics Card, this idea will work only if you have a file named brightness in /sys/class/backlight/acpi_video0

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

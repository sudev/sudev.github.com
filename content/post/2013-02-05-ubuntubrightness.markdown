---
categories:
- posts
comments: true
date: 2013-02-05T00:00:00Z
tags:
- ubuntu brightness problem
- permenantly
- ubuntu brightness reset error
- Linux Mint
title: Permanently adjust screen brightness in Ubuntu / Linux Mint
---

This details a fix to reset the screen brightness in Ubuntu / Linux Mint during each boot.       

This idea will work only if you have a file named brightness in the folder `/sys/class/backlight/acpi_video0` and the hack is relevant for the Ubuntu (version < 12.04) where they had a bug in which the brightness is too high during each restart.       


To know your systems current brightness level.

{{< highlight csv >}}
cat /sys/class/backlight/acpi_video0/brightness
{{< / highlight >}}

Change brightness by changing the value 

{{< highlight csv >}}
echo 0 > /sys/class/backlight/acpi_video0/brightness
{{< / highlight >}}

To permanently set the brightness you can use the rc.loacal script file to change brightness which is executed at each reboot

{{< highlight csv >}}
sudo gedit /etc/rc.local
{{< / highlight >}}


add the following line above "exit 0" seen at the bottom of the document 

{{< highlight csv >}}
echo 0 > /sys/class/backlight/acpi_video0/brightness
{{< / highlight >}}

restart your system after saving the file.



---
layout: post
title: "使用btsync"
date: 2015-01-20 18:18:25 +0800
comments: true
tags: linux, fun
---
<meta charset="UTF-8"/>
昨天从[编程随想](http://program-think.blogspot.com/)的博客上知道了[btsync](https://www.getsync.com/)这个东西，用来p2p分享文件很是方便，所以我也尝试了一下，配置很简单，我在自己的笔记本和公司电脑都装好了，这样上班的时候2台电脑同步文件就方便了。

但是遇到了一个有点麻烦的问题，在最初我能够脸上p2p网络，并且找到了供源的机器，但是就是没法和他们同步，总是提示和另一方时间偏差太大无法同步，搜了一下发现是系统时间设置的问题，通过[wiki](https://wiki.archlinux.org/index.php/Time)又好好了解了一下linux的时间机制。

linux上的时间包括2种，一是硬件时间，另一个是系统时间。

1. 硬件时间：(Real Time Clock, RTC)是由机器硬件记录的时间，根据[tldp](http://www.tldp.org/LDP/sag/html/hw-sw-clocks.html)的说法，个人电脑都内建了一块独立的电池，来给一个时钟供电，这个时钟可以通过BIOS来设置，或者开机之后通过操作系统来设置。

2. 系统时间:(software clock)，这个时间是由linux内核来设置的。在系统中存放着一个叫做`/etc/adjtime`的配置文件，我本机这个文件是7月份创建的，也就是我上次重装系统的时间，是linux的初始系统时间，之后linux估计用了某种还不清楚的手段，每次开机都会更新最新的系统时间。

我的问题就是系统时间和硬件时间不一致，估计是因为双系统和windows共存导致的，解决方法就是通过执行`sudo hwclock --systz`来把系统时间和硬件时间都和本地时区的时间对齐。时区配置文件是`/etc/localtime`这个文件可以通过`timedatectl`命令来设置，也可以手动建立到`/usr/share/zoneinfo/Asia/Hong_Kong`的软链接，或者是直接复制`/usr/share/zoneinfo/Hong_Kong`到`/etc/localtime`来设置。

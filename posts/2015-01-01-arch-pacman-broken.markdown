---
layout: post
title: 解决pacman依赖关系破裂问题
date: 2015-01-01 11:35:23 +0000
comments: true
tags: linux
---


今天准备更新一下系统，执行`sudo pacman -Syu`的时候，竟然没法同步软件源了，报告说依赖错误。
<pre>
packagekit: requires pacman<4.2.0
package-query: requires pacman<4.2.0
</pre>
但是我查看了一下pacman的版本,`pacman --version`发现是4.1的满足需求啊，为什么会有这样的提示呢？我想用`sudo pacman -S pacman`重装pacman也不行，真是费解，这么重要的软件可千万不能出错啊。后来上网搜了一下，发现有人也遇到这种情况，都是直接`sudo pacman -R packagekit python-packagekit yaourt package-query`，然后再`sudo pacman-db-upgrade`升级一下数据库，然后再`sudo pacman -Syu`，ok问题解决。

还有一点是关于virtualbox的，今天我又用了一下vagrant，发现每次`vagrant up`都会提示：
<pre>
VirtualBox is complaining that the kernel module is not loaded. Please
run `VBoxManage --version` or open the VirtualBox GUI to see the error
message which should contain instructions on how to fix this error.
</pre>
用最简单的方法`sudo modprobe vboxdrv`然后`lsmod | grep vbox`发现加载进了
<pre>
vboxdrv               348377  3 vboxnetadp,vboxnetflt,vboxpci`
</pre>
但是这时候还是会报错说找不到网卡驱动，后来我执行`sudo vboxreload`显示：
<pre>
vboxpci                23139  0
vboxnetflt             27412  0
vboxnetadp             25443  0
vboxdrv               348377  3 vboxnetadp,vboxnetflt,vboxpci
</pre>
这时候才算是真正的把所有的驱动都加进来了。

---
layout: post
title: 学习使用postgresql
date: 2015-07-22 23:46:26 +0800
comments: true
tags: linux,postgresql
---

对mysql有点厌倦了，因为用的人多就会导致产品趋于平凡，就算是mariadb也是一样，所以有必要了解一下postgresql。

首先讲讲postgresql的安装配置吧，我是从[debian官方](https://wiki.debian.org/PostgreSql)上面找到的wiki，
操作很简单，但是首先就感觉postgresql和mysql的设计哲学有点区别。要让一个用户能够访问pgsql的数据库，首先得在
操作系统上面创建对应的用户，然后用postgres这个默认的超级用户去连接默认的postgres数据库，连接上之后再在服务器
里面创建一个同名的用户，然后是创建一个数据库，然后是分配数据库访问权限，感觉这个过程很繁琐而且有点反人类。像
mysql是不需要在操作系统上面创建用户的，直接create user就可以，然后设置密码，分配数据库访问权限。


接下来就是具体如何操作postgresql了，这部分得慢慢来，毕竟我刚学着用。

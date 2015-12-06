---
layout: post
title: 玩弄git
date: 2015-01-05 22:54:11 +0000
comments: true
tags: git docker vagrant
---

经过元旦几天的努力，论坛正式上线了[http://f.joxy.org](http://f.joxy.org)，目前仅仅是使用了[discourse](https://github.com/discourse/discourse)的框架，自己还没有开始定制，总是需要一个熟悉的过程的，本地的开发环境因为邮件的问题，现在还没有好，挺郁闷的。

今晚就稍微熟悉了一下discourse的结构。

1. 我本地的开发环境，这个没什么多说的，就是用vagrant跑了一个虚拟机，上面运行着discourse需要的环境。
2. 线上的环境是用docker运行的，这是官方推荐的安装方式，说实话最初我竟然没有明白discourse的架构。

其实也是很简单的，在我的vps上跑着一个docker，docker类似于一个虚拟机，创建了一个容器，里面跑着discourse需要的环境。代码存放在`/var/www/discourse`下，但是因为是用官方的安装方法安装的，只能等官方的更新，如果我们要自定义怎么办呢？那就需要改git的配置了。

我在github上面fork了官方的仓库，把这个自己的仓库拷贝到本地，改了一些简单的文件，提交，这时候我去官方仓库看，发现他们竟然领先我好多个版本，那就 把他们的改动也拿过来吧，这里需要添加官方仓库的配置，使用`git remote add upstream git://github.com/discourse/discourse.git`，这时候会发现在`.git/config`里面多了一个remote的配置，如果需要把这个upstream的改动也合并到自己的仓库，就要`git fetch upstream`，等改动都更新下来了之后会存放在`.git`目录下，这时候代码还没有变化。如果执行`git pull upstream master`，就能把版本库中的版本检出到主分支了。然后执行`git push origin master`就能把本地的版本更新到github了。

我想把改动也更新到vps上，那就需要改vps上git仓库的配置了。

1. `cd /var/discourse/`
切换到docker配置所在的目录

2. `./launcher ssh app`
因为我当初初始化配置的时候用的是app来作为container的名字，所以要加上app参数

3. `cd /var/www/discourse/`

4. `git remote add develop git://github.com/xcaptain/discourse.git`
这样就把我的github仓库中的版本作为开发版加入到线上的配置了。

5. `git pull develop master`
    就能把我的版本拿下来了，因为我的这个版本都会现在本地开发环境测试过之后再上，所以是很安全的

这样应该也算是和一大伙志愿者协同开发吧，虽然我没有提交pull request，但是我一直在用别人的免费的代码，真是惭愧。

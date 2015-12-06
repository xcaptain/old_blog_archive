---
layout: post
title: 上powerline
date: 2015-06-03 23:27:17 +0800
comments: true
tags: linux
---

看到身边的同事好多都切换到zsh+powerline这样的组合了，总是在我面前炫耀操作git如何方便，用了很久一直没有更新[oh-my-fish](https://github.com/oh-my-fish/oh-my-fish)之后，今晚总算是完全更新了一下配置，向更加高大上的方向进发。

首先是安装[oh-my-fish](https://github.com/oh-my-fish/oh-my-fish)，这步还是很简单的，有一键安装脚本执行。

然后是安装[powerline](https://github.com/powerline/powerline)，这是一个用python写的状态插件，以前我就在vim和emacs上用过，在我的urxvt上面还没有配置过，照着[文档](https://powerline.readthedocs.org/en/latest/)简单配置了一下，也是很简单的，但是最后我发现我的urxvt终端模拟器没法使用默认的powerline字体，后来从[这里](https://github.com/powerline/fonts)找到了几个字体补丁，把我的.Xresources文件修改一下之后就能使用powerline字体了，真是不错。

现在使用的主题是[agnoster](https://github.com/oh-my-fish/theme-agnoster)，功能和配色都挺符合我的胃口的，默认支持powerline，git的状态显示也很不错。

顺便重新配置了一下tmux，以前多任务都是通过urxvt的新建tab来实现的，如果到了别的终端环境如何复用终端呢？还是使用tmux吧，而且也可以配置tmux使得它支持powerline，很不错的setup

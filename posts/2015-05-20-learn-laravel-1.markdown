---
layout: post
title: 简单使用laravel
date: 2015-05-20 23:11:20 +0800
comments: true
tags: php,laravel,web
---

去年的时候就留意过[laravel](http://laravel.com)这个框架了，对于这种类似rails风格的框架很是有好感，但是一直缺少一个实战操作的机会，让我在个人项目上用laravel是不可能的，追求的就是geek，个人项目暂时不考虑php这门语言。

公司有个简单的项目是做一个简单的统计后台，这个需求使用laravel就很简单了，可以省很多事。具体的代码我托管在[github](https://github.com/xcaptain/simple-admin.git)了，想了一下主要的功能包括：

1. 登录注册
2. 权限管理
3. 脚本自动化
4. 渲染页面

登录注册好办，直接使用laravel自带的Auth库就行，自带的示例就内置了一个简单的登录注册页面。权限管理稍微有点麻烦，不过在github上搜了一下也找到好多个基于角色的权限控制的第三方库，我这里用的是[entrust](http://github.com) 脚本自动化指的是后台的数据都来自脚本统计，所以必须要确保脚本能够正确的执行，使用laravel的schedule功能就很方便，可以在代码里面实现定时任务。渲染页面就更加简单了，简单的mvc模式，利用blade模板引擎可以写出很规范的模板。

在这个简单项目中，前端使用的是google开发的[materialcss](http://materialcss.com)，类似与[bootstrap](http://getbootstrap.com)，也是基于网格的设计，很方便初学者使用。前端数据可视化用到的是百度的[echart](http://www.baidu.com/)库。

了解了基本的laravel就能很快上手了，比如说:

`php artisan make:controller ExampleController`可以创建一个控制器。

`php artisan make:model 'Models\Example'`可以创建一个模型，另外还会自动创建一个migrate，因为一个model总是对应着一张表的。

`php artisan make:console UpdateData --command=update:data`会创建一条命令，执行`php artisan list`就能看到这条命令的介绍，去`App\Console\Commands\`下面就能找到对应的类了，要实现什么功能往这个类里面加方法就行了。

`php artisan make:migration create_post_table`会自动在`database/migrations`目录下建立迁移文件，这个文件就是用来建表的，可以很方便的修改模式，而且还能记录开发过程中对表的修改。

`php artisan migrate`会自动查找迁移文件，执行有必要的改动。

`php artisan db:seed`可以自动给测试表填充内容，当然具体的实施得靠人来确定，框架是不可能知道要填入什么数据到哪张表的，对应的文件在`database/seeds/`下。

剩下的一些都是比较简单的，比如使用[Carbon]()来操作时间，使用[Request]()来获得http参数，用query builder来构造查询，用Eloquent来提交/读取model。还有一个比较强大的是laravel的路由功能，但是目前用到的还比较简单。

另外今天看到一篇文章，讲的是为什么laravel要放弃models目录，主要原因说的是对于现代web开发来说，MVC有点不够用了，除了model可以操作数据库，读写数据，但是对于别的业务，比如说发送邮件，请求第三方接口，记录日志等等，如果还是按照mvc来这些文件就不知道放哪里了，将决定权交给用户，真是很方便。

未来要做的事情主要包括：

1. 完善权限管理，目前只做了很简单的权限判断，还没有给admin帐号分配创建用户，创建角色的功能
2. 整理路由配置，目前只做了2个简单的功能，但是路由就有点混乱了，得把页面分得清晰，构造优雅的url规则

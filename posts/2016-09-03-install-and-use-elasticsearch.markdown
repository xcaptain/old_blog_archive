---
title: 安装和配置elasticsearch
tags: web,php,laravel,elasticsearch
---

[elasticsearch](https://www.elastic.co/products/elasticsearch)是一个非常优秀的搜索工具，比数据库的搜索功能好太多了，以下介绍elasticsearch的配置与应用，以下操作在ubuntu 16.04下进行

### 安装
ubuntu 16.04中默认已经使用elasticsearch 5.x的源了，jdk最好使用java8
1. `apt install default-jre default-jdk`
2. `apt install elasticsearch`

### 启动
`systemctl start elasticsearch.service` 如果一切顺利，执行`curl 'http://localhost:9200'`的时候就可以正常返回一个json

### 安装中文分词插件[ik](https://github.com/medcl/elasticsearch-analysis-ik)

1. 安装mvn `apt install maven`
2. 按照github说明安装插件(elasticsearch对插件版本要求比较严格，有时候要自己改插件编译配置)
3. 把编译好的zip文件复制到`/usr/local/share/elasticsearch/plugins/ik`中，删除目录中原有的代码，解压zip包，重启es
4. 重启之后要监控es的日志，避免因为更新插件导致启动失败

### 实际应用
在laravel 5.4中已经集成了[laravel-scout](https://laravel.com/docs/5.4/scout)，所以就不需要像以前那样使用第三方的包了，不过令人伤心的是在[scout v2.0](https://github.com/laravel/scout/tree/2.0)之后，es的支持就被剥离出核心项目了，当然如果想继续使用scout+es，可以使用[laravel-scout-elastic](https://github.com/ErickTamayo/laravel-scout-elastic)，这个库代码很少，包含了基本的api，使用很方便。

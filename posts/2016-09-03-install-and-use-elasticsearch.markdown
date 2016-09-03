---
title: 安装和配置elasticsearch
tags: web,php,laravel,elasticsearch
---

[elasticsearch](https://www.elastic.co/products/elasticsearch)是一个非常优秀的搜索工具，比数据库的搜索功能好太多了，以下介绍elasticsearch的配置与应用，以下操作在ubuntu 16.04下进行

### 安装
默认源安装的是老版本，如果想要用最新版本可以手动添加源

1. `apt install default-jre default-jdk`
2. `wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -`
3. `echo "deb http://packages.elastic.co/elasticsearch/2.x/debian stable main" | tee -a /etc/apt/sources.list.d/elasticsearch-2.x.list`
4. `apt install elasticsearch`

### 启动
`service elasticsearch start` 如果一切顺利，执行`curl 'http://localhost:9200'`的时候就可以正常返回一个json

### 安装中文分词插件[ik](https://github.com/medcl/elasticsearch-analysis-ik)

1. 安装mvn `apt install maven`
2. 按照github说明安装插件(elasticsearch对插件版本要求比较严格，有时候要自己改插件编译配置)

### 实际应用
在laravel/lumen中集成elasticsearch很方便，我喜欢的2个库是[elasticquent/elasticquent](https://github.com/elasticquent/elasticquent)和[nordsoftware/lumen-elasticsearch](https://github.com/nordsoftware/lumen-elasticsearch)

elasticquent用来更新索引很方便，model更新的时候通过[model event](https://laravel.com/docs/5.3/eloquent#events)来同步更新索引，lumen-elasticsearch主要是方便构造查询语句

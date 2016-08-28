---
title: php从mysql中获取真实字段类型
tags: php,mysql,web
---

刚接触新公司代码不熟悉框架，发现Model层返回的数据都是字符串类型的,这可不能忍，在restapi中字段类型是要固定的，不然接口文档不明确，客户端调用时可能也会出问题。

追查了一下发现这边低层连接mysql是用mysqli扩展连接的，主要查询操作是
```php
$result = mysqli_query($handle, $sql);
$row = mysqli_fetch_assoc($result);
mysqli_free_result($result);
```

在这个地方打印 $row变量就是字符串了，导致后续的数据全部都是字符串类型。在php内部使用倒是没什么问题，毕竟php本身是弱类型的语言，字符串数字和纯数字基本没什么区别，但是在接口开发方面就会有很大问题，一个restapi首要就是文档清晰，如果连每个返回值的类型都不能正确给出那么在调用的时候就容易出现不可预测的事情。

在老的代码中总是看到类似`$fieldType`这样的变量用来存放一个表的名称类型映射，但是这样做比较傻，涉及到数据库字段类型变更的时候还得改model。在pdo-mysql驱动中，底层的mysql驱动是mysqlnd，这个比mysqli先进，能够查询出每一行的元数据(metadata)，由一个PDO选项`PDO::ATTR_EMULATE_PREPARES`来控制是否返回真实的类型，具体见下面的例子：
```php
$servername = "localhost";
$username = "root";
$password = "123456";
$conn = new PDO("mysql:host=$servername;dbname=testdb", $username, $password);
$conn->setAttribute(PDO::ATTR_EMULATE_PREPARES, false);
$sql = "select * from test_table where id=1";
$sth = $conn->prepare($sql);
$sth->execute();
$row = $sth->fetch();
```
如果不加`$conn->setAttribute(PDO::ATTR_EMULATE_PREPARES, false);`这一行，返回值都是字符串类型，加了之后就能正确返回类型了。

laravel的eloquent ORM很强大，看了一下底层连接db的代码，在[framework/src/Illuminate/Database/Connectors/Connector.php](https://github.com/laravel/framework/blob/5.3/src/Illuminate/Database/Connectors/Connector.php)设置好的默认选项中就有这个，果然是自己写的代码越少，犯错的机会就越少，框架和软件的设计思想都应该把易用性和可配置性放到第一位，而且要做到把默认值设置为普遍最佳方案，这样开发者才能有更多精力专注业务开发，做更多充满创造力的事情，而不是花几十分钟去搜索这种尝试性的问题。喜欢自己开发框架，但是又写不出好框架的团队是一个差劲的团队。

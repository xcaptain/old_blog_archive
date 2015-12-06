---
layout: post
title: php的getter方法
date: 2015-05-23 15:58:40 +0800
comments: true
tags: php
---

这几天做统计后台频繁用到一个叫做[Carbon](http://carbon.nesbot.com/)的日期库，用多了发现这个库的接口设计得非常方便，方法非常自然，稍微用几次就知道用法了。比如说好多方法都是静态方法，省去了频繁构造对象的开销。最方便的还是里面的getter方法，`$dt->year`就返回了本年，这个year很明显是一个静态方法，因为$dt不是new出来的对象，但是我们看起来就好像是直接访问一个类的属性一样。

```php
class A {
    public function year() {
        return 2015;
    }
    public static function __get($var) {
        return $this->$var();
    }
}
a = new A;
echo a->year;
```
是getter的一种简单写法，挺不错的。

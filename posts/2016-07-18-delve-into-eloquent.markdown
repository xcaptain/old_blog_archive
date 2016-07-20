---
title: eloquent高级用法
tags: php,web
---

常见的关系模型有1对1,1对多，多对多，在一个model中描述这些关系用`hasOne`, `hasMany`, `belongsTo`, `belongsToMany`就够了，但是最近遇到一个新的情况要用到更复杂的关系模型，就是polymorphic relation，按照[wikipedia](https://en.wikipedia.org/wiki/Polymorphic_association) 的说法，这个概念是在ORM开发中提出来的，[laravel](https://laravel.com/docs/5.2/eloquent-relationships)和[rails](http://guides.rubyonrails.org/association_basics.html)中很好的实现了多态关系。第一次遇到这个问题的时候没见过，以为能用自带的1对多关系解决，但是在经过一番尝试之后没法优雅的解决我的问题，所以求助了[stackoverflow](https://stackoverflow.com/questions/38433109/eloquent-belongsto-determine-relation-table)，很快就有网友提示了正确的思路，避免我在错误的道路上越走越远，今天就来总结一下我的问题吧。

在项目中有一些资源表，如games, news, songs，现在的需求要针对每种资源都能进行评论，所以还有一个评论表，但是在评论表中得区分是对什么资源的评论，所以用`commentable_id`和`commentable_type`来标识一个可被评论项实体。最初建表的时候是打算用`item_id`和`item_type`这2个字段的，但是按照laravel和ruby官方文档的建议，我还是改为以`able`结尾的单词了，我的model见下:


```php
class Game
{
    public function comment()
    {
        return $this->morphMany(Comment::class, 'commentable');
    }
}
```

```php
class News
{
    public function comment()
    {
        return $this->morphMany(Comment::class, 'commentable');
    }
}
```

```php
class Comment
{
    public function commentable()
    {
        return $this->morphTo();
    }
    public function getCommentableTypeAttribute($value)
    {
        if ($value == 'App\Model\Game') {
            return 'game';
        } else {
            return 'news';
        }
    }
    public function author()
    {
        return $this->belongsTo(User::class, 'author_id');
    }
}
```

在获取一个用户发布的所有评论时，可以使用
```php
$comments = Comment::where('author_id', '=', $id)
    ->with('commentable')
    ->paginate();
```

通过打印这行的查询日志发现总共有4条查询语句:

1. 查询用户的总评论数，分页时用到
```sql
select count(*) as aggregate from `comments` where `author_id` = ?
```
2. 查询评论本身的信息
```sql
select * from `comments` where `author_id` = ? limit 15 offset 0
```
3. 查询发布评论的人的信息
```sql
select * from `users` where `users`.`id` in (?)
```
4. 查询被评论物的详细信息
```sql
select * from `games` where `games`.`id` in (?) and `status` = ?
```

一行代码对应4条查询，使用eloquent的优点由此可见，省去了很多自己手写查询的精力。

另一个查询这种数据模型的思路是关联查询，
```sql
select c.id as comment_id, c.commentable_id, g.name as title from comments as c left join games as g on c.commentable_id=g.id where c.commentable_type='App\\Model\\Game' union select c.id as comment_id, c.commentable_id, n.title as title from comments as c left join news as n on c.commentable_id=n.id where c.commentable_type='App\\Model\\News';
```
开始是打算动态选择如何管理资源表的，后来尝试之后发现只能用union合并2条查询的结果，手写查询还是不如eloquent优雅。

---
layout: post
title: 一个php分页示例
date: 2014-08-16 17:26:45 +0800
comments: true
tags: php
---

上次做的那个视频站有一个页面被领导否决了，因为之前是使用ajax加载数据的，他们说这种方法
对seo不友好，所以让我重新再写一遍。不得不吐槽这些可恶的领导了，虽然我们只是个小兵，可是
一句话就把我们辛辛苦苦写出来的代码给否决了，怎么说都还是很伤心的。不过伤心归伤心，活
还是的继续干的，谁让我们是给别人打工的呢。

之前的实现方法是用jsonp来做的，我用php写好接口，输出一个回调函数和数据，前端获得这个数据，
然后就能显示出页面了。不过现在要重写只能我自己来处理数据了。

首先遇到一个多级选择的问题，这个问题一点都不是问题，因为既然是反复请求，那么给每一级选项
加上一个参数，然后传给后台就能取出相应的数据了。真正让我比较烦恼的是分页的功能如何实现。

分页貌似简单，实则复杂。如果对于很少的页面当然可以把所有的页码罗列出来，但是如果页面很多，
比如一个分类有100个页面，那么如何显示这100个页面就是个问题了，通常的惯例是显示头几个，当前页
附近几个，和最后几个页面，剩下的用省略号表示。这时候就涉及到一个比较麻烦的问题了，怎么设计
当前的分页结构，使得页面均匀分布。

在网上搜了一段代码，稍微改动了一下加到我的页面中去了，不得不说这段代码的逻辑还是很复杂的，
我甚至都没有仔细去研究为什么要这么做，只是稍微看了一下各种情况对应的代码实现。
<pre>
class BaseFunction {
    /**
     * lol.7k7k.com列表页分页函数
     * 返回一段html代码
     * $page  当前页面号码，如1,2
     * $totalCount: 总共视频数目，根据这个值获得总页数
     * $adjacents:  现实与当前也相邻的几个页面
     * $catname:  分类名，比如zmjs
     * $tagname:   标签名中文
     * $sort: 排序  "/$tagname-$sort-$page/"
     */
    public static function listPager($page, $pageSize, $totalCount, $adjacents, $catname, $tagname, $sort) {
        if($page == 0) $page = 1;                //默认从第一页开始
        $prev = $page - 1;
        $next = $page + 1;
        $lastPage = ceil($totalCount/$pageSize); //最后一页编号
        $lpm1 = $lastPage - 1;                   //倒数第二页编号

        $pagination = '';
        if($lastPage > 1) {
            $pagination .= "<div class=\"pagination\">";
            if($page > 1) {
                $pagination .= "<a href=\"/$catname/$tagname-$sort-$prev/\">上一页</a>";
            } else {
                $pagination .= "<span class=\"disabled\">上一页</span>";
            }
            if($lastPage < 7 + ($adjacents * 2)) { //不需要隐藏额外的页码
                for($counter = 1; $counter <= $lastPage; $counter++) {
                    if($counter == $page) {
                        $pagination .= "<span class=\"curr\">$counter</span>"; //高亮当前页
                    } else {
                        $pagination .= "<a href=\"/$catname/$tagname-$sort-$counter/\">$counter</a>"; //其余页面做成链接
                    }
                }
            } elseif($lastPage > 5 + ($adjacents * 2)) { //页面很多，要隐藏部分页码
                if($page < 1 + ($adjacents * 2)) {       //靠近前面，隐藏后面的页面
                    for($counter = 1; $counter < 4 + ($adjacents * 2); $counter++) {
                        if($counter == $page) {
                            $pagination .= "<span class=\"curr\">$counter</span>";
                        } else {
                            $pagination .= "<a href=\"/$catname/$tagname-$sort-$counter/\">$counter</a>";
                        }
                    }
                    $pagination .= "...";
                    $pagination .= "<a href=\"/$catname/$tagname-$sort-$lpm1/\">$lpm1</a>";
                    $pagination .= "<a href=\"/$catname/$tagname-$sort-$lastPage/\">$lastPage</a>";
                } elseif($lastPage - ($adjacents * 2) > $page && $page > ($adjacents * 2)) { //在中间，前后都隐藏部分
                    $pagination .= "<a href=\"/$catname/$tagname-$sort-1/\">1</a>";
                    $pagination .= "<a href=\"/$catname/$tagname-$sort-2/\">2</a>";
                    $pagination . "...";
                    for($counter = $page - $adjacents; $counter <= $page + $adjacents; $counter++) {
                        if($counter == $page) {
                            $pagination .= "<span class=\"curr\">$counter</span>";
                        } else {
                            $pagination .= "<a href=\"/$catname/$tagname-$sort-$counter/\">$counter</a>";
                        }
                    }
                } else { //靠近尾部，隐藏前面部分
                    $pagination .= "<a href=\"/$catname/$tagname-$sort-1/\">1</a>";
                    $pagination .= "<a href=\"/$catname/$tagname-$sort-2/\">2</a>";
                    $pagination .= "...";
                    for($counter = $lastPage - (2 + ($adjacents * 2)); $counter <= $lastPage; $counter++) {
                        if($counter == $page) {
                            $pagination .= "<span class=\"/$catname/$tagname-$sort-$counter/\">$counter</span>";
                        } else {
                            $pagination .= "<a href=\"/$catname/$tagname-$sort-$counter/\">$counter</a>";
                        }
                    }
                }
            } //end elseif 对于很长的页面情况判断结束
            if($page < $counter - 1) {
                $pagination .= "<a href=\"/$catname/$tagname-$sort-$next/\">下一页</a>";
            } else {
                $pagination .= "<span class=\"disabled\">下一页</span>";
            }
            $pagination .= "</div>";
            return $pagination;
        } else {  //对于只有一页的情况，也做一个分页
            $pagination .= "<div class=\"pagination\">";
            $pagination .= "<span class=\"disabled\">上一页</span>";
            $pagination .= "<span class=\"curr\">1</span>";
            $pagination .= "<span class=\"disabled\">上一页</span>";
            $pagination .= "</div>";
            return $pagination;
        }
    }
}
</pre>
在我的网站上面试验了一下，基本符合我的要求，不过难怪php和javascript都要做一个分页类，手动实现分页真是太累了。还好有人共享代码，不然自己写有得写了。

OMG,竟然把我代码里的html自动解析了，这让我怎么插入html代码啊。

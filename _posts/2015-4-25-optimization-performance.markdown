---
layout: post
keywords: blog
description: blog
title: "Android性能优化"
categories: [Optimization]
tags: [优化]
---
{% include codepiano/setup %}

## Android性能调优

[http://www.trinea.cn/android/android-performance-demo/](http://www.trinea.cn/android/android-performance-demo/)

## 概念

* 1.响应时间

* 2.TPS(Transaction per second)每秒处理的事务

&emsp;&emsp;在Android应用程序中由于系统ANR的限制，所以对主线程的响应时间提出了更高的要求。Android ANR的具体要求是指Activity对事件响应不超过5秒，BroadcastReceiver中执行时间不超过10秒。
 
## 性能调优方式
明白了何为性能问题之后，就能明白性能优化实际就是优化系统的响应时间，提高TPS。优化响应时间，提高TPS的方式包括：

* 1.降低执行时间

&emsp;&emsp;这部分包括：a. 缓存(包括对象缓存、IO缓存、网络缓存), b. 数据存储类型优化, c. 算法优化, d. JNI, e. 逻辑优化, f. 需求优化

* 2.同步改异步，利用多线程提高TPS

* 3.提前或延迟操作，错开时间段提高TPS


## Android 布局优化

###  1.<include\>标签
include标签常用于将布局中的公共部分提取出来供其他layout共用，以实现布局模块化，这在布局编写方便提供了大大的便利。

###  2.<viewstub\>标签
viewstub标签同include标签一样可以用来引入一个外部布局，不同的是，viewstub引入的布局默认不会扩张，即既不会占用显示也不会占用位置，从而在解析layout时节省cpu和内存。

viewstub常用来引入那些默认不会显示，只在特殊情况下显示的布局，如进度布局、网络失败显示的刷新布局、信息出错出现的提示布局等。

###  3.<merge\>标签
在使用了include后可能导致布局嵌套过多，多余不必要的layout节点，从而导致解析变慢，不必要的节点和嵌套可通过hierarchy viewer(下面布局调优工具中有具体介绍)或设置->开发者选项->显示布局边界查看。
 
merge标签可用于两种典型情况：

a.  布局顶结点是FrameLayout且不需要设置background或padding等属性，可以用merge代替，因为Activity内容试图的parent view就是个FrameLayout，所以可以用merge消除只剩一个。

b.  某布局作为子布局被其他布局include时，使用merge当作该布局的顶节点，这样在被引入时顶结点会自动被忽略，而将其子节点全部合并到主布局中。

## 代码过几天贴上
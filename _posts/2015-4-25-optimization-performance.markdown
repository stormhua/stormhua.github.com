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

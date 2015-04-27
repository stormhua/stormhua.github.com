---
layout: post
keywords: blog
description: blog
title: "Java线程池"
categories: [Optimization]
tags: [Thread]
---
{% include codepiano/setup %}

## Java(Android)线程池

[http://www.trinea.cn/android/java-android-thread-pool/](http://www.trinea.cn/android/java-android-thread-pool/)

## new Thread弊端

* 1.每次new Thread新建对象性能差。

* 2.线程缺乏统一管理，可能无限制新建线程，相互之间竞争，及可能占用过多系统资源导致死机或oom。

* 3.缺乏更多功能，如定时执行、定期执行、线程中断。

相比new Thread，Java提供的四种线程池的好处在于：

* 1.重用存在的线程，减少对象创建、消亡的开销，性能佳。

* 2.可有效控制最大并发线程数，提高系统资源的使用率，同时避免过多资源竞争，避免堵塞。

* 3.提供定时执行、定期执行、单线程、并发数控制等功能。

### 1.newCachedThreadPool

### 2.newFixedThreadPool

### 3.newScheduledThreadPool

### 4.newSingleThreadExecutor
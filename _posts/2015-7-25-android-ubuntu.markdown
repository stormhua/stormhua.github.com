---
layout: post
keywords: blog
description: blog
title: "Ubuntu在win7下安装教程"
categories: [android]
tags: [Ubuntu、win7]
---
{% include codepiano/setup %}

## Ubuntu在win7下安装教程

### 安装背景
最近一直在使用Android Studio 。感觉比Eclipse开发要快很多！我的电脑内存是4g，i5的，128G的ssd。我本来以为运行Android studio+genymotion不成问题，可是每一运行内存就飙到百分之九十几！！！搞得好几次有加类存条的冲动。后来没办法就试试Linux。开始几天被各种命令搞晕了。。但是慢慢有点习惯，也开始喜欢这Ubuntu。 我用的是Ubuntu 优麒麟！下面就简单介绍下它的安装吧！

### 步骤

* 1.准备工作--> esaybcd--> ubuntu镜像--> 100G空闲空间(用于ubuntu的安装.根据自己情况而定)

* 2.打开esaybcd 添加新条目 NeoGrub 安装 配置

* 3.
{% highlight java %} 
title Install Ubuntu
root (hd0,0)
kernel (hd0,0)/vmlinuz boot=casper iso-scan/filename=/ubuntu-15.04-desktop-i386.iso ro quiet splash locale=zh_CN.UTF-8
initrd (hd0,0)/initrd.lz
{% endhighlight %}

**注意**

*(hd0,0)第一个代表磁盘,第二代表第几个区*

*ubuntu-15.04-desktop-i386.iso是你镜像的名称*

* 4.将镜像文件移动到c盘根目录下，找到casper文件夹，复制里面的initrd.lz和vmlinuz到C盘，把.disk文件夹也复制到C盘。重启进入NeoGrub加载器。开始安装Ubuntu.


### 总结
*未完待续。。接下来会给大家更新Ubuntu下Android Studio 的环境搭建。。*








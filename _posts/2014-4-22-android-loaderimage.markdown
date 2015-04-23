---
layout: post
keywords: blog
description: blog
title: " Android高效加载大图"
categories: [android]
tags: [oom]
---
{% include codepiano/setup %}

## Android高效加载大图、多图解决方案，有效避免程序OOM(out of memery)

* 1.我们的摄像头拍摄照片的分辨率要比我们手机屏幕分辨率高

* 2.每个应用程序都是有内存限制的、

### 查看每个应用程序最高可用内存

{% highlight java %}
int maxMemory = (int) (Runtime.getRuntime().maxMemory() / 1024);
Log.d("TAG", "Max memory is " + maxMemory + "KB");
{% endhighlight %}
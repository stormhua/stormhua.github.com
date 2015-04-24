---
layout: post
keywords: blog
description: blog
title: "Android缓存"
categories: [android]
tags: [ASimpleCache]
---
{% include codepiano/setup %}

## Android缓存

## 轻量缓存框架----ASimpleCache

### ASimpleCache可以缓存哪些东西

1. 普通的字符串

1. JsonObject

1. JsonArray

1. Bitmap

1. Drawable

1. 序列化的java对象

1. byte数据

## ASimpleCache的示例代码

{% highlight java%}
ACache mCache = ACache.get(this);
mCache.put("test_key1", "test value");
mCache.put("test_key2", "test value", 10);//保存10秒，如果超过10秒去获取这个key，将为null
mCache.put("test_key3", "test value", 2 * ACache.TIME_DAY);//保存两天，如果超过两天去获取这个key，将为null
{% endhighlight %}

### 获取缓存数据：

{% highlight java %}
ACache mCache = ACache.get(this);
String value = mCache.getAsString("test_key1");
{% endhighlight %}

[开源地址](https://github.com/yangfuhai/ASimpleCache)
---
layout: post
keywords: blog
description: blog
title: "GSON库--解析JSON数据"
categories: [开源库]
tags: [gson]
---
{% include codepiano/setup %}

[GSON资源包](http://code.google.com/p/google-gson/downloads/list)
[jar包](http://download.csdn.net/detail/guanjianwoshinidaye/8625281)

## 解析jason数据

### JSON格式数据如下

{"name":"Tom","age":20}

那我们就可以定义一个Person 类，并加入name 和age 这两个字段，然后只需简单地调用如下代码就可以将JSON 数据自动解析成一个Person 对象了：

{% highlight java %}
Gson gson = new Gson();
Person person = gson.fromJson(jsonData, Person.class);
{% endhighlight %}

如果需要解析的是一段JSON 数组会稍微麻烦一点，我们需要借助TypeToken 将期望解
析成的数据类型传入到fromJson()方法中，如下所示：

{% highlight java %}
List<Person> people = gson.fromJson(jsonData, new TypeToken<List<Person>>()
{}.getType());
{% endhighlight %}
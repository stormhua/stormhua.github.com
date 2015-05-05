---
layout: post
keywords: blog
description: blog
title: "Android 网络编程--socket tcp/ip之间的关系"
categories: [android]
tags: [tcp ip]
---
{% include codepiano/setup %}


##  android 网络编程--socket tcp/ip udp http之间的关系

### 区别

- 应用层协议：Http

- 网络层协议：ip

- 运输层协议：Tcp Udp（不可靠的协议，只负责把应用层的协议的数据传送到ip层的数据报，而不管数据是否到达。）

- socket:长连接

- http：短连接

---

### 理解

<img src="/image/tcp.png"/>




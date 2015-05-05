---
layout: post
keywords: blog
description: blog
title: "Android 网络编程--socket tcp/ip之间的关系"
categories: [android]
tags: [tcp ip]
---
{% include codepiano/setup %}


##  android 网络编程->socket tcp/ip udp http之间的关系

### 区别

- 应用层协议：Http

- 网络层协议：ip

- 运输层协议：Tcp Udp（不可靠的协议，只负责把应用层的协议的数据传送到ip层的数据报，而不管数据是否到达。）

- socket:长连接

- http：短连接

---

### 理解

<img src="/image/tcp.png"/>

Tcp/ip通信协议是一种可靠的网络协议，它在通信两端各建立一个Socket，从而在通信的两端形成网络虚拟链路。一旦建立了虚拟的网路链路，两端的程序就可以通过虚拟链路来进行通信，Java对基于TCP协议的网络通信提供了良好的封装，Java使用Socket对象来代表两端的通信接口，并通过Socket产生I/O流来进行网络通信。

- IP只保证计算机能接收和发送分组数据。

- TCP可以帮助IP协议提供可靠并且无差错的通信服务（端对端协议）

TCP协议负责收集这谢信息包，并将按适当的次序放好传送，在接收端接到后在将其正确的还原，TCP保证了数据包在传送中准确无误，TCP协议使用重发机制





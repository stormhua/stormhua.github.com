---
layout: post
keywords: blog
description: blog
title: "第一行代码"
categories: [android]
tags: [第一行代码]
---
{% include codepiano/setup %}

## 第一行代码Git使用 ##
### &nbsp;&nbsp; Windows 教程: ###

1. 配置身份 

		- git config --global user.name "stormhua"
		-git config --global user.email "stormhua@gmail.com"


1. 创建代码仓库

		-git init	//先进入项目目录下

1. 提交本地代码
		
		-git add src
		-git add .	//添加当前目录的所有文件
		-git add AndroidMainfest.xml
		-git commit -m "First commit"

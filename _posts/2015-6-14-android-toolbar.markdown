---
layout: post
keywords: blog
description: blog
title: "Android 5.x toolbar"
categories: [android]
tags: [toolbar、5.0]
---
{% include codepiano/setup %}

## Android Toolbar

### 背景
以前一般用actionbar，文字不能随意设置，不能自己自定义添加自己的布局。记得以前github上还有一个很火的项目[ActionBarSherlock](https://github.com/JakeWharton/ActionBarSherlock)，但现在5.0有一个新的，叫toolbar。完全代替了actionbar。

### 基本步骤

* 1.为了能在你的Activity中使用Toolbar，你必须在工程里修改styles.xml文件里的主题风格，系统默认如下








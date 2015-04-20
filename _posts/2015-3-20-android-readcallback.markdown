---
layout: post
keywords: blog
description: blog
title: "第一行代码"
categories: [android]
tags: [第一行代码]
---
{% include codepiano/setup %}

## 第一行代码Git使用 

##  Windows 教程: 

* 1.配置身份

		-git config --global user.name "stormhua"
		-git config --global user.email "stormhua@gmail.com"


* 2.创建代码库

		-git init	//先进入项目目录下

* 3.提交本地代码
		
		-git add src
		-git add .	//添加当前目录的所有文件
		-git add AndroidMainfest.xml
		-git commit -m "First commit"

----------

## 忽略文件

&nbsp;&nbsp;Git 提供了一种可配性很强的机制来允许用户将指定的文件或目录排除在版本控制之
外，它会检查代码仓库的根目录下是否存在一个名为.gitignore 的文件，如果存在的话就去一
行行读取这个文件中的内容，并把每一行指定的文件或目录排除在版本控制之外。注意.gitignore 中指定的文件或目录是可以使用“*”通配符的。

## 查看修改内容 

查看文件修改情况的方法非常简单，只需要使用status 命令就可以了，在项目的根目录
下输入如下命令：

	git status

那么如何才能看到更改的内容呢？这就需要借助diff 命令了，用法如下所示：

	git diff

这样可以查看到所有文件的更改内容，如果你只想查看MainActivity.java 这个文件的更
改内容，可以使用如下命令：

	git diff src/com/example/providertest/MainActivity.java

## 撤销未提交的修改 

	git checkout src/com/example/providertest/MainActivity.java

当然不是，只不过对于已添加的文件我们应该先对其取消添加，然后才可以撤回提交。取消添加使用的是reset 命令，用法如下所示：

	git reset HEAD src/com/example/providertest/MainActivity.java

然后再运行一遍git status 命令，你就会发现MainActivity.java 这个文件重新变回了未添
加状态，此时就可以使用checkout 命令来将修改的内容进行撤销了

## 查看提交记录 
	git log

当提交记录非常多的时候，如果我们只想查看其中一条记录，可以在命令中指定该记录
的id，并加上-1 参数表示我们只想看到一行记录，如下所示：

	git log 2e7c0547af28cc1e9f303a4a1126fddbb704281b -1
而如果想要查看这条提交记录具体修改了什么内容，可以在命令中加入-p 参数，命令如下：

	git log 2e7c0547af28cc1e9f303a4a1126fddbb704281b -1 –p

----------

## 分支的用法 

## 查看分支 

	git branch –a

## 创建分支 

	git branch version1.0
将修改的代码一行行复制到master 分支上显然不是一种聪明的做法，最好的办法就是使用merge
命令来完成合并操作，

	git checkout master
	git merge version1.0

## 删除分支 

	git branch -D version1.0

## 与远程版本库协作

比如说现在有一个远程版本库的Git 地址是https://github.com/exmaple/test.git，就可以使
用如下的命令将代码下载到本地：

	
git clone https://github.com/exmaple/test.git
之后你在这份代码的基础进行了一些修改和提交，那么怎样才能把本地修改的内容同步
到远程版本库上呢？这就需要借助push 命令来完成了，用法如下所示：

	git push origin master
其中origin 部分指定的是远程版本库的Git 地址，master 部分指定的是同步到哪一个分
支上，上述命令就完成了将本地代码同步到https://github.com/exmaple/test.git 这个版本库的
master 分支上的功能。知道了将本地的修改同步到远程版本库上的方法，接下来我们看一下如何将远程版本库上的修改同步到本地。Git 提供了两种命令来完成此功能，分别是fetch 和pull，fetch 的语法规则和push 是差不多的，如下所示：

	git fetch origin master
执行这个命令后，就会将远程版本库上的代码同步到本地，不过同步下来的代码并不会
合并到任何分支上去，而是会存放在到一个origin/master 分支上，这时我们可以通过diff 命令来查看远程版本库上到底修改了哪些东西：

	git diff origin/master
之后再调用merge 命令将origin/master 分支上的修改合并到主分支上即可，如下所示：

	git merge origin/master
而pull 命令则是相当于将fetch 和merge 这两个命令放在一起执行了，它可以从远程版本库上获取最新的代码并且合并到本地，用法如下所示：

	git pull origin master
也许你现在对远程版本库的使用还会感觉比较抽象，没关系，因为暂时我们只是了解了
一下命令的用法，还没进行实践
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

{% highlight java %}
 <style name="AppTheme" parent="AppTheme.Base" />
    <!-- Base application theme. -->
    <style name="AppTheme.Base" parent="Theme.AppCompat.NoActionBar">
        <item name="actionBarPopupTheme">@style/ThemeOverlay.AppCompat</item>
        <item name="android:windowNoTitle">true</item>
        <item name="windowActionModeOverlay">true</item>
        <item name="actionOverflowMenuStyle">@style/OverflowMenuStyle</item>
        <!-- Customize your theme here. -->
        <!--导航栏底色-->
        <item name="colorPrimary">@color/colorPrimary</item>
        <!--状态栏底色-->
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <!--导航栏上的标题颜色-->
        <item name="android:textColorPrimary">@color/textColorPrimary</item>
        <!--Activity窗口的颜色-->
        <item name="android:windowBackground">@color/windowBackground</item>
        <!--EditText 输入框中字体的颜色-->
        <item name="editTextColor">@android:color/white</item>
        <item name="tagViewStyle">@style/MyTagViewStyle</item>
    </style>
{% endhighlight %}


* 2.build.gradle设置如下

{% highlight java %}
compileSdkVersion 22
buildToolsVersion “22.0.1”
compile ‘com.android.support:appcompat-v7:22.1.1’
{% endhighlight %}

* 3.效果如下
<img src="/image/toolbar.png"/>







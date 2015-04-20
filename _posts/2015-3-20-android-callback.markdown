---
layout: post
keywords: blog
description: blog
title: "java回调机制"
categories: [android]
tags: [回调]
---
{% include codepiano/setup %}

## 对java回调机制的理解

有一天小王遇到一个很难的问题，问题是“1 + 1 = ?”，就打电话问小李，小李一下子也不知道，就跟小王说，等我办完手上的事情，就去想想答案，小王也不会傻傻的拿着电话去等小李的答案吧，于是小王就对小李说，我还要去逛街，你知道了答案就打我电话告诉我，于是挂了电话，自己办自己的事情，过了一个小时，小李打了小王的电话，告诉他答案是2

* Class A实现接口CallBack callback——背景1

* class A中包含一个class B的引用b ——背景2

* class B有一个参数为callback的方法f(CallBack callback) ——背景3

* A的对象a调用B的方法 f(CallBack callback) ——A类调用B类的某个方法 C

* 然后b就可以在f(CallBack callback)方法中调用A的方法 ——B类调用A类的某个方法D

## 1.异步回调

**从网上异步下载图片**

{% highlight java %}
    //接口的回调方式
    public interface ImageCallBack {
        public void getImage(Drawable drawable);
    }
//class B
 public void loadImage(final ImageCallBack imageCallBack) {
        final Handler handler = new Handler() {
            @Override
            public void handleMessage(Message msg) {
                super.handleMessage(msg);
                Drawable drawable= (Drawable) msg.obj;
                imageCallBack.getImage(drawable);
            }

        };
//相当于实现背景一、二、这里用的匿名内部类class A
pdownLoadImage.loadImage(new DownLoadImage.ImageCallBack() {
                @Override
                public void getImage(Drawable drawable) {
                    imageView.setImageDrawable(drawable);
                }
            });
                }
{% endhighlight %}

## 2.同步回调

按钮的setOnClickListener();

[http://blog.csdn.net/xiaanming/article/details/8703708](http://blog.csdn.net/xiaanming/article/details/8703708 "参考")
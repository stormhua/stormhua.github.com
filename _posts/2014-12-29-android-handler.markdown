---
layout: post
keywords: blog
description: blog
title: "Handler+Message+MessageQueue+Looper"
categories: [Android]
tags: [handler]
---
{% include codepiano/setup %}
### 介绍
1.**Handler在面试题里经常出现**

2.**更容易要你了解多线程和子线程之间的问题,加载图片用了接 口回调方法**

3.**此案例中也用到了面试题经常出现的listview**

4.**这是一个自动从网络加载图片和文字的**

### 代码块
{% highlight ruby %}
     public void loadImage(final ImageCallBack imageCallBack) {
            final Handler handler = new Handler() {
                @Override
                public void handleMessage(Message msg) {
                    super.handleMessage(msg);
                    Drawable drawable= (Drawable) msg.obj;
                    imageCallBack.getImage(drawable);
                }
    
            };
      //接口的回调方式
            public interface ImageCallBack {
                public void getImage(Drawable drawable);
            }
{% endhighlight %}

$$  接口回调的实现$$

{% highlight ruby %}

	public class MyTask extends AsyncTask<String, Void, List<Map<String, Object>>> {
	protected void onPreExecute() {}
	protected void onPostExecute(List<Map<String, Object>> maps) {}
	 protected List<Map<String, Object>> doInBackground(String... params) {}
{% endhighlight %}

$$异步执行$$

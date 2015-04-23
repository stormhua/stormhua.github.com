---
layout: post
keywords: blog
description: blog
title: "Volley的使用"
categories: [开源库]
tags: [volley]
---
{% include codepiano/setup %}

##  Android 网络通信框架Volley简介(Google IO 2013)

## Volley提供的功能

[jar包下载](http://download.csdn.net/detail/guanjianwoshinidaye/8620709)

* 1.JSON，图像等的异步下载

* 2.网络请求的排序（scheduling）

* 3.网络请求的优先级处理

* 4.缓存

* 5.多级别取消请求

* 6.和Activity生命周期的联动（Activity结束时同时取消所有网络请求）

## StringRequest解析

{% highlight java %}
StringRequest stringRequest = new StringRequest(Method.POST, url,
				new Response.Listener() {
					@Override
					public void onResponse(Object arg0) {
						// TODO Auto-generated method stub
						Log.d(TAG, arg0.toString());
						judgmentLoginResult(context, arg0.toString());
					}
				}, new Response.ErrorListener() {
					@Override
					public void onErrorResponse(VolleyError arg0) {
						// TODO Auto-generated method stub
					}
				}) {
			@Override
			protected Map<String, String> getParams() throws AuthFailureError {
				// TODO Auto-generated method stub
				Map<String, String> map = new HashMap<String, String>();
				map.put("pn", userTel);
				map.put("pw", password);
				return map;
			}

		};
		queue.add(stringRequest);
		queue.start();
{% endhighlight %}
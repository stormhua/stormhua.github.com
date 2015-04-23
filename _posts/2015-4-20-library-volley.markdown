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
RequestQueue queue = Volley.newRequestQueue(context);
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

## JasonObject解析

{% highlight java %}
	RequestQueue queue = Volley
						.newRequestQueue(splashactivity.this);
				String url = "http://m.weather.com.cn/data/101010100.html";
				JsonObjectRequest jsonObjectRequest = new JsonObjectRequest(
						Method.POST, url, null,
						new Response.Listener<JSONObject>() {
							@Override
							public void onResponse(JSONObject response) {
								Log.d("TAG", response.toString());

							}
						}, new Response.ErrorListener() {
							@Override
							public void onErrorResponse(VolleyError error) {
								Log.e("TAG", error.getMessage(), error);
							}

						});
				queue.add(jsonObjectRequest);
				queue.start();
{% endhighlight %}


----------

## 使用Volley加载网络图片
[ Android Volley完全解析(二)，使用Volley加载网络图片](http://blog.csdn.net/guolin_blog/article/details/17482165)

## 设置网络超时

覆写request的方法

{% highlight java %}
					@Override
					public RetryPolicy getRetryPolicy() {
						// TODO Auto-generated method stub
						RetryPolicy retryPolicy = new DefaultRetryPolicy(Contast.SOCKET_TIMEOUT, DefaultRetryPolicy.DEFAULT_MAX_RETRIES, DefaultRetryPolicy.DEFAULT_BACKOFF_MULT);  
						return retryPolicy;
					}
{% endhighlight %}

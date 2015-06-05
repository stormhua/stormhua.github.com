---
layout: post
keywords: blog
description: blog
title: "EventBus使用"
categories: [android]
tags: [消息；事件]
---
{% include codepiano/setup %}

## EventBus使用

### 自我理解

当我们在Activity、Fragment、service、线程之间传递消息时。我们一般用的是Intent、Bundle、Handle、Broadcast。有时候用Intent传对象时还得先Serializable对象。EventBus优点很多

* 1.开销小

* 2.代码简洁

* 3.解耦性强

*还有一款事件总线是otto,暂时没用过*

### 代码演示

* 1.EventBus项目：[https://github.com/greenrobot/EventBus](https://github.com/greenrobot/EventBus)

* 2.

{% highlight java %}

 @Override
    protected void onStart() {
        super.onStart();
        EventBus.getDefault().register(this);
    }

    @Override
    protected void onStop() {
        super.onStop();
        EventBus.getDefault().unregister(this);
    }
{% endhighlight %}

* 3.在onclick中


{% highlight java %}

EventBus.getDefault().post(new Event("真的可以收到啊！！哈哈"));
{% endhighlight %}

* 4.

{% highlight java %}

 //事件2接收者：在主线程接收自定义MsgBean消息
    public void onEvent(MsgBean event){
        tv_event.setText(event.getMsg());
    }
{% endhighlight %}


### 注意事项
- onEvent：事件的处理在和事件的发送在相同的线程，所以事件处理时间不应太长，不然影响事件的发送线程。

- onEventMainThread: 事件的处理会在UI线程中执行。事件处理时间不能太长，长了会出现臭名远之的ANR。

- onEventBackgroundThread：事件的处理会在一个后台线程中执行。虽然名字是BackgroundThread，事件处理是在后台线程，但事件处理时间还是不应该太长，因为如果发送事件的线程是后台线程，会直接在当前后台线程执行事件；如果当前线程是UI线程，事件会被加到一个队列中，由一个线程依次处理这些事件，如果某个事件处理时间太长，会阻塞后面的事件的派发或处理。

- onEventAsync：事件处理会在单独的线程中执行，主要用于在后台线程中执行耗时操作，每个事件会开启一个线程，但最好限制线程的数目。



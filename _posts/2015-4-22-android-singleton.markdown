---
layout: post
keywords: blog
description: blog
title: "单例模式"
categories: [android]
tags: [singleton]
---
{% include codepiano/setup %}

## 单例模式

## singleton单例设计模式
&emsp;&emsp;这个设计模式主要目的是想在整个系统中只能出现一个类的实例。这样做当然是有必然的，比如你的软件的全局配置信息，或者是一个Factory，或是一个主控类，等等。你希望这个类在整个系统中只能出现一个实例.

单例模式的要点有三个：一是某个类只能有一个实例；而是必须自行创建整个实例；三是它必须自行向整个系统提供整个实例。

* 1.饿汉式

{% highlight java %}
public class Singleton{
private static fianl Singleton singleton=new Singleton();
private Singleton(){
	}
public static Singleton get(){
return singleton
	}
}
{% endhighlight %}


* 2.懒汉式

｛% highlight java %｝
// 这种方式在需要使用的时候才实例化
public class Singleton {
    private static Singleton singleton;

    // 构造函数私有化
    private Singleton() {

    }

    // 提供一个全局的静态方法
    public static Singleton get() {
        if (singleton == null) {
            singleton = new Singleton();
        }
        return singleton;
    }
}
{% endhighlight %}


## 问题
上面的这个程序存在比较严重的问题，因为是全局性的实例，所以，在多线程情况下，所有的全局共享的东西都会变得非常的危险，这个也一样，在多线程情况下，如果多个线程同时调用getInstance()的话，那么，可能会有多个进程同时通过 (singleton== null)的条件检查，于是，多个实例就创建出来，并且很可能造成内存泄露问题。嗯，熟悉多线程的你一定会说——“我们需要线程互斥或同步”。

{% highlight java %}
public class Singleton{
	public static Singleton singleton=null;
	private Singleton(){

	}
	public static Singleton getInstance(){
		synchronized(Singleton.class){
			if(singleton==null){
			singleton=new Singleton();
		}
		}
		return singleton;
	}
}
{% endhighlight %}

注意，创建对象的动作只有一次，后面的动作全是读取那个成员变量，这些读取的动作不需要线程同步啊。这样的作法感觉非常极端啊，为了一个初始化的创建动作，居然让我们达上了所有的读操作，严重影响后续的性能啊!

主要在于singleton = new Singleton()这句，这并非是一个原子操作，事实上在 JVM 中这句话大概做了下面 3 件事情。

给 singleton 分配内存
调用 Singleton 的构造函数来初始化成员变量，形成实例
将singleton对象指向分配的内存空间（执行完这步 singleton才是非 null 了）
但是在 JVM 的即时编译器中存在指令重排序的优化。也就是说上面的第二步和第三步的顺序是不能保证的，最终的执行顺序可能是 1-2-3 也可能是 1-3-2。如果是后者，则在 3 执行完毕、2 未执行之前，被线程二抢占了，这时 instance 已经是非 null 了（但却没有初始化），所以线程二会直接返回 instance，然后使用，然后顺理成章地报错。

对此，我们只需要把singleton声明成 volatile 就可以了。下面是1.4版：
{% highlight java %}
// version 1.4
public class Singleton
{
    private volatile static Singleton singleton = null;
    private Singleton()  {    }
    public static Singleton getInstance()   {
        if (singleton== null)  {
            synchronized (Singleton.class) {
                if (singleton== null)  {
                    singleton= new Singleton();
                }
            }
        }
        return singleton;
    }
}
{% endhighlight %}

使用 volatile 有两个功用：

1）这个变量不会在多个线程中存在复本，直接从内存读取。

2）这个关键字会禁止指令重排序优化。也就是说，在 volatile 变量的赋值操作后面会有一个内存屏障（生成的汇编代码上），读操作不会被重排序到内存屏障之前。


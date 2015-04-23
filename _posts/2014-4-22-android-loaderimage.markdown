---
layout: post
keywords: blog
description: blog
title: " Android高效加载大图"
categories: [android]
tags: [oom]
---
{% include codepiano/setup %}


## Android高效加载大图、多图解决方案，有效避免程序OOM(out of memery)

* 1.我们的摄像头拍摄照片的分辨率要比我们手机屏幕分辨率高

* 2.每个应用程序都是有内存限制的、

### 查看每个应用程序最高可用内存

{% highlight java %}
int maxMemory = (int) (Runtime.getRuntime().maxMemory() / 1024);
Log.d("TAG", "Max memory is " + maxMemory + "KB");
{% endhighlight %}

&emsp;&emsp;BitmapFactory这个类提供了多个解析方法(decodeByteArray, decodeFile, decodeResource等)用于创建Bitmap对象，我们应该根据图片的来源选择合适的方法。比如SD卡中的图片可以使用decodeFile方法，网络上的图片可以使用decodeStream方法，资源文件中的图片可以使用decodeResource方法。这些方法会尝试为已经构建的bitmap分配内存，这时就会很容易导致OOM出现。为此每一种解析方法都提供了一个可选的BitmapFactory.Options参数，将这个参数的inJustDecodeBounds属性设置为true就可以让解析方法禁止为bitmap分配内存，返回值也不再是一个Bitmap对象，而是null。虽然Bitmap是null了，但是BitmapFactory.Options的outWidth、outHeight和outMimeType属性都会被赋值。这个技巧让我们可以在加载图片之前就获取到图片的长宽值和MIME类型，从而根据情况对图片进行压缩。如下代码所示：

{% highlight java %}
BitmapFactory.Options options = new BitmapFactory.Options();
options.inJustDecodeBounds = true;
BitmapFactory.decodeResource(getResources(), R.id.myimage, options);
int imageHeight = options.outHeight;
int imageWidth = options.outWidth;
String imageType = options.outMimeType;
{% endhighlight %}


&emsp;&emsp;那我们怎样才能对图片进行压缩呢？通过设置BitmapFactory.Options中inSampleSize的值就可以实现。比如我们有一张2048\*1536像素的图片，将inSampleSize的值设置为4，就可以把这张图片压缩成512\*384像素。原本加载这张图片需要占用13M的内存，压缩后就只需要占用0.75M了(假设图片是ARGB_8888类型，即每个像素点占用4个字节)。下面的方法可以根据传入的宽和高，计算出合适的inSampleSize值：

{% highlight java %}
public static int calculateInSampleSize(BitmapFactory.Options options,
		int reqWidth, int reqHeight) {
	// 源图片的高度和宽度
	final int height = options.outHeight;
	final int width = options.outWidth;
	int inSampleSize = 1;
	if (height > reqHeight || width > reqWidth) {
		// 计算出实际宽高和目标宽高的比率
		final int heightRatio = Math.round((float) height / (float) reqHeight);
		final int widthRatio = Math.round((float) width / (float) reqWidth);
		// 选择宽和高中最小的比率作为inSampleSize的值，这样可以保证最终图片的宽和高
		// 一定都会大于等于目标的宽和高。
		inSampleSize = heightRatio < widthRatio ? heightRatio : widthRatio;
	}
	return inSampleSize;
}
{% endhighlight %}

&emsp;&emsp;使用这个方法，首先你要将BitmapFactory.Options的inJustDecodeBounds属性设置为true，解析一次图片。然后将BitmapFactory.Options连同期望的宽度和高度一起传递到到calculateInSampleSize方法中，就可以得到合适的inSampleSize值了。之后再解析一次图片，使用新获取到的inSampleSize值，并把inJustDecodeBounds设置为false，就可以得到压缩后的图片了。

{% highlight java %}
public static Bitmap decodeSampledBitmapFromResource(Resources res, int resId,
        int reqWidth, int reqHeight) {
	// 第一次解析将inJustDecodeBounds设置为true，来获取图片大小
    final BitmapFactory.Options options = new BitmapFactory.Options();
    options.inJustDecodeBounds = true;
    BitmapFactory.decodeResource(res, resId, options);
    // 调用上面定义的方法计算inSampleSize值
    options.inSampleSize = calculateInSampleSize(options, reqWidth, reqHeight);
    // 使用获取到的inSampleSize值再次解析图片
    options.inJustDecodeBounds = false;
    return BitmapFactory.decodeResource(res, resId, options);
}
{% endhighlight %}

## 使用图片缓存技术

* 1.ListView,GirdView,ViewPager 屏幕上显示的图片可以通过滑动屏幕等事件不断地增加，最终导致OOM。

* 2.通常会把被移除屏幕的图片进行回收处理。此时垃圾回收器也会认为你不再持有这些图片的引用，从而对这些图片进行GC操作。重新滑入屏幕这种情况，无疑是性能的瓶颈。

&emsp;&emsp;内存缓存技术对那些大量占用应用程序宝贵内存的图片提供了快速访问的方法。其中最核心的类是LruCache (此类在android-support-v4的包中提供) 。这个类非常适合用来缓存图片，它的主要算法原理是把最近使用的对象用强引用存储在 LinkedHashMap 中，并且把最近最少使用的对象在缓存值达到预设定值之前从内存中移除。

为了能够选择一个合适的缓存大小给LruCache, 有以下多个因素应该放入考虑范围内，例如：

- 你的设备可以为每个应用程序分配多大的内存？

- 设备屏幕上一次最多能显示多少张图片？有多少图片需要进行预加载，因为有可能很快也会显示在屏幕上？

- 你的设备的屏幕大小和分辨率分别是多少？一个超高分辨率的设备（例如 Galaxy Nexus) 比起一个较低分辨率的设备（例如 Nexus S），在持有相同数量图片的时候，需要更大的缓存空间。

- 图片的尺寸和大小，还有每张图片会占据多少内存空间。

- 图片被访问的频率有多高？会不会有一些图片的访问频率比其它图片要高？如果有的话，你也许应该让一些图片常驻在内存当中，或者使用多个LruCache 对象来区分不同组的图片。

- 你能维持好数量和质量之间的平衡吗？有些时候，存储多个低像素的图片，而在后台去开线程加载高像素的图片会更加的有效。

```java
public static voidm main(String[] args){
	System.out.println("llh")；
}
```

[转载自：](http://blog.csdn.net/guolin_blog/article/details/9316683)
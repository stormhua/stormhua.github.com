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

*[转载自：](http://blog.csdn.net/guolin_blog/article/details/9316683)*
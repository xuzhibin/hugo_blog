---
title: android中加载图片时出现oom
date: 2013-02-21
categories: 
- android
tags:
- android
---
ImageView加载图片时，有时会出现OOM


``` java
imageView.setImageResource(imageId);
```

解决方法


``` java
/**
 * 以最省内存的方式读取本地资源的图片
 * 
 * @param context
 * @param resId
 * @return
 */
public static Bitmap readBitMap(Context context, int resId) {
    BitmapFactory.Options opt = new BitmapFactory.Options();
    opt.inPreferredConfig = Bitmap.Config.RGB_565;
    opt.inPurgeable = true;
    opt.inInputShareable = true;
    // 获取资源图片
    InputStream is = context.getResources().openRawResource(resId);
    return BitmapFactory.decodeStream(is, null, opt);
}
```

``` java
Bitmap bitmap=readBitMap(LoginActivity.this,imageId);
imageView.setImageBitmap(bitmap);
```

那是为什么，会导致oom呢：

　　原来当使用像 imageView.setBackgroundResource，imageView.setImageResource, 或者 BitmapFactory.decodeResource 这样的方法来设置一张大图片的时候，这些函数在完成decode后，最终都是通过java层的createBitmap来完成的，需要消耗更多内存。

　　因此，改用先通过BitmapFactory.decodeStream方法，创建出一个bitmap，再将其设为ImageView的 source，decodeStream最大的秘密在于其直接调用JNI>>nativeDecodeAsset()来完成decode，无需再使用java层的createBitmap，从而节省了java层的空间。如果在读取时加上图片的Config参数，可以跟有效减少加载的内存，从而跟有效阻止抛out of Memory异常。

　　另外，需要特别注意：


　　decodeStream是直接读取图片资料的字节码了， 不会根据机器的各种分辨率来自动适应，使用了decodeStream之后，需要在hdpi和mdpi，ldpi中配置相应的图片资源，否则在不同分辨率机器上都是同样大小（像素点数量），显示出来的大小就不对了。
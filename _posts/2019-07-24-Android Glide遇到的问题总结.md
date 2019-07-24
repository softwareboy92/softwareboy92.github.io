---
layout:     post
title:      Android Glide遇到的问题总结
subtitle:   Android Glide兼容AndroidX及生成GlideApp方法
date:       2019-07-24
author:     鱼忆七秒
header-img: img/post-bg-coffee.jpeg
catalog: true
tags:
    - Android
    - Androidx
    - Glide
    - GlideApp
    - 兼容
---

## Android Glide使用遇到的问题

### Android Glide中的注解不兼容AndroidX

今天在进行Glide二次封装的时候，发现Glide中的注解不兼容androidX。查找了一些资料，最后总结一下：

参考资料：https://github.com/bumptech/glide/issues/3185

问题描述：当我们进行Glide框架引用的时候，我们通常是按照如下方式：
``` java
    implementation 'com.github.bumptech.glide:glide:4.9.0'
    annotationProcessor 'com.github.bumptech.glide:compiler:4.9.0'
    implementation 'com.github.bumptech.glide:okhttp3-integration:4.9.0'
```

我们在使用的时候回发现提示是这样的：
**android.support.annotation.CheckResult** 和  **android.support.annotation.NonNull** 提示是不存在的

根据上面GitHub中获取到的知识点总结方法如下的：
``` java 
    implementation 'com.github.bumptech.glide:glide:4.9.0'
    annotationProcessor 'com.github.bumptech.glide:compiler:4.9.0'
    implementation 'com.github.bumptech.glide:okhttp3-integration:4.9.0'
    implementation 'com.android.support:support-annotations:28.0.0'
    annotationProcessor 'com.android.support:support-annotations:28.0.0'
```
通过如上方法进行包的引用即可解决；


### Android Glide中GlideApp无法生成问题解决

##### 如若要使用GlideApp需要做的是：

1. 自定义一个类extends AppGlideModule
2. 为这个类加入注解@GlideModule

``` java
/**
 * @author by lvzhongdi on 2019/7/24.
 */
@GlideModule
public class ProgressAppGlideModule extends AppGlideModule {
    @Override
    public void registerComponents(@NonNull Context context, @NonNull                               Glide glide, @NonNull Registry registry) {
        super.registerComponents(context, glide, registry);
       
    }
}
```

完成如上操作后：**Clear** 一下工程，然后再 **Build** 一下即可生成 **GlideApp**

#### **注意** 使用GlideApp这个类，**SDK build**版本必须为>=27
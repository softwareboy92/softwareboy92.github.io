---
layout:     post
title:      Android Studio 引入aar文件遇到的问题总结
subtitle:   最近在用android studio生成aar文件的时候遇到的问题总结一下
date:       2019-04-17 01:13:17 +0800
author:     北边一小民
header-img: img/post-bg-os-metro.jpg
catalog: true
tags:
    - Android
    - aar

---

### 1.先说说如何引入

1. 准备下自己需要的aar文件包(例如：test-debug.aar)
2. 将文件包放入到自己的app工程的libs文件下
3. 配置app的Gradle文件，配置如下：

```
android{
        .......
}
 repositories{
        flatDir {
                dirs'libs'
        }
}
dependencies {
        compile(name:'test-debug', ext:'aar')
}
```
### 2.出现的问题
>    提示：Manifest merger failed with multiple errors, see logs

### 3.问题解决方案

1. 检查生成aar文件的编译器支持的最低minSdkVersion 和最高的targetSdkVersion 是否在本地项目支持的范围内；
2. 可能是由于资源文件冲突导致。需要解决的方法如下；
    在manifest根标签上加入xmlns:tools="http://schemas.android.com/tools"，并在Manifest.xml的application标签下添加tools:replace="icon, label,theme"（多个属性用,隔开，视情况而定，最好照抄）。
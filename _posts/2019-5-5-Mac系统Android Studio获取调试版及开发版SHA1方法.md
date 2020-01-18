---
layout:     post
title:      Mac系统Android Studio获取调试版及开发版SHA1方法
subtitle:   Mac系统Android Studio获取调试版及开发版SHA1方法
date:       2019-05-05
author:     北边一小民
header-img: img/post-bg-sha1.PNG
catalog: true
tags:
    - Mac
    - Android Studio
    - SHA1
---

### 调试版本：
1. 直接在Android Studio工程中打开Terminal：
![图1](/img/sha1_image01.png)
2. 输入keytool -list -v -keystore ~/.android/debug.keystore 回车
![图2](/img/sha1_image02.png)
3.输入密码，默认密码为android
![图3](/img/sha1_image03.png)
### 发布版本：
1. 打开终端
![图4](/img/sha1_image04.png)
2. 输入keytool -list -v -keystore path(path是自己制作的打包时候的签名证书) 回车
![图5](/img/sha1_image05.png)
3. 输入密码，密码为打包时候的签名证书的密码



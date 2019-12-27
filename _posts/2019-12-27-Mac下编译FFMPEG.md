---
layout:     post
title:      Mac下编译FFMPEG
subtitle:   Mac下编译FFMPEG
date:       2019-12-27
author:     Albert
header-img: img/post-bg-flutter.jpeg
catalog: true
tags:
    - Mac
    - 编译
    - FFMPEG
    
---


### 编译环境：

- Mac
- FFMPEG 3.3.6
- NDK 版本：android-ndk-r13b

[FFMPEG下载](https://links.jianshu.com/go?to=https%3A%2F%2Fffmpeg.org%2Freleases%2Fffmpeg-3.3.6.tar.bz2)

## 编译过程

- 修改FFMPEG根目录下的configure配置文件


默认编译后的.so文件格式为：文件名+.so+三段版本号的格式比如libavformat.so.57.0.101。这样的文件格式不符合我们的要求，而且即便是将这样的文件名称简单粗暴的删除.so后面的版本号，在实际使用时也无法编译。所以修改如下：

```
#修改前
SLIBNAME_WITH_MAJOR='$(SLIBNAME).$(LIBMAJOR)'
LIB_INSTALL_EXTRA_CMD='$$(RANLIB) "$(LIBDIR)/$(LIBNAME)"' SLIB_INSTALL_NAME='$(SLIBNAME_WITH_VERSION)'
SLIB_INSTALL_LINKS='$(SLIBNAME_WITH_MAJOR) $(SLIBNAME)'
#修改后  
SLIBNAME_WITH_MAJOR='$(SLIBPREF)$(FULLNAME)-$(LIBMAJOR)$(SLIBSUF)'
LIB_INSTALL_EXTRA_CMD='\$$(RANLIB) "$(LIBDIR)/$(LIBNAME)"'
SLIB_INSTALL_NAME='$(SLIBNAME_WITH_MAJOR)'
SLIB_INSTALL_LINKS='\$(SLIBNAME)'
```

- 编写脚本

新建一个文本并将下面的内容复制粘贴进去后保存命名为build_android.sh

```
#!/bin/bash
# 修改为自己NDK包根目录
export NDK_HOME=/Users/albert/android-ndk-r13b
#根据自己的需求修改编译平台版本
export PLATFORM_VERSION=android-14
#定义编译方法
function build
{
    #echo 输出命令
    echo "start build ffmpeg for $ARCH"
    #调用configure命令开始编译，并传入对应的参数
    ./configure --target-os=linux \
    --prefix=$PREFIX --arch=$ARCH \
    --disable-doc \
    --disable-static \
    --disable-yasm \
    --disable-asm \
    --disable-symver \
    --disable-ffmpeg \
    --disable-ffplay \
    --disable-ffprobe \
    --disable-ffserver \
    --cross-prefix=$CROSS_COMPILE \
    --enable-cross-compile \
    --enable-shared \
    --enable-gpl \
    --sysroot=$SYSROOT \
    --enable-small \
    --extra-cflags="-Os -fpic $ADDI_CFLAGS" \
    --extra-ldflags="$ADDI_LDFLAGS" \
    $ADDITIONAL_CONFIGURE_FLAG
    make clean
    make
    make install
    echo "build ffmpeg for $ARCH finished"
}

#编译 arm-v7a
PLATFORM_VERSION=android-14
ARCH=arm
CPU=armeabi-v7a
PREFIX=$(pwd)/android_all/$CPU
TOOLCHAIN=$NDK_HOME/toolchains/arm-linux-androideabi-4.9/prebuilt/darwin-x86_64
CROSS_COMPILE=$TOOLCHAIN/bin/arm-linux-androideabi-
ADDI_CFLAGS="-march=armv7-a -mfloat-abi=softfp -mfpu=vfpv3-d16 -mthumb -mfpu=neon"
ADDI_LDFLAGS="-march=armv7-a -Wl,--fix-cortex-a8"
SYSROOT=$NDK_HOME/platforms/$PLATFORM_VERSION/arch-$ARCH/
build
```

- 开始编译

给编译脚本添加权限
```
sudo chmod 777 build_android.sh
```

开始执行脚本
```
./build_android.sh
```
编译过程大概需要五六分钟左右，编译完成后可以根据脚本中你指定的输出目录中查看编译好的so文件，脚本默认输出目录是：ffmpeg当前目录/android_all/CPU架构/ , 类似如下图所示:

![image](http://note.youdao.com/yws/res/3841/95A7FA192A854FB5A5E4C972B150A23C)

如果需要其他CPU平台so可以根据需求自己修改对应的参数就可以了，下面列出几个常用的平台，直接复制到脚本中就可以了.

```
#arm
PLATFORM_VERSION=android-14
ARCH=arm
CPU=armeabi
PREFIX=$(pwd)/android_all/$CPU
TOOLCHAIN=$NDK_HOME/toolchains/arm-linux-androideabi-4.9/prebuilt/darwin-x86_64
CROSS_COMPILE=$TOOLCHAIN/bin/arm-linux-androideabi-
ADDI_CFLAGS="-marm -mthumb"
ADDI_LDFLAGS=""
SYSROOT=$NDK_HOME/platforms/$PLATFORM_VERSION/arch-$ARCH/
build

#arm-v7a
PLATFORM_VERSION=android-14
ARCH=arm
CPU=armeabi-v7a
PREFIX=$(pwd)/android_all/$CPU
TOOLCHAIN=$NDK_HOME/toolchains/arm-linux-androideabi-4.9/prebuilt/darwin-x86_64
CROSS_COMPILE=$TOOLCHAIN/bin/arm-linux-androideabi-
ADDI_CFLAGS="-march=armv7-a -mfloat-abi=softfp -mfpu=vfpv3-d16 -mthumb -mfpu=neon"
ADDI_LDFLAGS="-march=armv7-a -Wl,--fix-cortex-a8"
SYSROOT=$NDK_HOME/platforms/$PLATFORM_VERSION/arch-$ARCH/
build


#arm64
PLATFORM_VERSION=android-21
ARCH=arm64
CPU=arm64
PREFIX=$(pwd)/android_all/$CPU
TOOLCHAIN=$NDK_HOME/toolchains/aarch64-linux-android-4.9/prebuilt/darwin-x86_64
CROSS_COMPILE=$TOOLCHAIN/bin/aarch64-linux-android-
ADDI_CFLAGS=""
ADDI_LDFLAGS=""
SYSROOT=$NDK_HOME/platforms/$PLATFORM_VERSION/arch-$ARCH/
build


#x86
PLATFORM_VERSION=android-14
ARCH=x86
CPU=x86
PREFIX=$(pwd)/android_all/$CPU
TOOLCHAIN=$NDK_HOME/toolchains/x86-4.9/prebuilt/darwin-x86_64
CROSS_COMPILE=$TOOLCHAIN/bin/i686-linux-android-
ADDI_CFLAGS="-march=i686 -mtune=intel -mssse3 -mfpmath=sse -m32"
ADDI_LDFLAGS=""
SYSROOT=$NDK_HOME/platforms/$PLATFORM_VERSION/arch-$ARCH/
build

#x86_64
PLATFORM_VERSION=android-21
ARCH=x86_64
CPU=x86_64
PREFIX=$(pwd)/android_all/$CPU
TOOLCHAIN=$NDK_HOME/toolchains/x86_64-4.9/prebuilt/darwin-x86_64
CROSS_COMPILE=$TOOLCHAIN/bin/x86_64-linux-android-
ADDI_CFLAGS="-march=x86-64 -msse4.2 -mpopcnt -m64 -mtune=intel"
ADDI_LDFLAGS=""
SYSROOT=$NDK_HOME/platforms/$PLATFORM_VERSION/arch-$ARCH/
build

#mips
PLATFORM_VERSION=android-14
ARCH=mips
CPU=mips
PREFIX=$(pwd)/android_all/$CPU
TOOLCHAIN=$NDK_HOME/toolchains/mipsel-linux-android-4.9/prebuilt/darwin-x86_64
CROSS_COMPILE=$TOOLCHAIN/bin/mipsel-linux-android-
ADDI_CFLAGS=""
ADDI_LDFLAGS=""
SYSROOT=$NDK_HOME/platforms/$PLATFORM_VERSION/arch-$ARCH/
build

#mips64
PLATFORM_VERSION=android-21
ARCH=mips64
CPU=mips64
PREFIX=$(pwd)/android_all/$CPU
TOOLCHAIN=$NDK_HOME/toolchains/mips64el-linux-android-4.9/prebuilt/darwin-x86_64
CROSS_COMPILE=$TOOLCHAIN/bin/mips64el-linux-android-
ADDI_CFLAGS=""
ADDI_LDFLAGS=""
SYSROOT=$NDK_HOME/platforms/$PLATFORM_VERSION/arch-$ARCH/
build

```

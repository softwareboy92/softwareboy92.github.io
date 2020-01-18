---
layout:     post
title:      Mac 下 Android 发布项目到私服Nexus仓库
subtitle:   Mac 下 Android 发布项目到私服Nexus仓库
date:       2019-04-18
author:     北边一小民
header-img: img/post-bg-desk.jpg
catalog: true
tags:
    - Mac
    - Nexus
    - 仓库
---

**Mac 下 Android 发布项目到私服Nexus仓库**

之前了解过一点，但是没操作过，我看有的人在GitHub上发布自己的私有库，然后提供给其他人使用，这个我试了一下，还没搞清楚什么问题，不知道为什么使用不了。希望看到本文的人，知道的可以告诉我一下，谢谢哦，下面我要开始写我操作的步骤了；

**1.下载Nexus**

从nexus官网下载；http://www.sonatype.org/nexus/go 下载对应版本即可
通过brew install nexus 命令下载。没安装的可以去安装个brew命令；（ https://brew.sh/ ） brew官网进行安装；
1.在终端中输入 brew install nexus 安装完成后
2.启动nexus 终端命令： brew services start nexus
3.这样就已经启动了，现在我们输入地址http://127.0.0.1:8081/nexus 端口默认是8081 用户名密码默认是：admin/admin123
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416125722485.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==,size_16,color_FFFFFF,t_70)
2.创建Android工程，并且创建lib依赖包

1.创建一个android工程
![d62515da7d5bfa0f8a14397435d4b518.png](https://img-blog.csdnimg.cn/2019041612531480.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==,size_16,color_FFFFFF,t_70)
2.创建一个model
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416125340724.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==,size_16,color_FFFFFF,t_70)
3.修改model的名字
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416125354662.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==,size_16,color_FFFFFF,t_70)
4.在model中创建一个抽象的activity类
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416125418444.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==,size_16,color_FFFFFF,t_70)
完整的项目目录结构
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416125436797.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==,size_16,color_FFFFFF,t_70)

准备工作基本已经完成，下面是gradle的配置

**3.lib下的gradle配置**

在根部添加
apply plugin:'maven'
在最下面配置如下内容：
```
uploadArchives {
    repositories {
        mavenDeployer {
            repository(url: uri("http://localhost:8081/nexus/content/repositories/releases/")) {
                authentication(userName:'admin',password:'admin123')
            }
            pom.groupId = "com.lib.maven" //随便写，但是一般为包名
            pom.artifactId = "maven-demo" //随便写，但是一般为lib名字
            pom.version = "1.0.0" //随便写，有点规律好吗？
            pom.project {
                licenses {
                    license {
                        name 'The Apache Software License, Version 2.0'
                        url 'http://www.apache.org/licenses/LICENSE-2.0.txt'
                    }
                }
            }
        }
    }
}
```
**4.如何上传**

1.配置一下nexus的文件哦
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416125530682.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==,size_16,color_FFFFFF,t_70)
2.按照图示顺序操作
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416125543727.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==,size_16,color_FFFFFF,t_70)
执行完毕后，即可看到如下图所示内容：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416125558495.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==,size_16,color_FFFFFF,t_70)
**5.本地的nexus上传完毕，应该如何使用呢？**

1. 打开Android 工程的root gradle文件，添加内容；完整结构如下：
```
 // Top-level build file where you can add configuration options common to all sub-projects/modules.
buildscript {
    repositories {
        google()
        jcenter()
        mavenCentral()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.3.2'
        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}
allprojects {
    repositories {
        google()
        jcenter()
        maven {
            url "http://localhost:8081/nexus/content/repositories/releases"
        }
    }
}
task clean(type: Delete) {
    delete rootProject.buildDir
}
```
2.在app项目下的gradle文件中添加如下内容
```
dependencies {
    ......忽略
    implementation 'com.lib.maven:maven-demo:1.0.0'
}
```
3.为了验证是否已经可以使用，我们将项目中setting.gradle中的'libdemo'去除
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416125616502.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==,size_16,color_FFFFFF,t_70)
4.在app的MainActivity中继承BaseActivity这个类，如下图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019041612563027.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==,size_16,color_FFFFFF,t_70)
**总结**

1.如上内容是在本地配置的一个nexus的代码仓库，这个也可以更改到服务器上，原理一样；
2.如果只想本地配置，不需要nexus也可以的，我直接展示代码，不做具体步骤；
```
	A.lib的gradle中的内容做如下修改：
    uploadArchives {
        repositories.mavenDeployer {
        repository(url: uri('../repository'))
        pom.project {
            groupId "com.lib.maven" // 可以随意取，一般取包名
            artifactId "maven-demo" // 可以随意取，一般取库的名字
            version "1.0.0" // 版本号
        }
    }
}
	 B: 使用上，在root的gradle中修改上面的maven 为：
     maven {
          url uri('../repository')
     }
 ```
        
3.致此文章完毕，有疑问的可以留言
GitHub：https://github.com/softwareboy92/maven-demo
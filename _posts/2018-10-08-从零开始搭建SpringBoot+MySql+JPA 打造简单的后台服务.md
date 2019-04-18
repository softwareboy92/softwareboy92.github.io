---
layout:     post
title:      从零开始搭建SpringBoot+MySql+JPA 打造简单的后台服务
subtitle:   从零开始搭建SpringBoot+MySql+JPA 打造简单的后台服务
date:       2018-10-08
author:     鱼忆七秒
header-img: img/post-bg-kuaidi.jpg
catalog: true
tags:
    - Springboot
    - mysql
    - jpa
  
---

刚开始学习使用Springboot，想简单的记录一下自己学习的过程。同时也讲我知道的分享出去，就这么简单；有什么错误，请指正；至于概念什么的，我就不说了，自己百度吧；
![开始干活](https://img-blog.csdn.net/2018100816010890?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
第一步：前期准备
1.开发工具使用的idea 2017
2.数据库使用的是mysql
3.导包用的是maven
4.java version "1.8.0_181"
5.测试工具使用的是postman
第二步：开始新建工程
![创建工程的开始第一步](https://img-blog.csdn.net/20181008160524201?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
![项目和包名](https://img-blog.csdn.net/20181008160736151?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
![在这里插入图片描述](https://img-blog.csdn.net/20181008160811519?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
![在这里插入图片描述](https://img-blog.csdn.net/20181008160850996?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
![在这里插入图片描述](https://img-blog.csdn.net/20181008160926173?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
新建完的工程如下所示：
![在这里插入图片描述](https://img-blog.csdn.net/20181008161147726?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
第三步：配置数据库
在application.properties 中配置一下；
如图所示
![在这里插入图片描述](https://img-blog.csdn.net/2018100816211116?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
记得在本地用mysql创建个dbtest的数据库哦；
![在这里插入图片描述](https://img-blog.csdn.net/2018100816222931?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
第四步：写个controller验证一下；
新建一个包叫controller
创建一个HelloController的类
如图所示
![在这里插入图片描述](https://img-blog.csdn.net/2018100816150772?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
为了验证，我们点击启动一下项目
![在这里插入图片描述](https://img-blog.csdn.net/20181008162339738?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
接下来我们使用postman访问一下；看看能不能访问到？
![在这里插入图片描述](https://img-blog.csdn.net/20181008162546114?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
我的访问成功了，你的呢？这个步骤完成了；项目其实是可以使用的了，为了动态，我们需要创建个数据库表格；
第五步：干活之前先清楚项目的结构，我的很简单，如图所示吧
![在这里插入图片描述](https://img-blog.csdn.net/20181008163018439?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
1.创建一个数据库表，使用了jpa，不用像以前，先在数据库创建表，然后在连接表，这个是可以直接创建的。一个bean生成一个表；（ps：这个是需要标签的，具体标签学习，后面再详细介绍）***在bean中，生成get和set方法，再重写他的toString方法，为了我们后面打印；***
![在这里插入图片描述](https://img-blog.csdn.net/20181008164047530?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
2.下面我们要连接这个表，创建jpa，然后就可以对这个表中的数据进行想要的操作了；如下图所示：
![在这里插入图片描述](https://img-blog.csdn.net/201810081647345?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
这就搞定了；
3.接下来创建service：
如图所示：
![在这里插入图片描述](https://img-blog.csdn.net/20181008165252242?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
4.下面我们开始创建controller，我理解就是提供对外访问的；

![在这里插入图片描述](https://img-blog.csdn.net/20181008165632637?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**这里留个问题：为什么不直接返回一个List<JobModel> 就OK了呢？**

5.我们重启下服务，看看这个表是否生成
我的报错了，我擦擦，翻车了，错误提示如下：

![在这里插入图片描述](https://img-blog.csdn.net/20181008170447956?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

老夫检查了下，结果是application中的jpa没配置，增加jpa的配置如下：

![在这里插入图片描述](https://img-blog.csdn.net/20181008170718283?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

之后老夫访问了一下，结果成功了。如图所示：

![在这里插入图片描述](https://img-blog.csdn.net/20181008170802950?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这么看不出来效果，来我们给数据库中增加下数据，然后访问下这个网址：

![在这里插入图片描述](https://img-blog.csdn.net/20181008170921596?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![在这里插入图片描述](https://img-blog.csdn.net/20181008170940349?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2emhvbmdkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

到这里看到我们想要的数据了，这个简单的工程就完成了；是不是很简单？

总结：springboot 我个人感觉，这个jpa挺好用的，少写了很多sql，相比之下，我认为这个东西现在在小型项目中的使用应该是没问题的，别用到复杂的工程中，会乱掉；


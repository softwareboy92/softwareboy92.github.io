---
layout:     post
title:      Android性能优化
subtitle:   Android性能优化
date:       2020-03-06
author:     北边一小民
header-img: img/post-bg-re-vs-ng2.jpg
catalog: true
tags:
    - 性能
    - 优化
    - Android
---
# Android性能优化

![](https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=422562817,3977851189&fm=26&gp=0.jpg)

* 为什么要进行性能优化?
* 有哪些可以进行性能优化？

## 为什么要进行性能优化？
随着项目版本的不断迭代，App的性能问题会逐渐的暴露出来，给用户带来一些卡顿、崩溃的体验。面对给和用户造成的不良效果，做出了性能优化，提升App整体性能，带用户带来良好的用户触感。
## 有哪些可以进行性能优化？
- 内存优化
- UI优化
- 网络优化
- 启动优化
- 电量优化


### 1.内存优化
内存泄漏是Android的常客。那么什么是内存泄漏呢？内存不在GC的掌控范围之内了。那么java的GC内存回收机制是什么？某对象不在有任何引用的时候才会进行回收。那么GC回收机制的原理是什么？我们先来看张图。

![在这里插入图片描述](https://pic4.zhimg.com/80/v2-2da9d038d505f0aed2daac3ca7923307_1440w.jpg)

当我们向上寻找，一直寻找到GC Root的时候，此对象不会进行回收，例如，一个Activity。那么如果我们向上寻找，直到找到GC Root对象的时候，就说明它是不可以回收的，

例如，我定义了一个int a；但是这个数据，我整个页面或者说整个项目都没有用到，则这个对象会被GC掉。

**GC的引用点**
- java栈中引用的对象
- 方法静态引用的对象
- 方法常量引用的对象
- Native中JNI引用的对象
- Thread——“活着的”线程

#### 如何判断

那么我们如何判断一个对象是一个垃圾对象，可以讲他进行回收呢？

[了解如何GC](https://blog.csdn.net/canot/article/details/51037938)

举了小例子教你们如何区分：
> 一般在学校吃饭，我们有两种情况，
第一：吃完饭就直接走人，碗筷留给阿姨来收拾处理。
第二：吃完之后把碗筷放到收盘处直接进行回收。
但我们是个有素质的人，一般采用第二种情况，但根据想法，我们更倾向于第一种。
那么一般在饭店或者KFC中，都是第一种情况。
那么此时，问题来了，如果我已经吃完饭，然后我并没有离开饭店，做在位置上和朋友吹吹牛逼，谈谈理想，聊聊人生。
那么桌上那一堆碗筷是收还是不收？讲道理是不能收的。虽然实际也是不能收的。因为顾客是上帝~~~

So，我们如何判断一个对象是一个可回收的垃圾对象呢？这是我们的一个主观的判断。但是有种情况我们是必须要考虑到的，没错，就是内存过多无法释放的时候，会直接导致OOM。整个项目boom炸了。什么鬼？outofmemory。没错就是它。

#### 内存溢出

**分析原因**

我们需要分析内存溢出的原因，我们先来看一张图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200306163724614.png)
内存泄漏一般导致应用卡顿，极端情况会导致项目boom。Boom的原因是因为超过内存的阈值。原因主要有两方面：

- 代码存在泄漏，内存无法及时释放导致oom（这个我们后面说）
- 一些逻辑消耗了大量内存，无法及时释放或者超过导致oom

所谓消耗大量的内存的，绝大多数是因为图片加载。这是我们oom出现最频繁的地方。我有写过图片加载的方法，一个是控制每次加载的数量，第二，保证每次滑动的时候不进行加载，滑动完进行加载。一般情况使用先进后出，而不是先进先出。不过一般我们图片加载都是使用fresco或者Glide等开源库。我们来看下下面两张图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200306163742393.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200306163755565.png)
对比两张图，我们可以在第一张的情况出现了oom情况，我们通过log打印发现，处理的好像没什么问题，换句话说，如果我不放那0.8M的图片。然后继续不停的操作同样会出现OOM，然而我们就蒙了。没什么图片加载怎么就这么崩掉了。

#### 内存分析工具
- Heap SnapShot工具
- Heap Viewer工具
- LeakCanary工具（常用）
- MAT工具

#### 注意事项

- 我们尽量不要使用Activity的上下文，而是使用application的上下文，因为application的生命周期长，进程退出时才会被销毁。所以，单例模式是最容易造成内存溢出的原本所在，因为单例模式的生命周期的应该和application的生命周期一样长，而不是和Activity的相同。
- Animation也会导致内存溢出，为什么？因为我们是通过view来进行演示的，导致view被Activity持有，而Activity又持有view。最后因为Activity无法释放，导致内存泄漏。解决方法是在Activity的ondestory（）方法中调用Animation.cancle（）进行停止，当然一些简单的动画我们可以通过自定义view来解决。至少我现在已经很少使用Animation了。没有一个动画是自定义view解决不了的。如何有，那就是两个~~~。


### 2.UI优化

UI优化主要包括布局优化以及view的绘制优化。不急，我们接下来一个一个慢慢看~~。先说下UI的优化到底是什么？有些时候我们打开某个软件，会出现卡顿的情况。这就是UI的问题。那么我们想一下，什么情况会导致卡顿呢？一般是如下几种情况：

- 人为在UI线程中做轻微耗时操作，导致UI线程卡顿；
- 布局Layout过于复杂，无法在16ms内完成渲染；
- 同一时间动画执行的次数过多，导致CPU或GPU负载过重；
- View过度绘制，导致某些像素在同一帧时间内被绘制多次，从而使CPU或GPU负载过重；
- View频繁的触发measure、layout，导致measure、layout累计耗时过多及整个View频繁的重新渲染；内存频繁触发GC过多（同一帧中频繁创建内存），导致暂时阻塞渲染操作；
- 冗余资源及逻辑等导致加载和执行缓慢；
- 臭名昭著的ANR；

可以看见，上面这些导致卡顿的原因都是我们平时开发中非常常见的。有些人可能会觉得自己的应用用着还蛮OK的，其实那是因为你没进行一些瞬时测试和压力测试，一旦在这种环境下运行你的App你就会发现很多性能问题。

#### 布局优化

**GPU绘制**
我们对于UI性能的优化还可以通过开发者选项中的GPU过度绘制工具来进行分析。在设置->开发者选项->调试GPU过度绘制（不同设备可能位置或者叫法不同）中打开调试后可以看见如下图（对settings当前界面过度绘制进行分析）：

![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxNy8xMS8xNi8xNWZjMzg3NjA3NGMwNTNm?x-oss-process=image/format,png)
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxNy8xMS8xNi8xNWZjMzg3NjBmOWRmZTM1?x-oss-process=image/format,png)
可以发现，开启后在我们想要调试的应用界面中可以看到各种颜色的区域，具体含义如下：

![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxNy8xMS8xNi8xNWZjMzg3NjA0MmUxODg1?x-oss-process=image/format,png)
Overdraw有时候是因为你的UI布局存在大量重叠的部分，还有的时候是因为非必须的重叠背景。例如某个Activity有一个背景，然后里面的Layout又有自己的背景，同时子View又分别有自己的背景。仅仅是通过移除非必须的背景图片，这就能够减少大量的红色Overdraw区域，增加蓝色区域的占比。这一措施能够显著提升程序性能。

如果布局中既能采用RealtiveLayout和LinearLayout，那么直接使用LinearLayout，因为Relativelayout的布局比较复杂，绘制的时候需要花费更多的CPU时间。如果需要多个LinearLayout或者Framelayout嵌套，那么可采用Relativelayout。因为多层嵌套导致布局的绘制有大部分是重复的，这会减少程序的性能。

**代码优化**

Android Studio自带的代码检查工具。打开Analyze->Run Inspection by Name… –>unused resource 点击开始检测，等待一下后会发现如下结果：
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxNy8xMS8xNi8xNWZjMzg3NjMyY2QyNGY1?x-oss-process=image/format,png)
我们还可以这样，将鼠标放在代码区点击右键->Analyze->Inspect Code–>界面选择你要检测的模块->点击确认开始检测，等待一下后会发现如下结果：
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxNy8xMS8xNi8xNWZjMzg3NjM2YzZjNDY4?x-oss-process=image/format,png)
上面那两种方法是最容易找到代码缺陷以及无用代码的地方。所以尽情的入坑去填坑把~~~


#### 绘制优化

那么什么是绘制优化？绘制优化主要是指View的onDraw方法需要避免执行大量的操作。我将分为了2个方面。

- onDraw方法不需要创建新的局部对象，这是因为onDraw方法是实时执行的，这样会产品大量的临时对象，导致占用了更多内存，并且使系统不断的GC。降低了执行效率。
- onDraw方法不需要执行耗时操作，在onDraw方法里少使用循环，因为循环会占用CPU的时间。导致绘制不流畅，卡顿等等。Google官方指出，view的绘制帧率稳定在60dps，这要求每帧的绘制时间不超过16ms（1000/60)。虽然很难保证，但我们需要尽可能的降低。


### 3.网络优化

线程是我们项目中不可缺少的重要部分，因为我们大多数数据都是从网络获取的。线程这个是必备用品。我们依旧可以通过Memory下面的Net进行网络的监听：
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9sYy1nb2xkLWNkbi54aXR1LmlvLzk4ZmI5M2ZhMDEwNWYxMWVkNzA5LmpwZw?x-oss-process=image/format,png)

**ANR问题**

那么什么是ANR？

Application Not Responding。应用程序无响应。

那么一般什么时候会出现ANR?

Android官方规定：
- activity如果5s内无响应事件（屏幕触摸事件或者键盘输入事件）。
- BroadcastReceiver如果在10s内无法处理完成。
- Service如果20s内无法处理完成。这三种情况会导致ANR。

用张简洁的图来介绍把。看起来方便~~
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9sYy1nb2xkLWNkbi54aXR1LmlvLzBiNmUxMzExNmQzYzI2NWQ2MTI2LnBuZw?x-oss-process=image/format,png)
**线程优化**

上面说的三种导致ANR的情况，绝大多数就是因为线程阻塞导致的。那么我们应该如何处理呢？

Android系统为我们提供了若干组工具类来解决此问题。

- Asynctask：为UI线程与工作线程之间进行快速处理的切换提供一种简单便捷的机制。适用于当下立即需要启动，但是异步执行的生命周期短暂的场景。
- HandlerThread：为某些回调方法或者等待某些执行任务的执行设置一个专属的线程，并提供线程任务的调度机制。
- ThreadPool：把任务分解成不同的单元，分发到各个不同的线程上，进行同时并发处理。
- IntentService：适合执行由Ui触发的后台任务。并可以把这些任务执行的情况通过一定的机制反馈给UI。

网络请求耗时会给用户带来卡顿的产品体验，虽然可以使用Loading提升用户体验，但属于治标不治本。例如，当网络差的时候我们公司的项目一个loading就是10多s。甚至更多.....

一般多线程的情况我们可以通过Asynctask处理。我前面有说过annotation（或者可以使用Rxjava）。这是google官方推出的注解。比bufferknife强大很多。这个可以快捷方便的处理多线程而且不会导致线程阻塞，而且你也可以控制线程的顺序，例如我要执行完线程A后，根据线程A的某个参数来执行线程B。以此类推.....

至于线程池么，最多的还是要说道图片加载了~~。图片加载用三方就行了~想看详细介绍，我前面有说，当然除了这个还有下载操作。这就和IntentService有关联了。

**图片处理**

- 使用WebP格式；同样的照片，采用WebP格式可大幅节省流量，相对于JPG格式的图片，流量能节省将近 25% 到 35 %；相对于 PNG 格式的图片，流量可以节省将近80%。最重要的是使用WebP之后图片质量也没有改变。后台的小伙伴们看看是不是要处理下？

- 使用缩略图，我在前面写图片加载有说过，就是控制他的inside和option。然后进行图片缩放。压缩？讲道理....我并不知道网络图片怎么压缩，but，我会缩放啊~~反正也不会失真。啦啦啦~咬我啊？

**网络请求处理**

我们可以对服务端返回数据进行缓存，设定有效时间，有效时间之内不走网络请求，减少流量消耗。对网络的缓存可以参见[图片的三级缓存](https://www.cnblogs.com/zhchoutai/p/8423214.html)

在某些情况，我们尽量少使用GPS定位，如果条件允许，尽可能使用网络定位。

下载、上传，我们尽可能使用断点续传，断点下载也是我们的必修课~

刷新数据时，尽可能使用局部刷新，而不是全局刷新，

第一、界面会闪屏一下，网差的界面直接白屏一段时间也不是不可能。
第二、流量的使用！！一个闪屏的缓存60+M，我清个3、5次缓存，在打开个3、5次。好了，2分钟时间，我一个月流量就没了。。。我前面提到的网络缓存很重要。

### 4.启动优化

安卓应用的启动方式分为三种：冷启动、暖启动、热启动，不同的启动方式决定了应用UI对用户可见所需要花费的时间长短。顾名思义，冷启动消耗的时间最长。基于冷启动方式的优化工作也是最考验产品用户体验的地方。谈及优化之前，我们先看看这三种启动方式的应用场景，以及启动过程中系统都做了些什么工作。

**冷启动**

为什么说冷启动是耗时最长的。冷启动是在启动应用前，系统没有获取到当前app的activity、Service等等。例如，第一次启动app。又或者说杀死进程后第一次启动。那么对比其他两种方式。冷启动自然是耗时最久的。

应用发生冷启动时，系统一定会执行下面的三个任务：

- 开始加载并启动应用
- 应用启动后，显示一个空白的启动窗口（启动闪屏页）
- 创建应用信息

那么创建应用信息，系统就需要做一屁股的事：

- application的初始化
- 启动UI线程
- 创建Activity
- 导入视图（inflate view）
- 计算视图大小（onmesure view）
- 得到视图排版（onlayout view）
- 绘制视图（ondraw view）

这其中有两个 creation 工作，分别为 Application 和 Activity creation。他们均在 View 绘制展示之前。所以，在应用自定义的 Application 类和 第一个 Activity 类中，onCreate() 方法做的事情越多，冷启动消耗的时间越长。

**暖启动**

当应用中的 Activities 被销毁，但在内存中常驻时，应用的启动方式就会变为暖启动。相比冷启动，暖启动过程减少了对象初始化、布局加载等工作，启动时间更短。但启动时，系统依然会展示闪屏页，直到第一个 Activity 的内容呈现为止。

**热启动**

相比暖启动，热启动时应用做的工作更少，启动时间更短。热启动产生的场景很多，常见如：用户使用返回键退出应用，然后马上又重新启动应用。

#### 如何进行优化？

[参考今日头条启动优化流程](https://juejin.im/post/5d95f4a4f265da5b8f10714b)

### 5.电量优化

**耗电概念**

其实大多数开发者对电量优化的重视程度极低，其实提到性能优化想到的就是内存优化，但我们不能忽视其他的优化，电量优化其实还是必要的，例如爱奇艺、优酷等等的视频播放器以及音乐播放器。众所周知，音乐和视频其实是耗电量最大的。如果用户一旦发现我们的应用非常耗电，不好意思，他们大多会选择卸载来解决此类问题。为此，我们需要进行优化。

**如何优化**

其实我们把上面那四种优化解决了，就是最好的电量优化。So，对于电量优化，我在此提一些建议：

- 需要进行网络请求时，我们需先判断网络当前的状态。
- 在多网络请求的情况下，最好进行批量处理，尽量避免频繁的间隔网络请求。
- 在同时有wifi和移动数据的情况下，我们应该直接屏幕移动数据的网络请求，只有当wifi断开时在调用，因为，wifi请求的耗电量远比移动数据的耗电量低的低。
- 后台任务要尽可能少的唤醒CPU。（比方说，锁屏时，QQ的消息提示行就是唤醒了CPU。但是它的提示只有在你打开锁屏或者进行充电时才会进行提示。）


### 总结
对于Android的优化，这是一个必经之路，不光是要提升用户的体验，更是要自己的程序更加的健壮。如上的几个分类是对Android的运行中的性能进行几方面的优化方案。还有一些Android安装包的优化方案，例如：APK减肥计划、交互体验的优化、动画的优化等等。
















































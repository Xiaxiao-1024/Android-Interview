![](https://upload-images.jianshu.io/upload_images/2824145-b25f520b367237d1.jpeg?imageMogr2/auto-orient/strip|imageView2/2/w/739/format/webp)

### 前言

金九银十面试季，相信大家肯定急需一套Android面试宝典，今天小编就给大家准备了我珍藏已久的Android高阶面试宝典，一份超级详细的Android面试必备知识点，供大家学习 ！

想必每一个安卓程序员都有追求大厂的决心，但是想要进入大厂，我们需要掌握哪些知识点呢？这里，我为大家梳理了一个整体的知识架构。整体包括Java、Android、算法、网络等。希望大家阅读之后，能帮助大家完善与整理自己的知识体系。祝大家面试顺利~~

**文末还有给大家分享我整理的Android面试专题及答案可以拿去参考下~**

> 注意，整篇文章是知识点的概括，不包含答案，需要大家多看源码，知识只有自己去探索与发现，才会弥足珍贵！

## Java基础知识点

#### Jvm相关

*   Java内存结构及分区
*   Java对象的创建、存储及访问
*   Java判断对象是否存活及垃圾回收算法（GC）
*   Jvm中的常见的垃圾回收器
*   Java类加载过程
*   Java类加载器（双亲委派模型）

#### 集合相关

*   ArrayList分析
*   LinkedList分析
*   HashMap分析
*   HashTable分析
*   LinkedHashMap分析
*   HashSet分析
*   LinkedHashSet分析
*   ArrayMap、SparseMap、与HashMap的对比
*   ConcurrentHashMap分析

#### 并发相关

*   Java内存模型
*   volatile原理
*   Synchronized的原理
*   AQS原理
*   Condition原理
*   ReentrantLock 原理
*   公平锁与非公平锁
*   ReentrantReadWriteLock原理

#### 线程相关

*   线程和进程的区别
*   线程的启动和终止
*   线程间通信
*   等待/通知机制

#### 线程池相关

*   使用线程池的原因
*   线程池内部原理
*   线程池中的几种重要的参数及流程说明
*   线程池中几种常见的工作队列
*   几种常见的线程池及使用场景。

#### IO相关

*   IO相关面试问题-Socket
*   IO相关面试问题-BIO／NIO

### Android基础知识点

#### Activity相关

*   典型状况下的生命周期
*   异常情况下的生命周期
*   异常情况下的数据保存
*   各种情况下跳转到某个Activity时目标Activity及当前Activity的生命周期
*   Activity的启动模式及应用场景
*   进程和应用生命周期

#### Service相关

*   Service的定义及作用
*   Service两种启动方式 startService、 bindService 区别及生命周期
*   Service绑定服务的三种实现方式，扩展Binder类、使用Messenger、使用AIDL
*   关于启动服务与绑定服务间的转换问题 先绑定服务后启动服务、先启动服务后绑定服务
*   服务Service与线程Thread的区别
*   Android 5.0以上的隐式启动问题及其解决方案
*   如何保证服务不被杀死
*   IntentService的使用及原理

#### BroadcastReceiver相关

*   BroadcastReceiver定义及作用、应用场景
*   BroadcastReceiver的注册方式，静态方式、动态方式
*   BroadcastReceiver注册与取消的时机
*   BroadcastReceiver的不同类型，普通广播，系统广播、有序广播、粘性广播、应用类广播

#### Fragment相关

*   Fragment生命周期
*   Fragment的懒加载
*   Fragment之间的通信
*   FragmentPagerAdapter与FragmentStatePagerAdapter的区别
*   为什么不建议直接通过使用new Fragment的方式传入数据

#### 序列化相关

*   序列化与反序列化的定义及区别
*   Serializable中serialVersionUID及transient关键字的作用
*   序列化：Parcelable和Serializable差异

#### IPC相关

*   在Android中什么样的情况下会使用多进程模式，如何开启多进程
*   Android为什么采用Binder做为IPC机制
*   IPC常用方式 使用Bundle、使用文件共享、使用Messenger、使用AIDL、使用ContentProvider、使用Socket
*   AIDL的语义
*   AIDL如何创建
*   AIDL生成Java文件详细分析

#### View事件机制相关

*   View的坐标体系
*   View滑动的几种方式，使用ScrollTo/ScrollBy、使用动画、改变布局参数
*   弹性滑动的原理及实现
*   View的事件分发机制，点击事件的传递规则，事件分发的源码解读
*   处理滑动冲突的场景及解决方法

#### View绘制相关

*   DecorView、Window、ViewRootImpl等概念
*   MeasureSpec概念
*   View的工作流程，measure过程、layout过程、draw过程
*   自定义View需要注意的事项
*   Activity、Window、View三者之间的关系

#### View动画相关

*   常用动画View动画（补间动画）、属性动画与帧动画
*   补间动画与属性动画区别
*   差值器和估值器理解
*   属性动画的工作原理

#### Handler相关

*   Handler机制之ThreadLocal
*   Handler机制之Looper、Handler、消息队列如何理解
*   Handler机制之Message的发送与取出
*   Handler机制之Message及Message的回收机制
*   Handler机制之循环消息队列的退出
*   Handler机制之内存泄漏
*   Handler机制之IdleHandle的理解及使用

#### AsyncTask相关

*   AsyncTask的使用和注意事项
*   AsyncTask几个重要的方法 doInBackgound、onProgressUpdate、onPostExecute等
*   AsyncTask的工作原理及源码理解

#### Bitmap压缩机回收相关

*   Bitmap所占内存
*   常用压缩图片方式
*   LruCache原理
*   DiskLruCache原理
*   LinkedHashMap原理

#### ListView与RecyclerView相关

*   ListView的原理和复用机制
*   ListView和RecyclerView的区别

#### 数据存储相关

*   常用数据库框架GreenDao,官方Room
*   数据库数据迁移问题
*   GreenDao中一对一，一对多，多对多关系
*   SharedPreferences使用及源码，commit与apply()方法的区别

### Android开源框架知识点

#### OkHttp相关

*   OkHttp的优点
*   OkHttp执行请求的整个流程
*   OkHttp中的拦截器
*   OkHttp中的同步请求与异步请求的理解及其源码
*   OkHttp中涉及到的设计模式
*   OkHttp底层网络请求实现，socket还是URLConnection

#### Retrofit相关

*   Retrofit执行请求的整个流程
*   Retrofit中ConverterFactory、CallAdapterFactory的理解
*   Retrofit中CallAdapter的适配器模式

#### RxJava相关

*   RxJava常用创建操作符 create、from、just、interval、range等
*   RxJava常用组合、合并操作符 combineLatest、join、merge、zip等
*   RxJava错误处理操作符 onErrorReturn、onErrorResumeNext、onExceptionResumeNext等
*   RxJava过滤操作符 filter、ofType、sample、take等
*   Rxjava背压相关理解
*   RxJava实际开发中的使用：网络请求轮询、网络请求嵌套回调、从磁盘 / 内存缓存中 获取缓存数据等

#### Glide相关

*   Glide的执行流程
*   Glide的缓存机制
*   Glide图片转换
*   Glide带进度的图片加载功能
*   Glide内存、磁盘缓存，优先级使用

#### ButterKnife相关

*   Java注解相关Annotation
*   Java注解相关之APT工具
*   ButterKnife注解框架原理

#### EventBus相关

*   EventBus原理，及索引类的使用

### Android性能优化

*   性能优化:布局优化、绘制优化、线程优化等
*   ANR异常:主线程执行了耗时操作，如BroadcastReceiver(前台广播10s,后台广播为60s)、Service(前台20s,后台200s)没有处理完相关任务等
*   OOM异常：内存溢出的原因
*   内存泄漏：内存泄露的几种场景，如单例模式引出的泄露、静态变量导致的泄露、属性动画导致的内存泄露等

### Android屏幕适配知识点

*   今日头条适配方式
*   宽高限定符适配方式
*   smallestWidth适配

### Android打包知识点

*   安卓签名的理解
*   Gradle多渠道打包

### Android架构知识点

*   MVC架构设计模式面试问题讲解
*   MVP架构设计模式面试问题讲解
*   MVVM架构设计模式面试问题讲解

### Android不同版本特性知识点

推荐阅读：
[Android不同版本下的特性](https://links.jianshu.com/go?to=https%3A%2F%2Fdeveloper.android.google.cn%2Fabout%2Fversions%2Fpie%2F)
[广播在7.0、8.0、9.0下的适配](https://links.jianshu.com/go?to=https%3A%2F%2Fdeveloper.android.google.cn%2Fguide%2Fcomponents%2Fbroadcasts)
[Android 6.0 权限下的适配](https://links.jianshu.com/go?to=https%3A%2F%2Fdeveloper.android.google.cn%2Fguide%2Ftopics%2Fpermissions%2Foverview)
[Android 7.0 应用共享文件（FileProvider)](https://links.jianshu.com/go?to=https%3A%2F%2Fdeveloper.android.google.cn%2Fabout%2Fversions%2Fnougat%2Fandroid-7.0-changes)
[Android 7.0 共享文件的使用方式](https://links.jianshu.com/go?to=https%3A%2F%2Fdeveloper.android.google.cn%2Freference%2Fandroid%2Fsupport%2Fv4%2Fcontent%2FFileProvider.html)

### 网络知识点

*   计算机网络三种体系架构，OSI体系架构（7层）、TCP/IP体系架构(4层)，五层体系架构
*   TCP的连接管理（三报文握手，四报文握手）
*   TCP与UDP的理解与区别
*   Http（HyberText Transfer Protocol）基本概念及报文结构
*   Http常见错误码
*   Http1.0与Http1.1与Http2.0的区别
*   Http中get请求与post请求的区别
*   Http中cookie与session的区别
*   Http与Https的区别
*   Https加密算法相关面试问题，签名证书，公钥私钥、数字摘要的理解

### 设计模式知识点

*   单例模式
*   Builder模式
*   装饰模式
*   策略模式
*   模板方法
*   观察者模式
*   等.....

### 算法知识点

*   常见的八大排序方式
*   时间复杂度的计算
*   链表相关算法，链表翻转，链表合并等
*   二叉树相关算法前序、中序、后序遍历（递归，迭代）
*   红黑树与BL树
*   等

## 总结

学习技术是一条慢长而艰苦的道路，不能靠一时激情，也不是熬几天几夜就能学好的，必须养成平时努力学习的习惯。所以：贵在坚持！

**最后如何才能让我们在面试中对答如流呢？**

答案当然是平时在工作或者学习中多提升自身实力的啦，那如何才能正确的学习，有方向的学习呢？有没有免费资料可以借鉴？为此我整理了一份Android学习资料路线：

![](https://upload-images.jianshu.io/upload_images/15233854-60663c5b11d2d8c0.png?imageMogr2/auto-orient/strip|imageView2/2/w/874/format/webp)

这里是一部分我工作以来以及参与过的大大小小的面试收集总结出来的一套**BAT大厂面试资料专题包**，在这里免费分享给大家，主要还是希望大家在如今大环境不好的情况下面试能够顺利一点，希望可以帮助到大家。**需要的小伙伴们可以 关注我的主页 获取免费领取方式~**

![](https://upload-images.jianshu.io/upload_images/15233854-30b2fb4da36b025b.png?imageMogr2/auto-orient/strip|imageView2/2/w/517/format/webp)

好了，今天的分享就到这里，如果你对在面试中遇到的问题，或者**刚毕业及工作几年迷茫不知道该如何准备面试并突破现状提升自己，对于自己的未来还不够了解不知道给如何规划，可以去我的主页加一下技术群**。来看看同行们都是如何突破现状，怎么学习的，来吸收他们的面试以及工作经验完善自己的之后的面试计划及职业规划。

最后，祝愿即将跳槽和已经开始求职的大家都能找到一份好的工作！

> 这些只是整理出来的部分面试题，后续会持续更新，希望通过这些高级面试题能够降低面试Android岗位的门槛，让更多的Android工程师理解Android系统，掌握Android系统。喜欢的话麻烦点击一个喜欢再关注一下~

#### 以下墙裂推荐阅读！！！

*   [Android学习笔记参考（敲黑板！！）](https://links.jianshu.com/go?to=https%3A%2F%2Fshimo.im%2Fdocs%2FVxOufNNdaD4WS61R)
*   [“寒冬未过”，阿里P9架构分享Android必备技术点，让你offer拿到手软！](https://www.jianshu.com/p/534cb0eb8d4e)
*   [毕业3年，我是如何从年薪10W的拖拽工程师成为30W资深Android开发者！](https://www.jianshu.com/p/0eda7b13e9fe)
*   [腾讯T3大牛带你了解 2019 Android开发趋势及必备技术点！](https://www.jianshu.com/p/da8dc5b9d3f3)
*   [八年Android开发，从码农到架构师分享我的技术成长之路，共勉！](https://www.jianshu.com/p/ea587bd3cfe5)

**如果你觉得还算有用的话，不妨把它们推荐给你的朋友~**

![](https://upload-images.jianshu.io/upload_images/15233854-e90328d2bcf537a7.png?imageMogr2/auto-orient/strip|imageView2/2/w/55/format/webp)

![](https://upload-images.jianshu.io/upload_images/15233854-e051d8a3d35dcfc2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**面试：如果不准备充分的面试，完全是浪费时间，更是对自己的不负责！**

> 如果想变得优秀那就去看那些优秀的人在学什么，做什么，想尽一切办法和他们交流；多出去看看这个世界，然后把自己逼到往死里学。

如今已是八月份了，马上即将迎来秋招和金九银十，大批的届毕业和未就业的程序员生即将进入求职面试的紧张阶段，在这里分享一下我从事Android多年以来的面试经验。

这篇攻略是我从事开发工作七八年来，去面试，以及面试别人的经验总结。其中大部分都是大企业面试常问的面试题，可以对照这查漏补缺，当然了，这里所列的肯定不可能覆盖全部方式，希望对大家找工作有帮助！

**文末会给大家分享我整理的全套Android面试专题及答案**（这些题我基本都刷了三遍以上，毕竟之前的年前突然裁员风波心里是有点迷）这里先放上部分面试题：

![](https://upload-images.jianshu.io/upload_images/15233854-6991761c35ad221f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/517/format/webp)


>注：答案在最下面，因为实际开发与参考答案会有所不同，再者怕误导大家的理解，所以这些面试题答案还是自己去理解！面试官会针对简历中提到的知识点由浅入深提问，所以不要背答案，多理解。

###Activity基础相关

* 说下Activity生命周期 ？
* Activity A 启动另一个Activity B 会调用哪些方法？如果B是透明主题的又或则是个DialogActivity呢 ？
* 说下onSaveInstanceState()方法的作用 ? 何时会被调用？
* 说下 Activity的四种启动模式、应用场景 ？
* 了解哪些Activity常用的标记位Flags？
* 说下 Activity跟window，view之间的关系？
* 横竖屏切换的Activity生命周期变化？
* 如何启动其他应用的Activity？
* Activity的启动过程？（**重点**）

###Fragment基础相关

* 谈一谈Fragment的生命周期 ？与Activity生命周期的不同 ？
* 谈谈Activity和Fragment的区别？
* Fragment中add与replace的区别（Fragment重叠）
* getFragmentManager、getSupportFragmentManager 、getChildFragmentManager之间的区别？
* FragmentPagerAdapter与FragmentStatePagerAdapter的区别与使用场景

###Service基础相关

* 谈一谈Service的生命周期？
* Service的两种启动方式？区别在哪？
* 如何保证Service不被杀死 ？
* 能否在Service开启耗时操作 ？怎么做 ？
* 用过哪些系统Service ？
* 了解ActivityManagerService吗？发挥什么作用（重点）

###Broadcast Receiver基础相关

* 广播有几种形式 ? 都有什么特点 ？
* 广播的两种注册方式 ？
* 广播发送和接收的原理了解吗 ？（Binder机制、AMS）

###ContentProvider基础相关

* ContentProvider了解多少？
* ContentProvider的权限管理？
* 说说ContentProvider、ContentResolver、ContentObserver 之间的关系？

###数据存储相关

* 描述一下Android数据持久存储方式？
* SharedPreferences的应用场景？注意事项？
* SharedPrefrences的apply和commit有什么区别？
* 了解SQLite中的事务操作吗？是如何做的
* 使用SQLite做批量操作有什么好的方法吗？
* 如何删除SQLite中表的个别字段？
* 使用SQLite时会有哪些优化操作？

###IPC（重点）

* Android中进程和线程的关系？区别？
* 如何开启多进程 ？应用是否可以开启N个进程 ？
* 为何需要IPC？多进程通信可能会出现的问题？
* Android中IPC方式、各种方式优缺点，为什么选择Binder？
* Binder机制的作用和原理？
* Binder框架中ServiceManager的作用？
* Bundle传递对象为什么需要序列化？Serialzable和Parcelable的区别？
* 讲讲AIDL？原理是什么？如何优化多模块都使用AIDL的情况？

###View相关

* 讲下View的绘制流程？
* MotionEvent是什么？包含几种事件？什么条件下会产生？
* 描述一下View事件传递分发机制？
* 如何解决View的事件冲突 ？举个开发中遇到的例子 ？
* scrollTo()和scollBy()的区别？
* Scroller是怎么实现View的弹性滑动？
* invalidate()和postInvalidate()的区别 ？
* SurfaceView和View的区别？
* 自定义View如何考虑机型适配 ?

###Handler相关

* 谈谈消息机制Handler ? 作用 ？有哪些要素 ？流程是怎样的 ？
* 一个线程能否创建多个Handler，Handler跟Looper之间的对应关系 ？
* 软引用跟弱引用的区别
* Handler 引起的内存泄露原因以及最佳解决方案
* 为什么系统不建议在子线程访问UI
* Looper死循环为什么不会导致应用卡死
* 使用Handler的postDealy后消息队列会有什么变化 ？
* 可以在子线程直接new一个Handler吗 ？怎么做 ？
* Message可以如何创建 ？哪种效果更好 ？为什么 ？

###线程（重点）相关

* 线程池的好处？线程池的几个参数的理解，四种线程池的使用场景
* Android中还了解哪些方便线程切换的类？
* 讲讲AsyncTask的原理
* IntentService有什么用 ？
* 直接在Activity中创建一个thread跟在service中创建一个thread之间的区别
* ThreadPoolExecutor的工作策略 ？
* Handler、Thread和HandlerThread的差别？
* ThreadLocal的原理
* 多线程是否一定会高效（优缺点）
* 多线程中,让你做一个单例,你会怎么做
* 除了notify还有什么方式可以唤醒线程
* 什么是ANR ? 什么情况会出现ANR ？如何避免 ？在不看代码的情况下如何快速定位出现ANR问题所在 ？

###Bitmap相关

* Bitmap使用需要注意哪些问题 ？
* Bitmap.recycle()会立即回收么？什么时候会回收？如果没有地方使用这个Bitmap，为什么垃圾回收不会直接回收？
* 一张Bitmap所占内存以及内存占用的计算
* Android中缓存更新策略 ？
* LRU的原理 ？

#####性能优化（重点）

* 图片的三级缓存中,图片加载到内存中,如果内存快爆了,会发生什么？怎么处理？
* 内存中如果加载一张500*500的png高清图片.应该是占用多少的内存?
* WebView的性能优化 ?
* Bitmap如何处理大图，如一张30M的大图，如何预防OOM
* 内存回收机制与GC算法(各种算法的优缺点以及应用场景)；GC原理时机以及GC对象
* 内存泄露和内存溢出的区别 ？AS有什么工具可以检测内存泄露
* 性能优化,怎么保证应用启动不卡顿? 黑白屏怎么处理?
* 强引用置为null，会不会被回收？
* ListView跟RecyclerView的区别
* ListView的adapter是什么adapter ？
* LinearLayout、FrameLayout、RelativeLayout性能对比，为什么？

###JNI相关

* 对JNI是否了解
* 如何加载NDK库 ？如何在JNI中注册Native函数，有几种注册方法 ？
* 你用JNI来实现过什么功能 ？怎么实现的 ？（加密处理、影音方面、图形图像处理）

###设计模式相关

* 你所知道的设计模式有哪些？
* 谈谈MVC、MVP和MVVM，好在哪里，不好在哪里 ？
* 封装p层之后.如果p层数据过大,如何解决
* 是否能从Android中举几个例子说说用到了什么设计模式 ？
* 装饰模式和代理模式有哪些区别 ？
* 实现单例模式有几种方法 ？懒汉式中双层锁的目的是什么 ？两次判空的目的又是什么？
* 用到的一些开源框架，介绍一个看过源码的，内部实现过程。
* Fragment如果在Adapter中使用应该如何解耦？

###Android进阶延伸点

* 如何进行单元测试，如何保证App稳定
* Android中如何查看一个对象的回收情况
* APK的大小如何压缩 ？
* 如何通过Gradle配置多渠道包？
* 插件化原理分析
* 组建化原理
* 跨组件通信
* 组件化中路由、埋点的实现
* Hook以及插桩技术
* Android的签名机制
* v3签名key和v2还有v1有什么区别
* Android5.0~10.0之间大的变化 ？
* 说下Measurepec这个类
* 请例举Android中常用布局类型，并简述其用法以及排版效率
* 区别Animation和Animator的用法，概述其原理
* 使用过什么图片加载库 ？Glide的源码设计哪里很微妙 ？
* 如何绕过9.0限制？
* 用过哪些网络加载库 ？OkHttp、Retrofit实现原理 ？
* 对于应用更新这块是如何做的 ？（灰度，强制更新、分区域更新）
* 会用Kotlin、Fultter吗 ？谈谈你的理解

#答篇

###1、说下Activity生命周期 ？

* **参考解答**：在正常情况下，Activity的常用生命周期就只有如下7个

* **onCreate()**：表示Activity正在被创建，常用来初始化工作，比如调用setContentView加载界面布局资源，初始化Activity所需数据等；
* **onRestart()**：表示Activity正在重新启动，一般情况下，当前Acitivty从不可见重新变为可见时，OnRestart就会被调用；
* **onStart()**：表示Activity正在被启动，此时Activity可见但不在前台，还处于后台，无法与用户交互；
* **onResume()**：表示Activity获得焦点，此时Activity可见且在前台并开始活动，这是与onStart的区别所在；
* **onPause()**：表示Activity正在停止，此时可做一些存储数据、停止动画等工作，但是不能太耗时，因为这会影响到新Activity的显示，onPause必须先执行完，新Activity的onResume才会执行；
* **onStop()**：表示Activity即将停止，可以做一些稍微重量级的回收工作，比如注销广播接收器、关闭网络连接等，同样不能太耗时；
* **onDestroy()**：表示Activity即将被销毁，这是Activity生命周期中的最后一个回调，常做回收工作、资源释放；
* **延伸**：从整个生命周期来看，onCreate和onDestroy是配对的，分别标识着Activity的创建和销毁，并且只可能有一次调用；
* 从Activity是否可见来说，onStart和onStop是配对的，这两个方法可能被调用多次；
* 从Activity是否在前台来说，onResume和onPause是配对的，这两个方法可能被调用多次；
* 除了这种区别，在实际使用中没有其他明显区别；

###2、谈一谈Fragment的生命周期？

* **参考回答：**

* Fragment从创建到销毁整个生命周期中涉及到的方法依次为：onAttach()→onCreate()→ onCreateView()→onActivityCreated()→onStart()→onResume()→onPause()→onStop()→onDestroyView()→onDestroy()→onDetach()，其中和Activity有不少名称相同作用相似的方法，而不同的方法有:
* **onAttach()**：当Fragment和Activity建立关联时调用；
* **onCreateView()**：当fragment创建视图调用，在onCreate之后；
* **onActivityCreated()**：当与Fragment相关联的Activity完成onCreate()之后调用；
* **onDestroyView()**：在Fragment中的布局被移除时调用；
* **onDetach()**：当Fragment和Activity解除关联时调用；

###3、谈一谈Service的生命周期？

* **参考回答**：Service的生命周期涉及到六大方法

* **onCreate()**：如果service没被创建过，调用startService()后会执行onCreate()回调；如果service已处于运行中，调用startService()不会执行onCreate()方法。也就是说，onCreate()只会在第一次创建service时候调用，多次执行startService()不会重复调用onCreate()，此方法适合完成一些初始化工作；
* **onStartComand()**：服务启动时调用，此方法适合完成一些数据加载工作，比如会在此处创建一个线程用于下载数据或播放音乐；
* **onBind()**：服务被绑定时调用；
* **onUnBind()**：服务被解绑时调用；
* **onDestroy()**：服务停止时调用；

###4、广播有几种形式 ? 都有什么特点 ？

* **参考回答：**

* **普通广播**：开发者自身定义 intent的广播（最常用），所有的广播接收器几乎会在同一时刻接受到此广播信息，接受的先后顺序随机；
* **有序广播**：发送出去的广播被广播接收者按照先后顺序接收，同一时刻只会有一个广播接收器能够收到这条广播消息，当这个广播接收器中的逻辑执行完毕后，广播才会继续传递，且优先级（priority）高的广播接收器会先收到广播消息。有序广播可以被接收器截断使得后面的接收器无法收到它；
* **本地广播**：仅在自己的应用内发送接收广播，也就是只有自己的应用能收到，数据更加安全，效率更高，但只能采用动态注册的方式；
* **粘性广播**：这种广播会一直滞留，当有匹配该广播的接收器被注册后，该接收器就会收到此条广播；

###5、ContentProvider了解多少？

**参考回答：**

ContentProvider作为四大组件之一，其主要负责存储和共享数据。与文件存储、SharedPreferences存储、SQLite数据库存储这几种数据存储方法不同的是，后者保存下的数据只能被该应用程序使用，而前者可以让不同应用程序之间进行数据共享，它还可以选择只对哪一部分数据进行共享，从而保证程序中的隐私数据不会有泄漏风险。

###说说最近Android的行情

最近时不时看到一些风凉话，动不动就说Android凉了，给大家几个建议哈，首先要耐得住寂寞，不要被外界所干扰；其次要制定长期系统的学习计划并持之以恒；最后，注意学习方式，不要休闲式学习，很多人只看书看博客，但是从来不去动手实践一下，那理解肯定不会太深刻，时间久了就容易忘。

## 文末

好了，今天的分享就到这里，如果你对在面试中遇到的问题，或者刚毕业及工作几年迷茫不知道该如何准备面试并突破现状提升自己，对于自己的未来还不够了解不知道给如何规划，可以加一下合作的技术群：887084983。来看看同行们都是如何突破现状，怎么学习的，来吸收他们的面试以及工作经验完善自己的之后的面试计划及职业规划。

> 这里放上一部分我工作以来以及参与过的大大小小的面试收集总结出来的一套**进阶学习的视频及面试专题资料包**，在这里[免费分享](https://jq.qq.com/?_wv=1027&k=55Swnyf)给大家，主要还是希望大家在如今大环境不好的情况下面试能够顺利一点，希望可以帮助到大家~
>点击下方**Android学习笔记参考**获取免费领取方式~

![](https://upload-images.jianshu.io/upload_images/15233854-0a0e851153c871cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

##### 未完待续。。。

> 这里只是整理出来的部分面试题，后续会持续更新，希望通过这些高级面试题能够降低面试Android岗位的门槛，让更多的Android工程师理解Android系统，掌握Android系统。喜欢的话麻烦点击一个喜欢在关注一下~

#### 以下墙裂推荐阅读！！！

*   [Android学习笔记参考（敲黑板！！）](https://links.jianshu.com/go?to=https%3A%2F%2Fshimo.im%2Fdocs%2FVxOufNNdaD4WS61R)
*   [“寒冬未过”，阿里P9架构分享Android必备技术点，让你offer拿到手软！](https://www.jianshu.com/p/534cb0eb8d4e)
*   [毕业3年，我是如何从年薪10W的拖拽工程师成为30W资深Android开发者！](https://www.jianshu.com/p/0eda7b13e9fe)
*   [腾讯T3大牛带你了解 2019 Android开发趋势及必备技术点！](https://www.jianshu.com/p/da8dc5b9d3f3)
*   [八年Android开发，从码农到架构师分享我的技术成长之路，共勉！](https://www.jianshu.com/p/ea587bd3cfe5)

**最后祝大家生活愉快~**

![](https://upload-images.jianshu.io/upload_images/15233854-e90328d2bcf537a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/55/format/webp)

![](https://upload-images.jianshu.io/upload_images/15233854-cfd3460891437761.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

最近金九银十面试旺季身边好多朋友都裸辞了，出去旅游了一圈之后，回来才发现，工作并没有想象中那么好找。朋友小A一心只想进大厂面试Android，于是面试了阿里巴巴、美团、滴滴等，最后在某个大厂经历了5轮面试后拿到了offer。今天我将他面试的大厂Android面试题目整理出来，希望能够帮助大家！

##阿里巴巴

* LRUCache原理
* 图片加载原理
* 模块化实现（好处，原因）
* JVM
* 视频加密传输
* 统计启动时长,标准
* 如何保持应用的稳定性
* ThreadLocal 原理
* 谈谈classloader
* 动态布局
* 热修复,插件化
* HashMap源码,SpareArray原理
* 性能优化,怎么保证应用启动不卡顿
* 怎么去除重复代码
* SP是进程同步的吗?有什么方法做到同步
* 介绍下SurfView
* HashMap实现原理，ConcurrentHashMap 的实现原理
* BroadcastReceiver，LocalBroadcastReceiver 区别
* Bundle 机制
* Handler 机制
* android 事件传递机制
* 线程间 操作 List
* App启动流程，从点击桌面开始
* 动态加载
* 类加载器
* OSGI
* Https请求慢的解决办法，DNS，携带数据，直接访问IP
* GC回收策略
* 画出 Android 的大体架构图
* 描述清点击 Android Studio 的 build 按钮后发生了什么，大体说清一个应用程序安装到手机上时发生了什么；
* 对 Dalvik、ART 虚拟机有基本的了解；
* Android 上的 Inter-Process-Communication 跨进程通信时如何工作的；
* App 是如何沙箱化，为什么要这么做；
* 权限管理系统（底层的权限是如何进行 grant 的）
* 进程和 Application 的生命周期；
* 系统启动流程 Zygote进程 –> SystemServer进程 –> 各种系统服务 –> 应用进程
* recycleview listview 的区别,性能
* 排序，快速排序的实现
* 树：B 树的介绍
* 图：有向无环图的解释
* TCP/UDP的区别
* synchronized与Lock的区别
* volatile
* Java线程池
* Java中对象的生命周期
* 类加载机制
* 双亲委派模型
* Android事件分发机制
* MVP模式
* RxJava
* 抽象类和接口的区别
* 集合 Set实现 Hash 怎么防止碰撞
* JVM 内存区域 开线程影响哪块内存
* 垃圾收集机制 对象创建，新生代与老年代
* 二叉树 深度遍历与广度遍历
* B树、B 树
* 消息机制
* 进程调度
* 进程与线程
* 死锁
* 进程状态
* JVM内存模型
* 并发集合了解哪些
* ConCurrentHashMap实现
* CAS介绍
* 开启线程的三种方式,run()和start()方法区别
* 线程池
* 常用数据结构简介
* 判断环（猜测应该是链表环）
* 排序，堆排序实现
* 链表反转
* 动态权限适配方案，权限组的概念
* 网络请求缓存处理，okhttp如何处理网络缓存的
* 图片加载库相关，bitmap如何处理大图，如一张30M的大图，如何预- - 防OOM
* 进程保活
* listview图片加载错乱的原理和解决方案
* https相关，如何验证证书的合法性，https中哪里用了对称加密，哪里用了非对称加密，对加密算法（如RSA）等是否有了解

**最近面试被怼了？缺面试题刷提升自己吗？**

**点击:**

[Android 学习，面试文档，视频收集大整理](https://links.jianshu.com/go?to=https%3A%2F%2Fshimo.im%2Fdocs%2FVxOufNNdaD4WS61R)

**来获取学习资料提升自己去挑战一下BAT面试难关吧**

![](https://upload-images.jianshu.io/upload_images/15233854-8eacad8141424cef.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

##美团

* static synchronized 方法的多线程访问和作用，同一个类里面两个synchronized方法，两个线程同时访问的问题
* 内部类和静态内部类和匿名内部类，以及项目中的应用
* handler发消息给子线程，looper怎么启动
* View事件传递
* activity栈
* 封装view的时候怎么知道view的大小
* arraylist和linkedlist的区别，以及应用场景
* 怎么启动service，service和activity怎么进行数据交互
* 下拉状态栏是不是影响activity的生命周期，如果在onStop的时候做了* 网络请求，onResume的时候怎么恢复
* view渲染

##今日头条

* 数据结构中堆的概念，堆排序
* 死锁的概念，怎么避免死锁
* ReentrantLock 、synchronized和volatile（n面）
* HashMap
* singleTask启动模式
* 用到的一些开源框架，介绍一个看过源码的，内部实现过程。
* 消息机制实现
* ReentrantLock的内部实现
* App启动崩溃异常捕捉
* 事件传递机制的介绍
* ListView的优化
* 二叉树，给出根节点和目标节点，找出从根节点到目标节点的路径
* 模式MVP，MVC介绍
* 断点续传的实现
* 集合的接口和具体实现类，介绍
* TreeMap具体实现
* synchronized与ReentrantLock
* 手写生产者/消费者模式
* 逻辑地址与物理地址，为什么使用逻辑地址
* 一个无序，不重复数组，输出N个元素，使得N个元素的和相加为M，* 给出时间复杂度、空间复杂度。手写算法
* .Android进程分类
* 前台切换到后台，然后再回到前台，Activity生命周期回调方法。弹出Dialog，生命值周期回调方法。
* Activity的启动模式

##爱奇艺

* RxJava的功能与原理实现
* RecycleView的使用，原理，RecycleView优化
* ANR的原因
* 四大组件
* Service的开启方式
* Activity与Service通信的方式
* Activity之间的通信方式
* HashMap的实现，与HashSet的区别
* JVM内存模型，内存区域
* Java中同步使用的关键字，死锁
* MVP模式
* Java设计模式，观察者模式
* Activity与Fragment之间生命周期比较
* 广播的使用场景

![](https://upload-images.jianshu.io/upload_images/15233854-f1f922ea4cd339fa.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##百度

* Bitmap 使用时候注意什么？
* Oom 是否可以try catch ？
* 内存泄露如何产生？
* 适配器模式，装饰者模式，外观模式的异同？
* ANR 如何产生？
* String buffer 与string builder 的区别？
* 如何保证线程安全？
* java四中引用
* Jni 用过么？
* 多进程场景遇见过么？
* 关于handler，在任何地方new handler 都是什么线程下
* sqlite升级，增加字段的语句
* bitmap recycler 相关
* 强引用置为null，会不会被回收？
* glide 使用什么缓存？
* Glide 内存缓存如何控制大小？
* 如何保证多线程读写文件的安全？

##携程

* Activity启动模式
* 广播的使用方式，场景
* App中唤醒其他进程的实现方式
* AndroidManifest的作用与理解
* List,Set,Map的区别
* HashSet与HashMap怎么判断集合元素重复
* Java中内存区域与垃圾回收机制
* EventBus作用，实现方式，代替EventBus的方式
* Android中开启摄像头的主要步骤

##网易

* concurrenthashmap
* volatile
* synchronized与Lock
* Java线程池
* wait/notify
* NIO
* 垃圾收集器
* Activity生命周期
* AlertDialog,popupWindow,Activity区别

##小米

* String 为什么要设计成不可变的？
* fragment 各种情况下的生命周期
* Activity 上有 Dialog 的时候按 home 键时的生命周期
* 横竖屏切换的时候，Activity 各种情况下的生命周期
* Application 和 Activity 的 context 对象的区别
* 序列化的作用，以及 Android 两种序列化的区别。
* List 和 Map 的实现方式以及存储方式。
* 静态内部类的设计意图。
* 线程如何关闭，以及如何防止线程的内存泄漏

以上这些就是他面试大厂时遇到的Android面试题目，希望能帮助到大家，最后嘱咐大家一句：**“真正面试中，尤其是大公司中面试，没有固守成规的问法，唯有实力认真才能赢”。**

> 面试：如果不准备充分的面试，完全是浪费时间，更是对自己的不负责！
> 
> 金九银十面试季，赶快去为自己的面试做足准备吧！

不管怎么样，不论是什么样的大小面试，要想不被面试官虐的不要不要的，只有刷爆面试题题做好全面的准备，当然除了这个还需要在平时把自己的基础打扎实，这样不论面试官怎么样一个知识点里往死里凿，你也能应付如流啊~

**快来获取学习资料提升自己去挑战一下BAT面试难关吧！**

[Android 学习，面试文档，视频收集大整理](https://links.jianshu.com/go?to=https%3A%2F%2Fshimo.im%2Fdocs%2FVxOufNNdaD4WS61R)

![](https://upload-images.jianshu.io/upload_images/15233854-0e3ace121a77df2d.png?imageMogr2/auto-orient/strip|imageView2/2/w/835/format/webp)

**祝大家面试顺利！**

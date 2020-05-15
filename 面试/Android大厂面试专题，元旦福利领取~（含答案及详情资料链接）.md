![](http://upload-images.jianshu.io/upload_images/15233854-1df405c3b45191a4?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 我整理的这些资料比较乱，基本都是针对一些公司的面试题而整理的，我这里就不单独以公司为单位整理，我自己进行了一些分类，循序渐进，由基础到深入，由易到简，希望对大家有所帮助。
> 
> 对于每个问题我都会附上了1-3个答案，都是我自己摸索消化（真心花了很多时间），然后用一些觉得写的比较好的文章作为答案。这样就可以节省大家自己去搜索的时间，把时间用在正确的东西上，需要获取答案链接的请见文章底部。
> 
> 其实我可以直接以简易的答案写出来，看到过简易答案的面试题总结，但是感觉这并帮助不了同学们去深刻理解，三思之下还是采用别人长篇或者精简的答案，希望大家理解。

# Android 面试题分类：

*   1.四大组件
*   2.Fragment
*   3.自定义组件、动画
*   4.存储
*   5.网络
*   6.图片
*   7.布局
*   8.性能优化
*   9.JNI
*   10.进程间通信（简称：IPC）
*   11.WebView
*   12.进程保活
*   13.杂7杂8

# 一.四大组件

- Activity

- Service

- Content Provider

- Broadcast Receiver

**请阅读上面的基础知识**

1.  四大组件是什么
2.  四大组件的生命周期
3.  Activity之间的通信方式
4.  横竖屏切换的时候，Activity 各种情况下的生命周期
5.  Activity与Fragment之间生命周期比较
6.  Activity上有Dialog的时候按Home键时的生命周期
7.  两个Activity 之间跳转时必然会执行的是哪几个方法？
8.  Activity的四种启动模式对比以及使用场景
9.  Activity状态保存与恢复
10.  Activity 怎么和Service 绑定
11.  Service和Activity怎么进行数据交互？
12.  Service的开启方式
13.  请描述一下Service 的生命周期
14.  谈谈你对ContentProvider的理解
15.  ContentProvider、ContentResolver、ContentObserver 之间的关系
16.  请描述一下广播BroadcastReceiver的理解（实现原理）
17.  广播的分类
18.  广播使用的方式和场景
19.  本地广播和全局广播有什么差别？
20.  Application 和 Activity 的 Context 对象的区别

# 二.Fragment

1.  什么是Fragment
2.  为什么要用Fragment
3.  Fragment与Activity的通信方式
4.  Fragment各种情况下的生命周期
5.  Fragment之间传递数据的方式？
6.  Fragment的add与replace的区别
7.  用Fragment有遇过什么坑吗，怎么解决
8.  getFragmentManager，getSupportFragmentManager ，getChildFragmentManager三者之间的区别
9.  FragmentPagerAdapter与FragmentStatePagerAdapter的区别与使用场景

# 三.自定义组件、动画

1.  描述一下View的绘制流程
2.  说说自定义view的几个构造函数
3.  View 里面的 onSavedInstanceState和onRestoreInstanceState的作用
4.  onLayout() 和Layout()的区别
5.  描述一下getX、getRawX、getTranslationX
6.  Android中的动画有哪几类，它们的特点和区别是什么
7.  Interpolator和TypeEvaluator的作用
8.  请描述一下View事件传递分发机制
9.  事件分发中的onTouch 和onTouchEvent 有什么区别，又该如何使用？
10.  View和ViewGroup分别有哪些事件分发相关的回调方法
11.  View刷新机制

# 四.存储

1.  描述一下你知道的数据存储方式
2.  SharedPreferences的应用场景，核心原理是什么
3.  SharedPreferences是线程安全的吗？
4.  描述一下图片存储在本地的方式
5.  sqlite升级，增加字段的语句
6.  数据库框架对比和源码分析
7.  数据库的优化
8.  数据库数据迁移问题

# 五.网络

1.  描述一次网络请求的流程
2.  HTTP报文结构
3.  HttpClient和HttpURLConnection的区别
4.  Volley，okhttp，retrofit之间的区别和核心原理和使用场景
5.  描述一下https
6.  https中哪里用了对称加密，哪里用了非对称加密，对加密算法（如RSA）等是否有了解?
7.  说一下三次握手，四次挥手的具体细节
8.  我经常用面试问别人这道题，哈哈，为什么不能两次握手呢？要三次？
9.  描述一下socket是什么东西
10.  从网络加载一个10M的图片，说下注意事项
11.  TCP与UDP的区别
12.  client如何确定自己发送的消息被server收到?
13.  WebSocket与socket的区别
14.  网络请求缓存处理，okhttp如何处理网络缓存的
15.  自己去设计网络请求框架，怎么做？（随便套个开源框架的原理）

# 六.图片

1.  说一下OOM的原因，如何避免
2.  说一下三级缓存的原理
3.  描述一下内存缓存的容器
4.  LruCache其实是一个Hash表，内部使用的是LinkedHashMap存储数据
5.  图片库对比
6.  图片库的源码分析
7.  图片框架缓存实现
8.  郭霖大神写了几篇文章介绍Glide，都有详细介绍
9.  LRUCache原理
10.  自己去实现图片库，怎么做？（随便套个开源框架的原理）
11.  说说Glide内存缓存的具体实现？

# 七.布局

1.  说一下布局性能的排序，谁的效率最高
2.  描述一下约束布局
3.  关于布局优化的方案
4.  怎么检测布局深度
5.  LinearLayout、RelativeLayout、FrameLayout的特性及对比，并介绍使用场景。

# 八.性能优化

PS：性能优化包括内存，处理效率，视觉流畅度，CPU，电量，流量等方面，针对手机的性能去做相应的方案。个人认为更应该把握好内存优化、处理效率（代码质量）、视觉流畅度（布局优化）。

1.  ANR产生的原因是什么？
2.  oom是什么？
3.  什么情况导致oom？
4.  有什么解决方法可以避免OOM？
5.  Oom 是否可以try catch？为什么？
6.  内存泄漏是什么？
7.  什么情况导致内存泄漏？
8.  如何防止线程的内存泄漏？
9.  内存泄露的解决方法
10.  内存泄漏和内存溢出区别？
11.  如何对Android 应用进行性能分析以及优化?
12.  怎么去除无用代码？
13.  性能优化如何分析systrace？
14.  用IDE如何分析内存泄漏？
15.  跑一段你觉得有问题的代码段，gc，再跑，再gc，看看内存会不会一直上升
16.  Java多线程引发的性能问题，怎么解决？
17.  启动太慢怎么解决？
18.  怎么保证应用启动不卡顿？
19.  App启动崩溃异常捕捉
20.  自定义View注意事项
21.  减少不必要的调用invalidate()方法
22.  现在下载速度很慢,试从网络协议的角度分析原因,并优化(提示：网络的5层都可以涉及)。
23.  Https请求慢的解决办法（提示：DNS，携带数据，直接访问IP）
24.  如何保持应用的稳定性
25.  内存，布局优化，代码质量，数据结构效率，针对业务合理的设计框架
26.  RecyclerView和ListView的性能对比
27.  ListView的优化
28.  RecycleView优化
29.  View渲染
30.  Bitmap如何处理大图，如一张30M的大图，如何预防OOM
31.  java中的四种引用的区别以及使用场景
32.  强引用置为null，会不会被回收？

# 九.JNI

1.  请介绍一下NDK
2.  什么是NDK库?
3.  如何在JNI中注册native函数，有几种注册方式?
4.  Java如何调用c、c++语言？
5.  JNI如何调用java层代码？
6.  你用JNI来实现过什么功能吗？怎么实现的？

# 十.进程间通信（简称：IPC）

1.  进程间通信的方式？
2.  Binder机制的作用和原理
3.  简述IPC？
4.  什么是AIDL？
5.  AIDL解决了什么问题？
6.  AIDL如何使用？
7.  Android进程分类？
8.  进程和 Application 的生命周期？
9.  进程调度
10.  谈谈对进程共享和线程安全的认识

# 十一.WebView

1.  描述一下Webview的作用
2.  WebView的内核是什么
3.  描述一下WebView与js的交互方式
4.  描述一下WebView的缓存机制
5.  关于WebView的优化你知道哪些
6.  有没有用过第三方WebView组件？讲一讲优势

# 十二.进程保活

1.  做过进程保活吗？
2.  5.0下和5.0上的保活方式了解吗？
3.  描述一下进程回收的过程
4.  如何降低进程的oom_adj

# 十三.杂7杂8

1.  Handler机制和底层实现
2.  Handler、Thread和HandlerThread的差别
3.  handler发消息给子线程，looper怎么启动？
4.  关于Handler，在任何地方new Handler 都是什么线程下?
5.  ThreadLocal原理，实现及如何保证Local属性？
6.  请解释下在单线程模型中Message、Handler、Message Queue、Looper之间的关系
7.  AsyncTask机制
8.  AsyncTask原理及不足
9.  如何取消AsyncTask？
10.  为什么不能在子线程更新UI？
11.  LruCache默认内存缓存大小
12.  ContentProvider的权限管理(解答：读写分离，权限控制-精确到表级，URL控制)
13.  如何通过广播拦截和abort一条短信？
14.  广播是否可以请求网络？
15.  子线程可以，主线程超过10s引起anr
16.  广播引起anr的时间限制是多少？
17.  描述一下Activity栈
18.  Android线程有没有上限？
19.  线程池有没有上限？
20.  ListView重用的是什么？
21.  Android为什么引入Parcelable？
22.  有没有尝试简化Parcelable的使用？
23.  ListView 中图片错位的问题是如何产生的?
24.  混合开发有了解吗？
25.  知道哪些混合开发的方式？说出它们的优缺点和各自使用场景？（解答：比如:RN，weex，H5，小程序，WPA等)
26.  屏幕适配的处理技巧都有哪些?
27.  服务器只提供数据接收接口，在多线程或多进程条件下，如何保证数据的有序到达？
28.  动态布局的理解
29.  画出 Android 的大体架构图
30.  Recycleview和ListView的区别
31.  ListView图片加载错乱的原理和解决方案
32.  动态权限适配方案，权限组的概念
33.  Android系统为什么会设计ContentProvider？
34.  Bitmap 使用时候注意什么？
35.  Bitmap的recycler()
36.  Android中开启摄像头的主要步骤
37.  ViewPager使用细节，如何设置成每次只初始化当前的
38.  点击事件被拦截，但是想传到下面的View，如何操作？
39.  问题就是viewgroup被拦截，要传到子view那里，好好看这篇分发机制的文章，你就知道了
40.  描述一下微信主页面的实现方式
41.  invalidate和postInvalidate的区别及使用
42.  Activity-Window-View三者的差别
43.  谈谈对Volley的理解
44.  ActivityThread，AMS，WMS的工作原理
45.  LaunchMode应用场景
46.  SpareArray原理
47.  请介绍下ContentProvider 是如何实现数据共享的？
48.  IntentService原理及作用是什么？
49.  ApplicationContext和ActivityContext的区别
50.  封装View的时候怎么知道view的大小
51.  AndroidManifest的作用与理解

![](http://upload-images.jianshu.io/upload_images/15233854-6b9919faf6391443?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**所有的资料我都整理出来放在了群里面，希望可以帮助在这个行业发展的朋友和童鞋们，在论坛博客等地方少花些时间找资料，把有限的时间，真正花在学习上，所以我把这些资料，分享出来。相信对于已经工作和遇到技术瓶颈或者写博客码友，在这份资料中一定都有你需要的内容。**

![image](http://upload-images.jianshu.io/upload_images/15233854-79828a4e3d294726?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**资料领取**

**关注我私信回复【干货分享】**

**领取获取往期高级UI、性能优化、架构师课程、NDK、混合式开发（ReactNative+Weex）微信小程序、Flutter全方面的Android进阶实践技术**

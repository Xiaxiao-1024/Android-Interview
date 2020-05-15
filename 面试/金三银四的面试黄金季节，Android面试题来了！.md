**金三银四的跳槽季节，你准摆好了吗？**

首先我们分享一个Android知识图谱。

![image.png](https://upload-images.jianshu.io/upload_images/15233854-915d495f42f0a7ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


下面是一些面试官基本必问的问题，请一定要去了解！

*   基础知识 – 四大组件（生命周期，使用场景，如何启动）

*   java基础 – 数据结构，线程，mvc框架 通信 – 网络连接（HttpClient，HttpUrlConnetion），Socket

*   数据持久化 – SQLite，SharedPreferences，ContentProvider

*   性能优化 – 布局优化，内存优化，电量优化 安全 – 数据加密，代码混淆，WebView/Js调用，https

*   UI– 动画

*   其他 – JNI，AIDL，Handler，Intent等

*   开源框架 – Volley，Gilde，RxJava等（简历上写你会的，用过的） 拓展 – Android6.0/7.0/8.0特性，kotlin语言，I/O大会

######在这由于文字很多，我总结了Android面试所涉及到的常问范围及常问面试题免费分享给大家，文末有领取！

#### 1、Android本身的api并未声明会抛出异常，则其在运行时有无可能抛出 runtime异常，你遇到过吗？诺有的话会导致什么问题？如何解决？

#### 2、IntentService有何优点?

#### 3、如果后台的Activity由于某原因被系统回收了，如何在被系统回收之前保存 当前状态？

#### 4、如何将一个Activity设置成窗口的样式。

#### 5、如何退出Activity？如何安全退出已调用多个Activity的Application？

对于单一 Activity 的应用来说，退出很简单，直接 finish()即可。

当然， 也可以用 killProcess()和 System.exit()这样的方法。 对于多个 activity

##### 但是要注意的是：

*   在 2.1 之前，可以使用 ActivityManager 的 restartPackage 方法。 它可以直接结束整个应用。在使用时需要权限 android.permission.RESTART_PACKAGES。 注意不要被它的名字迷惑。 可是，在 2.2，这个方法失效了。
*   在 2.2 添加了一个新的方法，killBackground Processes()， 需要权限 android.permission.KILL_BACKGROUND_PROCESSES。可惜的是，它和 2.2 的 restartPackage 一样，根本起不到应有的效果。 另外还有一个方法，就是系统自带的应用程序管理里，强制结束程序的方法， forceStopPackage()。它需要权限 android.permission.FORCE_STOP_PACKAGES。并且 需要添加 android:sharedUserId="android.uid.system"属性

    ##### 同样可惜的是

    该方法是非公 开的，他只能运行在系统进程，第三方程序无法调用。 因为需要在 Android.mk 中添加 LOCAL_CERTIFICATE := platform。 而 Android.mk 是用于在 Android 源码下编译程序用的。 从以上可以看出，
*   在 2.2，没有办法直接结束一个应用，而只能用自己的办法间接办到。 现提供几个方法，供参考：
*   1、抛异常强制退出： 该方法通过抛异常，使程序 Force Close。 验证可以，但是，需要解决的问题是，如何使程序结束掉，而不弹出 Force Close 的窗口。
*   2、记录打开的 Activity： 每打开一个 Activity，就记录下来。在需要退出时，关闭每一个 Activity 即可。 -3、发送特定广播： 在需要结束应用时，发送一个特定的广播，每个 Activity 收到广播后，关闭即可。
*   4、递归退出 在打开新的 Activity 时使用 startActivityForResult，然后自己加标志，在 onActivityResult 中处理，递归关闭。

除了第一个，都是想办法把每一个 Activity都结束掉，间接达到目的。但是这样做同样不完 美。你会发现，如果自己的应用程序对每一个 Activity都设置了 nosensor，在两个 Activity 结束的间隙，sensor 可能有效了。但至少，我们的目的达到了，而且没有影响用户使用。 为了编程方便，最好定义一个 Activity基类，处理这些共通问题。

#### 6、AIDL的全称是什么？如何工作？能处理哪些类型的数据？

全称是：AndroidInterface Define Language

在 Android 中, 每个应用程序都可以有自己的进程. 在写UI应用的时候, 经常要用到 Service.

在不同的进程中, 怎样传递对象呢?显然, Java中不允许跨进程内存共享.因此传递 对象, 只能把对象拆分成操作系统能理解的简单形式, 以达到跨界对象访问的目的. 在J2EE 中,采用RMI 的方式,可以通过序列化传递对象.在 Android 中, 则采用 AIDL 的方式. 理论上 AIDL 可以传递 Bundle,实际上做起来却比较麻烦。

*   AIDL(AndRoid 接口描述语言)是一种借口描述语言;
*   编译器可以通过aidl 文件生成一段代 码
*   通过预先定义的接口达到两个进程内部通信进程的目的.
*   如果需要在一个 Activity中, 访 问另一个Service中的某个对象, 需要先将对象转化成AIDL可识别的参数(可能是多个参数),
*   然后使用 AIDL 来传递这些参数, 在消息的接收端, 使用这些参数组装成自己需要的对象.
*   AIDL 的 IPC 的机制和 COM 或 CORBA 类似, 是基于接口的，但它是轻量级的。
*   它使用代 理类在客户端和实现层间传递值.
*   如果要使用 AIDL, 需要完成 2 件事情:
*   1.  引入AIDL 的相 关类.;
*   2.调用aidl 产生的 class.

##### AIDL 的创建方法: AIDL

语法很简单,可以用来声明一个带一个或多个方法的接口，也可以传递参数和返回值。 由于远程调用的需要, 这些参数和返回值并不是任何类型. 下面是些 AIDL 支持的数据类型:

*   1.  不需要 import 声明的简单 Java 编程语言类型(int,boolean 等)
*   2.  String, CharSequence 不需要特殊声明
*   3.  List, Map 和 Parcelables 类型,这些类型内所包含的数据成员也只能是简单数据类型, String 等其他比支持的类型.

#### 7、请解释下Android程序运行时权限与文件系统权限的区别。

```
运行时权限 Dalvik(android 授权)
复制代码
```

文件系统 linux 内核授权

#### 8、系统上安装了多种浏览器，能否指定某浏览器访问指定页面？请说明原由。

#### 9、android系统的优势和不足

##### Android 平台手机 5 大优势：

##### 5 大不足：

#### 10、Android dvm的进程和Linux的进程, 应用程序的进程是否为同一个概念

#### 11、sim卡的EF文件是什么？有何作用

sim卡的文件系统有自己规范，主要是为了和手机通讯，sim本 身可以有自己的操 作系统，EF就是作存储并和手机通讯用的

#### 12、嵌入式操作系统内存管理有哪几种， 各有何特性

```
页式，段式，段页，用到了MMU,虚拟空间等技术
复制代码
```

#### 13、什么是嵌入式实时操作系统, Android 操作系统属于实时操作系统吗?

嵌入式实时操作系统是指当外界事件或数据产生时，能够接受并以足够快的 速度予以处理，其处理的结果又能在规定的时间之内来控制生产过程或对处理系 统作出快速响应，并控制所有实时任务协调一致运行的嵌入式操作系统。主要用 于工业控制、军事设备、 航空航天等领域对系统的响应时间有苛刻的要求，这 就需要使用实时系统。又可分为软实时和硬实时两种，而 android 是基于 linux 内核的，因此属于软实时。

#### 14、一条最长的短信息约占多少byte?


#### 15、有一个一维整型数组int[]data保存的是一张宽为width，高为height的图 片像素值信息。请写一个算法，将该图片所有的白色不)像素点透明(0xffffffff 的透明度调整为50%。

#### 16、如何将SQLite数据库(dictionary.db文件)与apk文件一起发布

#### 17、如何将打开res aw目录中的数据库文件?

#### 18、DDMS和TraceView的区别?

#### 19、java中如何引用本地语言 可以用JNI（java native interface java 本地接口）接口

#### 20、谈谈Android的IPC（进程间通信）机制

#### 21、NDK 是什么

#### 22.一道简单不易的算法题

```
        int  a = 10;  
        int b=5;
        怎么在不引入其他变量的情况下,让a和b互换？

    ```
    public class Test {
       int  a = 10;  
       int b=5;
    public static void main(String[] args) {
       a = a+b;
       b=a-b;
       a =a-b; 
       System.out.println("b="+b);
       System.out.println("a="+a);
     }
    }
    ----输出：
    b=10
    a=5      
    ```
复制代码
```

#### 23.你最近几年的计划是什么？

#### 24.你的项目中遇到了比较难以解决的问题？你又是如何解决的？ （提示：考察的是你分析问题和解决问题的能力）

#### 25.一些框架基本原理，设计模式的优势。

#### 26.自我介绍，（逻辑清晰，不可自我矛盾）

#### 27.你写过那些自定义组件？说一说是怎么使用的？

#### 27.tinker热修复的基本原理 ： 一定要形成自己的话术

#### 28.性能优化

#### 29.JVM和Dalvik虚拟机的工作原理

#### 30.APP的启动过程

**因为篇幅过长，所以小编在这里只整理出了一部分的面试答案，但我将所有的面试题及答案整理做成了文档，以及系统的进阶学习视频资料，免费分享给大家。**
**（包括Java在Android开发中应用、APP框架知识体系、高级UI、全方位性能调优，NDK开发，音视频技术，人工智能技术，跨平台技术等技术资料），希望能帮助到你面试前的复习，且找到一个好的工作，也节省大家在网上搜索资料的时间来学习。**

#####领取方式：关注+点赞+加群：185873940 免费获取！
​![image](http://upload-images.jianshu.io/upload_images/15405328-49428de20aa645c5?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

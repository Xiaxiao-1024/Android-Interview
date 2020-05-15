## **前言**

*   首先声明，楼主不是什么大牛，没有多牛逼的技术，只是公司扩招团队，有幸作为技术面试官，面试了这么多人之后的感想，希望对大家有一点点帮助。

## **准备**

*   当上级下达命令之后，身为下级得我就得去执行，而且要求快，求稳，求好的完成任务（ps：这是想象中的场景）

*   我们移动开发团队需要n名，所谓中级、高级的yi工程师，title的薪资，15-20K（钱不好拿，工作压力略大），然后作为面试官的我想了想，我该怎么样出题（没有笔试，楼主比较反感笔试，浪费面试同志的时间），

*   我们需要熟练使用NDK，对项目的优化有一定的经验，了解现在互联网中的一些新技术（重点）还有一些java的基础知识：以下是楼主的面试题：

**在这里由于文章篇幅较长，所以小编这里只放了一部分面试题的答案，关于这些面试题及答案小编整理了一个面试文档，需要更多面试题和完整面试答案的，文末有领取方式**
![image.png](https://upload-images.jianshu.io/upload_images/15233854-7c947bfe27b97480.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## **小伙伴们来面试了**
## 1、Android系统的架构

* **Android系统架构之应用程序**
    Android会同一系列核心应用程序包一起发布，该应用程序包包括email客户端，SMS短消息程序，日历，地图，浏览器，联系人管理程序等。所有的应用程序都是使用JAVA语言编写的。
* **Android系统架构之应用程序框架**
    开发人员可以完全访问核心应用程序所使用的API框架（android.jar）。该应用程序的架构设计简化了组件的重用;任何一个应用程序都可以发布它的功能块并且任何其它的应用程序都可以使用其所发布的功能块。
* **Android系统架构之系统运行库**
*  **Android系统架构之Linux 内核**
## 2.activity的生命周期
![image.png](https://upload-images.jianshu.io/upload_images/15233854-2d44c046f035a997.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 3.Fragment的生命周期

* **Fragment的生命周期**
* **Fragment与Activity生命周期对比**
## 4.Service生命周期

在Service的生命周期里，常用的有：

4个手动调用的方法

```
startService()    启动服务
stopService()    关闭服务
bindService()    绑定服务
unbindService()    解绑服务
```

5个内部自动调用的方法

```
onCreat()            创建服务
onStartCommand()    开始服务
onDestroy()            销毁服务
onBind()            绑定服务
onUnbind()            解绑服务
```

1.  手动调用startService()启动服务，自动调用内部方法：onCreate()、onStartCommand()，如果一个Service被startService()多次启动，那么onCreate()也只会调用一次。
2.  手动调用stopService()关闭服务，自动调用内部方法：onDestory()，如果一个Service被启动且被绑定，如果在没有解绑的前提下使用stopService()关闭服务是无法停止服务的。
3.  手动调用bindService()后，自动调用内部方法：onCreate()、onBind()。
4.  手动调用unbindService()后，自动调用内部方法：onUnbind()、onDestory()。
5.  startService()和stopService()只能开启和关闭Service，无法操作Service，调用者退出后Service仍然存在；bindService()和unbindService()可以操作Service，调用者退出后，Service随着调用者销毁。

## 5.Android中动画

Android中动画分别帧动画、补间动画和属性动画（Android 3.0以后的）

### 帧动画

帧动画是最容易实现的一种动画，这种动画更多的依赖于完善的UI资源，他的原理就是将一张张单独的图片连贯的进行播放，从而在视觉上产生一种动画的效果；有点类似于某些软件制作gif动画的方式。在有些代码中，我们还会看到android：oneshot="false" ，这个oneshot 的含义就是动画执行一次（true）还是循环执行多次。

```
<?xml version="1.0" encoding="utf-8"?>
<animation-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:drawable="@drawable/a_0"
        android:duration="100" />
    <item
        android:drawable="@drawable/a_1"
        android:duration="100" />
    <item
        android:drawable="@drawable/a_2"
        android:duration="100" />
</animation-list>
```

### 补间动画

补间动画又可以分为四种形式，分别是 alpha（淡入淡出），translate（位移），scale（缩放大小），rotate（旋转）。
补间动画的实现，一般会采用xml 文件的形式；代码会更容易书写和阅读，同时也更容易复用。Interpolator 主要作用是可以控制动画的变化速率 ，就是动画进行的快慢节奏。pivot 决定了当前动画执行的参考位置

```
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@[package:]anim/interpolator_resource"
    android:shareInterpolator=["true" | "false"] >
    <alpha
        android:fromAlpha="float"
        android:toAlpha="float" />
    <scale
        android:fromXScale="float"
        android:toXScale="float"
        android:fromYScale="float"
        android:toYScale="float"
        android:pivotX="float"
        android:pivotY="float" />
    <translate
        android:fromXDelta="float"
        android:toXDelta="float"
        android:fromYDelta="float"
        android:toYDelta="float" />
    <rotate
        android:fromDegrees="float"
        android:toDegrees="float"
        android:pivotX="float"
        android:pivotY="float" />
    <set>
        ...
    </set>
</set>
```

### 属性动画

## 6.Android中4大组件

* **Activity**
* **BroadCast Receiver**
* **Content Provider**
* **service**

## 7.Android中常用布局
## 8.消息推送的方式
## 9.android的数据存储

1.  使用SharedPreferences存储数据；它是Android提供的用来存储一些简单配置信息的一种机制，采用了XML格式将数据存储到设备中。只能在同一个包内使用，不能在不同的包之间使用。
2.  文件存储数据；文件存储方式是一种较常用的方法，在Android中读取/写入文件的方法，与Java中实现I/O的程序是完全一样的，提供了openFileInput()和openFileOutput()方法来读取设备上的文件。
3.  SQLite数据库存储数据；SQLite是Android所带的一个标准的数据库，它支持SQL语句，它是一个轻量级的嵌入式数据库。
4.  使用ContentProvider存储数据；主要用于应用程序之间进行数据交换，从而能够让其他的应用保存或读取此Content Provider的各种数据类型。
5.  网络存储数据；通过网络上提供给我们的存储空间来上传(存储)和下载(获取)我们存储在网络空间中的数据信息。

## 10.Activity启动模式

## 11.广播注册
## 12.Android中的ANR
## 13.ListView优化
## 14.Android数字签名
## 15.Android root机制

root指的是你有权限可以再系统上对所有档案有 "读" "写" "执行"的权力。root机器不是真正能让你的应用程序具有root权限。它原理就跟linux下的像sudo这样的命令。在系统的bin目录下放个su程序并属主是root并有suid权限。则通过su执行的命令都具有Android root权限。当然使用临时用户权限想把su拷贝的/system/bin目录并改属性并不是一件容易的事情。这里用到2个工具跟2个命令。把busybox拷贝到你有权限访问的目录然后给他赋予4755权限，你就可以用它做很多事了。

## 16.View、surfaceView、GLSurfaceView

#### View

显示视图，内置画布，提供图形绘制函数、触屏事件、按键事件函数等，必须在UI主线程内更新画面，速度较慢

#### SurfaceView

基于view视图进行拓展的视图类，更适合2D游戏的开发，是view的子类，类似使用双缓机制，在新的线程中更新画面所以刷新界面速度比view快

#### GLSurfaceView

基于SurfaceView视图再次进行拓展的视图类，专用于3D游戏开发的视图，是surfaceView的子类，openGL专用

#### AsyncTask

AsyncTask的三个泛型参数说明
## 17.Android i18n

I18n 叫做国际化。android 对i18n和L10n提供了非常好的支持。软件在res/vales 以及 其他带有语言修饰符的文件夹。如： values-zh 这些文件夹中 提供语言，样式，尺寸 xml 资源。

## 18.NDK

1.  NDK是一系列工具集合，NDK提供了一系列的工具，帮助开发者迅速的开发C/C++的动态库，并能自动将so和Java应用打成apk包。
2.  NDK集成了交叉编译器，并提供了相应的mk文件和隔离cpu、平台等的差异，开发人员只需要简单的修改mk文件就可以创建出so文件。

## 19.启动一个程序，可以主界面点击图标进入，也可以从一个程序中跳转过去，二者有什么区别？

通过主界面进入，就是设置默认启动的activity。在manifest.xml文件的activity标签中，写以下代码

```
<intent- filter>
<intent android:name=“android.intent.action.MAIN”>
<intent android:name=”android:intent.category.LAUNCHER”>
</intent-filter>
```

从另一个组件跳转到目标activity，需要通过intent进行跳转。具体

```
Intent intent=new Intent(this,activity.class),startActivity(intent)
```

## 20.内存溢出和内存泄漏有什么区别？何时会产生内存泄漏？

内存溢出：当程序运行时所需的内存大于程序允许的最高内存，这时会出现内存溢出；

内存泄漏：在一些比较消耗资源的操作中，如果操作中内存一直未被释放，就会出现内存泄漏。比如未关闭io,cursor。

## 21.sim卡的EF 文件有何作用

sim卡就是电话卡，sim卡内有自己的操作系统，用来与手机通讯的。Ef文件用来存储数据的。

## 22.Activity的状态有几种？

```
运行
暂停
停止
```

## 23.让Activity变成一个窗口
## 24.android:gravity与android:layout_gravity的区别
## 25.如何退出Activity

*   面到这里基本就快结束了，面试者的沟通表达呀，组织合作啊，这个我倒不太看中，我唯一看中你技术

## **面了三天一个都没招到**

*   楼主侧重原理，基础，因为我一直觉得，你基础掌握的扎实，一些知识了解过原理的实现，你掌握一门框架，MVC、MVP、MVVM三大框架，我觉得很快，框架的出现是为了让我们更好，更快的写我们的项目，而不是框架有多难，难得是我们自己，愿不愿意静下心来学....

*   楼主最后降低了题目的难度，当然工资也相应的降低，最后历时一周，招到我想要的。

*   楼主在面试的时候，基本都在半个小时以上，我觉得是对面试者的尊重，大老远跑到我们公司也不容易，都是敲代码的，我会给一些学习建议，哪些短板，该怎么补，哪里能找到（虽然不够全面，也是尽微薄之力，希望他们能尽快找到合适自己的工作）

## **建议**

当我们出去找工作，或者准备找工作的时候，我们一定要想，我面试的目标是什么，我自己的技术栈有哪些，近期能掌握的有哪些，我的哪些短板 ，列出来，有计划的去完成，别看前两天掘金一些大佬在驳来驳去 ，他们的观点是他们的，不要因为他们的观点，膨胀了自己，影响自己的学习节奏。基础很大程度决定你自己技术层次的厚度，你再熟练框架也好，也会比你便宜的，性价比高的替代，很现实的问题但也要有危机意识，当我们年级大了，有哪些亮点，与比我们经历更旺盛的年轻小工程师，竞争。

*   无论你现在水平怎么样一定要 **持续学习** 没有鸡汤，别人看起来的毫不费力，其实费了很大力，这四个字就是我的建议！！！！！！！！！
*   准备想说怎么样写简历，想象算了，我觉得，**技术就是你最好的简历**
*   我希望每一个努力生活的it工程师，都会得到自己想要的，因为我们很辛苦，我们应得的。

*   有什么问题想交流，欢迎给我简信，欢迎评论
### **【附】相关架构及资料**
![Android高级技术大纲](https://upload-images.jianshu.io/upload_images/15233854-1b8c62804dd8cacf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![面试资料整理](https://upload-images.jianshu.io/upload_images/15233854-2ad56e42af7f899e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640/format/webp)

### **资料领取**

###### **点赞+加群免费获取 [Android IOC架构设计](https://jq.qq.com/?_wv=1027&k=5kkiEEI)**

> **群里还有往期Android高级架构资料、源码、笔记、视频。高级UI、性能优化、架构师课程、NDK、混合式开发（ReactNative+Weex）微信小程序、Flutter全方面的Android进阶实践技术**


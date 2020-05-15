又要交房租了，工资怎么还不发？每个月总觉得钱不够花，工资总是跟不上消费，什么时候能涨个工资呢？

现在上班犹如上坟一样，已经找不到半点成就感，每天各种烦心事，各种琐事永远做不到。

我们为什么会离职？无非就是以上两种原因：钱没给够，干的不开心，想换个环境。如果你已经动了换工作的心思，趁着金三银四，可以行动准备起来了。

2018 年经历了互联网寒冬，今年还会更加糟糕，有读者告知上班的第一天就被 cai ，也就在前几天，滴滴官宣过冬，将 cai 员 15 %，波及员工超 2000 人，因此跳槽也要谨慎，如何跳槽拿到心仪的 offer 呢？

今天给大家分享下我整理的Android架构面试专题及相关学习资料，其中大部分都是大企业面试常问的面试题，可以对照这查漏补缺，当然了，这里所列的肯定不可能覆盖全部方式，不过也希望能对即将找工作的朋友起到一些帮助！
**在这由于文字很多，我总结了Android面试所涉及到的常问面试题及学习资料免费分享给大家，文末有领取！**

1.如何对 Android 应用进行性能分析
2.什么情况下会导致内存泄露
3.如何避免 OOM 异常
4.Android 中如何捕获未捕获的异常
5.ANR 是什么？怎样避免和解决 ANR（重要）
6.Android 线程间通信有哪几种方式
7.Devik 进程，linux 进程，线程的区别
8.描述一下 android 的系统架构
9.android 应用对内存是如何限制的?我们应该如何合理使用内存？
10. 简述 android 应用程序结构是哪些
11.请解释下 Android 程序运行时权限与文件系统权限的区别
12.Framework 工作方式及原理，Activity 是如何生成一个 view 的，机制是什么
13.多线程间通信和多进程之间通信有什么不同，分别怎么实现
14.Android 屏幕适配
15.什么是 AIDL 以及如何使用
16.Handler 机制
17.事件分发机制
18.子线程发消息到主线程进行更新 UI，除了 handler 和 AsyncTask，还有什么
19.子线程中能不能 new handler？为什么
20.Android 中的动画有哪几类，它们的特点和区别是什么
21.如何修改 Activity 进入和退出动画
22.SurfaceView & View 的区别
23.开发中都使用过哪些框架、平台
24.使用过那些自定义View
25.自定义控件：绘制圆环的实现过程
26.自定义控件：摩天轮的实现过程
27.GridLayout的使用
28.流式布局的实现过程
29.第三方登陆
30.第三方支付
31.进程保活

**这里小编根据上面的面试题整理出了一份关于高阶安卓的学习思路及资料，希望能帮助大家的学习提升技术，早日拿到大厂的offer。**

**1.Java语言进阶与Android相关技术内核**
Android应用是由Java语言进行开发的，SDK也是由Java语言编写，所以我们要学习java语言。另外，虽说kotlin语言得到了Android官方的热推，但是kotlin也是编译成了java语言再运行的。对于Android来说，只要SDK没有用kotlin重写，那么Java语言是都需要学习的。而且Android apk的后台服务器程序大概率是java语言构建，所以学习java也是一种必然。
![image.png](https://upload-images.jianshu.io/upload_images/15233854-dbec4e0e291d0625.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**2.Android前沿技术**

现在去很多公司面试，除了你具备基本的能够写一个高性能app的能力后，一般都会在自己的app里面加一些现有的相对较 666 的技术，这些技术我们称之为前沿技术。他们一般包含热升级，热修复，App Instant，强制更新，组件化路由架构Arouter，RxJava，IOC架构方法，Hook技术等等，当然，这些技术你不能只会用，你需要知道他的原理，有时候，你还需要知道如何对这些架构进行改进。

![](http://upload-images.jianshu.io/upload_images/15233854-cfb3a05f69cf3a60?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**3.NDK 模块开发**

音视频/高清大图片/人工智能/直播/抖音等等这年与用户最紧密，与我们生活最相关的技术一直都在寻找最终的技术落地平台，以前是windows系统，而现在则是移动系统了，移动系统中又是以Android占比绝大部分为前提，所以Android NDK技术已经是我们必备技能了。

![](http://upload-images.jianshu.io/upload_images/15233854-c956e219f7f97ccb?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**4.移动架构师**

架构师不是天生的，在Android里面最常用的架构无外乎 MVC，MVP，MVVM，但是这些思想如果和模块化，层次化，组件化混和在一起，那就不是一件那么简单的事了，我们需要一个真正身经百战的架构师才能讲解透彻其中蕴含的深理。

![](http://upload-images.jianshu.io/upload_images/15233854-59504bcadd1ece38?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**5.360° Android app全方位性能调优**

一个app的性能好不好我们需要从两个层面努力。第一个层面：从写代码的时候就需要注意，让自己的代码是高性能高可用的代码，这个过程是书写高性能代码；第二个层面：对已经成型的代码通过工具检查代码的问题，通过检查到的问题来指导我们进行代码的删改，这个过程被称为调优。
如何写出高性能的代码呢？那就需要我们具备深厚的代码功底，这就是代码的基础，另外一个就是对写好的代码进行调优。![image.png](https://upload-images.jianshu.io/upload_images/15233854-cd4b285a9a2db639.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**除了上面的面试题，我还总结出了互联网公司Android程序员面试涉及到的绝大部分面试题及答案，并整理做成了文档，以及系统的进阶学习视频资料，免费分享给大家。
（包括Java在Android开发中应用、APP框架知识体系、高级UI、全方位性能调优，NDK开发，音视频技术，人工智能技术，跨平台技术等技术资料），希望能帮助到你面试前的复习，且找到一个好的工作，也节省大家在网上搜索资料的时间来学习。**

### **领取方式：关注+点赞+加群：185873940 免费获取！**

**点击链接加入群聊【Android IOC架构设计】：[https://jq.qq.com/?_wv=1027&k=5tIZkaU](https://jq.qq.com/?_wv=1027&k=5tIZkaU)**

![image](//upload-images.jianshu.io/upload_images/15405328-49428de20aa645c5?imageMogr2/auto-orient/strip%7CimageView2/2/w/640/format/webp)

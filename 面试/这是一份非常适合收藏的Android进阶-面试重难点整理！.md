![](https://upload-images.jianshu.io/upload_images/15233854-bfae57332c164e05.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 写在前面

记得我大二时“不务正业”地自学Android并跟了老师做项目，到大三开始在目前的公司实习，至今毕业已有几年多，学习Android已经6、7年多了！但总感觉知识点很零散，并且不够深入，遇到瓶颈，所以有了这文章。

本文是我对自身的**Android编程技能的总结、思考和反省，意在系统地、深入地学习Android的重难点，使自己的能力上一个台阶**。下面我将会罗列Android知识体系中**比较高频、重要或难懂的知识点**，并未来会基于此文章列出的知识点一个一个学习研究，并写成一系列的文章，当做自己的学习总结笔记，也希望童鞋们和我一起学习和交流~

> 说明：本文不是面试经验谈，而是自我总结。我也不打算像网上很多文章那样只列出点，说出结果，而没说原因，然后死记硬背，知其所以然而不知其然。而我是打算对下面的知识点深入学习，并对每个知识点从源码或举例来写成文章，从而即知其然又知其所以然。我也不知能否做到，但我会坚持努力，当作立个flag吧~

![](//upload-images.jianshu.io/upload_images/2392382-76919c2ba8d9b5df.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/533/format/webp)

### 正文 Android进阶重难点

##### Java

*   集合：使用场景、源码阅读
*   GC机制
*   虚拟机
*   对象内存分配

要学好Android，必须要有扎实的Java基础（当然，现在还有Kotlin）。这里只列出了四点，但Java的体系非常庞大，重难点当然不只这些，列出来的是在实际项目和面试中常用或常见的。

##### Android常用/重要类

*   Activity
*   Handler
*   HandlerThread
*   AsyncTask
*   Binder

Android这几个类在编程时是比较高频使用的，所以我打算细读下这几个类的源码。而这几个类也是面试经常被问到的，如Activity启动流程、Binder通信原理等等，所以非常有阅读价值！

##### Android基础技术和高频面试知识点

*   View事件传递
*   View绘制流程
*   SurfaceView的使用
*   IntentService替换Service的场景
*   Service保活
*   Android序列化方式和区别

这些知识点算是比较基础的，但也很重要！可惜在实际项目中很少用到，所以经常会忘了这些知识。所以打算把其写成文章，加深自己印象。

##### NDK和JNI

说起这个我就头疼，相信很多人跟我一样，如果只是调用C层接口还好，如果要写C的代码，就很尴尬了。但我们不能总是逃避困难，而是迎难而上！所以我下定决心来跨过心里的这道坎了！

##### 算法与数据结构

*   队列、栈、链表、有向图
*   排序
*   查找
*   红黑树

在家的几本算法书都满是灰了... 罗列的点不难，算是需要基本掌握的吧，但我总是看了忘了，忘了又看，还是记不得... 所以也打算写文章增强记忆。另外，如果做应用级别的开发，掌握这几个知识点基本是够用的了，毕竟Java的集合也提供了排序接口。但还是需要掌握下，毕竟不同算法使用到不同场景。

##### 插件化/热修复

作为Android进阶必掌握的技能，这个不会都不好意思说自己的Android开发的。之前看了一个月RePlugin的源码，感觉道行还不够。很多代码的处理或适配看得似懂非懂，如Binder的机制等。所以这个需要大量的知识作为支撑才能完成看懂，未来再战！

##### 第三方优秀开源库

*   RxJava
*   Glide
*   OKHttp

“RTFSC (Read The F**king Source Code )”。每个人都有自己的编程风格，如果不借鉴、不参考下别人的代码，眼界永远这么小，难以进一步提升编程境界。所以阅读他人优秀的代码是非常有必要的！

##### Android常见的架构模式

*   MVC
*   MVP
*   MVVM

解耦！解耦！Android的架构模式的使用重中之重无非就是解耦了。开发代码不难，维护代码才是最难的。如果代码都很多的解耦，把维护成本降低，工作效率自然提高。

##### Gradle和Groovy

相信现在大多数人都用Android Studio开发了，Gradle作为构建工具真是非常强大，个人觉得它完成可以取代Java的编译期注解，有过之而无不及。因为连文件也能在编译期作修改，非常强大，所以也非常值得学习。

##### 通用技能

*   TCP/HTTP
*   SQL
*   自动化测试
*   抓包

除了Android的知识点意外，还需要掌握一些通用的技能。毕竟懂多点才有底气跟后端怼嘛哈哈。

* * *

### 写在最后

由于本文罗列的知识点是根据我自身总结出来的，并且由于本人水平有限，无法全部提及，欢迎大神们能补充~

将来我会对上面的知识点一个一个深入学习，也希望有童鞋跟我一起学习，一起进阶。

**提升架构认知不是一蹴而就的，它离不开刻意学习和思考。**

**这里，笔者分享一份从架构哲学的层面来剖析的视频及资料分享给大家，**梳理了多年的架构经验，筹备近1个月最新录制的，相信这份视频能给你带来不一样的启发、收获。

![](https://upload-images.jianshu.io/upload_images/15233854-e2a6b7a614126e73.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/874/format/webp)

![](https://upload-images.jianshu.io/upload_images/15233854-ee09e8246f8fdb38.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

**领取方式：获取免费架构视频资料请加群：887084983**

最近还在整理并复习一些Android基础知识点，有问题希望大家够指出，谢谢。

希望读到这的您能转发分享和关注一下我，以后还会更新技术干货，谢谢您的支持！

**转发+点赞+关注**，第一时间获取最新知识点

Android架构师之路很漫长，一起共勉吧！

* * *

#### 以下墙裂推荐阅读！！！

*   [Android学习笔记参考（敲黑板！！）](https://links.jianshu.com/go?to=https%3A%2F%2Fshimo.im%2Fdocs%2FVxOufNNdaD4WS61R)
*   [“寒冬未过”，阿里P9架构分享Android必备技术点，让你offer拿到手软！](https://www.jianshu.com/p/534cb0eb8d4e)
*   [毕业3年，我是如何从年薪10W的拖拽工程师成为30W资深Android开发者！](https://www.jianshu.com/p/0eda7b13e9fe)
*   [腾讯T3大牛带你了解 2019 Android开发趋势及必备技术点！](https://www.jianshu.com/p/da8dc5b9d3f3)
*   [八年Android开发，从码农到架构师分享我的技术成长之路，共勉！](https://www.jianshu.com/p/ea587bd3cfe5)

**最后祝大家生活愉快~**

![](https://upload-images.jianshu.io/upload_images/15233854-d134dc29b50a6869.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/339/format/webp)

本文将不定期更新，欢迎收藏~

回顾一下自己这段时间的经历，九月份的时候，公司通知了裁员，我匆匆忙忙地出去面了几家，但最终都没有拿到offer，我感觉今年的寒冬有点冷。到十二月份，公司开始第二波裁员，我决定主动拿赔偿走人。后续的面试过程我做了一些准备，基本都能走到hr面，后面我也顺利地拿到了offer，我给大家分享下我的求职体会，希望能给大家一些参考。

一般Android面试分为两部分：Java部分和Android部分，下面说一下自己面试过程遇到的一些具体题目和一些相关知识点。

##一 JAVA相关

#####1）JAVA基础

1.java基本数据类型有哪些，int， long占几个字节
2.== 和 equals有什么区别
3.hashcode 和 equals作用
4.new String创建了几个对象
5.位运算符的一些计算
6.java的拆装箱
7.compareable 和 compartor的区别
下面列一两个遇到的题吧

![image.png](https://upload-images.jianshu.io/upload_images/15233854-4d771ba91bda3689.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/15233854-8aee1a7decddbbef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/15233854-1191efe278a41100.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

它的打印结果是什么。
类似这种的题很多，考察的都是基础知识的牢固性。所以需要自己多巩固基础。

#####2）数据结构和算法

常见的数据结构就是：数组，栈，队列，集合，映射，链表，堆，二分搜索树，红黑树。当然还有其他的一些，比如AVL平衡树等一些数据结构。

我们要做的就是了解它们的实现原理和各自的优缺点。

数据结构部分面试遇到最多的就是：

1.ArrayList和LinkedList的区别，优缺点
2.hashmap实现，扩容是怎么做的，怎么处理hash冲突，hashcode算法等
3.链表需要知道。LinkedHashMap一般再问LRU的时候会问到
4.二分搜索树的特性和原理。前中后序遍历写出其中一种，当问到二分搜索树的缺点的时候，你需要提出基于二分搜索树的红黑树，说出他的特性。
5.堆的实现，最大堆，最小堆，优先队列原理。
#####算法
算法其实就是我们平时常见的一些排序：选择排序，插入排序，冒泡排序，希尔排序，归并排序，快速排序。以及和数据结构相关联的解决部分问题的一些计算方法。

算法面试遇到的一些题：

1.手写快速排序，插入排序，冒泡排序
2.翻转一个数字
3.翻转一个链表
4.O(n)复杂度找出数组中和是9的两个数的索引
5.写出二分搜索树前中后序遍历中的其中一个
6.实现一个队列，并能记录队列中最大的数。
算法这一块是需要练习的推荐去Leetcode上面刷刷题，开拓一下思维。算法也并不一定要求你能写出来，主要考察你的思路，已经如何优化你的算法。

#####3）JVM虚拟机

JVM虚拟机我们需要知道他们内部组成：堆，虚拟机栈，本地方法栈，方法区，计数器。每一块都存放什么东西，以及垃圾回收的时候主要回收哪些块的东西。GC-ROOT链是从哪些地方开始的，垃圾回收集算法(很少遇到问的)。

类加载ClassLoader已经双亲委派机制，类加载的过程，类加载的信息对应在JVM的哪些块中。

列一个遇到的类加载的面试题：

public class TestClassLoader {

    static class Father {

        public static final String TAG = "Father";

        static {
            System.out.println("static Father");
        }

        {
            System.out.println("unStatic Father");
        }

        public Father() {
            System.out.println("constract Father");
            method();
        }

        public void method() {
            System.out.println("method Father");
        }

        @Override
        public String toString() {
            return "toString Father";
        }
    }

    static class Son extends Father {
        public static Son instance = new Son();

        static {
            System.out.println("static Son");
        }

        {
            System.out.println("unStatic Son");
        }

        public Son() {
            System.out.println("constract Son");
            method();
        }

        public void method() {
            System.out.println("method Son");
        }

        @Override
        public String toString() {
            return "toString Son";
        }
    }

    public static void main(String[] args) {
        System.out.println("1.---------------------");
        System.out.println(Son.TAG);
        Son[] sons = new Son[10];
        System.out.println(sons);
        System.out.println("2.---------------------");
        System.out.println(Son.instance);
        System.out.println("3.---------------------");
        Son son = new Son();
        Father father = son;
        father.method();
        System.out.println(son);
    }
}
写出打印输出。

加一个小插曲：上面的题是饿了吗面试问到的。面试我的人自己说是17年北航毕业的研究生。说自己非常注重基础，全程问了很多JAVA基础，包括上面的类加载的题，很自豪的说这个题是他自己出的。还有Collections下面的子类都有哪些，都有什么不同。但是Android相关的题目问的水平很一般，说这个没有嘲讽的意思。想说的是有些面试从一开始就注定了你可能进不了这个公司。面试有的时候也是要看眼缘的。不要灰心，找出自己的不足，补齐它，继续前行。还有就是上面那个题，的确很不错。

#####4）线程安全

当多个线程访问一个对象的时候，如果不用考虑这些线程在运行时环境下的调度和交替执行，也不需要进行额外的同步，或者在调用方进行任何其他的协调操作，调用这个对象的行为都可以获取正确的结果，我们就认为这个对象时线程安全的。

线程安全就是一些多线程下载，同步，锁，死锁，线程池。volatile关键字的特性，变量的原子性。以及java.util.concurrent包下的类，也需要了解一下。

一般会问的是手写单例，以及双重锁式单例的优点。还有就是让你自己实现一个多线程下载，看你怎么设计。

#####5）编程思想

封装，继承，多态，抽象，反射，注解，设计模式，设计模式的原则。

面试中一般会问下：

1.抽象和接口有什么不一样
2.工作中常用的设计模式，一些源码中的设计模式
3.具体给你一个设计模式让你说说你对他的了解，比如观察者，工厂。
以上这些东西主要考察你的代码设计能力。

#####6）网络协议

1.互联网的实现主要分为几层，http、ftp、tcp、ip分别位于哪一层。
2.http和https的区别
3.为什么tcp要经过三次握手，四次挥手
4.socket了解过吗
一般http和https问的比较多，对称加密和非对称加密也会问。tcp和socket偶尔遇见问的。

###二 JAVA部分总结

我觉得JAVA部分大致上可以分为这几大块，细想一套代码，其实就是类和组合这些类，怎么组合其实就是设计模式。而类里面包含的其实就是基本数据类型和一些数据机构来存放这些基本数据类型或者类，然后就是JVM去怎么加载这些类。针对上面几个部分我推荐几本书吧：
《JAVA编程思想》
《深入理解JAVA虚拟机 第二版》
《大话设计模式》
《HeadFirst设计模式》
《数据结构和算法》
《图解HTTP》

数据结构和算法重点推荐：https://github.com/wangxp423/ExerciseJava 以及readme中提到的刘宇波(liuyubobobo)他开源的代码和文档。他在慕课网录制了四套视频来讲解数据结构和算法。文档通俗易懂。很适合初学者，和想系统了解的人。
######在这由于文字很多，我总结了Android面试所涉及到的常问范围及常问面试题免费分享给大家，文末有领取！

###三 Android相关

Android部分我就不分几大块了。直接列举，但是列举到的每一项都是面试经常会问到并且会延伸问的，所以需要深入的去研究。

1.四大组件有哪些，说出你对他们在Android系统中的作用和理解。
2.Activity生命周期，A启动B两个页面生命周期怎么运行的，为什么会 这样，生命周期为什么这么设计，你有了解过吗。
3.四种启动模式，内部堆栈是怎么回事，你工作中怎么使用的。
4.Activity的启动过程，这个我强烈建议每个Android开发人员都要清楚的知道，并且跟一下源码，几个核心类的作用。你会对Android有一个更好的认识。
5.事件分发流程，怎么处理滑动冲突。举例：长按ListView的一个Item它变灰了。这个时候在滑动。item恢复原来的样子，这个时候他们内部的事件传递是什么样子。有很多种问法，所以你一定要搞清楚。
6.自定义View,View的绘制流程。onMeasure,onLayout,onDraw都是什么作用。ViewGroup是怎么分发绘制的。onDraw里面怎么去做绘制，Canvas,Path,Paint你都需要了解。并且配合ValueAnimtor或者Scroller去实现动画。有时候面试的会突发奇想问你ViewGroup是树形结构，我想知道树的深度，你怎么计算，突然就变成了一个数据结构和算法的题。
7.Bitmap和Drawable
8.Animation和Animator
9.LinearLayout、RelativeLayout、FrameLayout三种常用布局的特性，他在布局的时候是怎么计算的。效率如何。CoordinatorLayout配合AppbarLayout的使用，以及自定义Behavior。ConstraintLayout的使用。用来减少层级。
10.Handler消息机制，推荐看一下Looper的源码
11.进程间通信，Binder机制
12.AsyncTask源码看一下。
13.图片的压缩处理，三级缓存，Lru算法
14.分辨率和屏幕密度，以及计算一个图片大小。mdpi,hdpi的关系和比例。
15.优化，内存优化，布局优化，启动优化，性能优化。内存泄露，内存溢出。怎么优化，用了什么工具，具体怎么做的。
16.listView和RecycleView对比，以及缓存策略。
17.JNI（很少问）
18.MVC,MVP,MVVM
19.开源框架Okhttp,Glide,EventBus,Rxjava等,以及JetPack下的开源库，要会用，还说说出一些东西，推荐 Retrofit，Okhttp,Glide,EventBus这些看一下源码。
20.RecyclerView四大块，能实现什么效果，大致怎么实现的，心里要有数
21.DecorView,Window,WindowManager,PhoneWindow关系，以及个子的职责。
加分项：Kotlin，Gradle，Flutter，组件化，插件化，热修复。

###四 Android相关总结

上面的列的相关内容，看似很短一句话，但是每一项都需要你深入的去研究，要了解原理，最好去看一下源码实现。当然还有一些是我没写到的，可能是我写的时候没有想起来，并不代表他们不重要，你也需要重视，再来一波推荐：
《Android开发艺术探索》强烈推荐，建议细细的读，如果粗心大意建议读两到三遍，针对性的读。
《Android进阶之光》
《高级Android开发强化实战》
《Android组件化架构》
《Android热修复技术原理》
《Android插件化开发指南》

###五 面试心得

1.准备要充分，知识面要尽量的广，同时深度也要够。
2.面试安排上，如果不着急，尽量给自己留多时间，两天一家，及时做总结和补充。
3.心态要放平，当做一次技术交流，面试要看一部分的运气，也要看一些眼缘，有的面试官一张嘴你就能感觉到你这次面试完了。想去的公司没有面试好，不要气馁，继续加油准备。
4.简历投递方面，拉勾上投了很多经常不匹配，可能是我学历问题(自考本)，有一些打击自信心，如果有同样感受的，不妨换BOSS或者其他平台。避免打击自信心。
5.写简历一定要体现自己的优势，最好能体现类似于，用到了什么技术，解决了什么问题。简历上写到的一定要胸有成竹。
6.类似于你的优势是什么，你觉得你项目中做的比较好的地方有哪些，你能给公司带来什么，这种问题心里要先想一些，免得临场发挥容易紧张说不好。
7.我所经历的面试，一般技术面最少是两轮。如果一轮后让你走了，而你还自我感觉良好，那我觉得你有必要回顾一下面试内容，并一条一条找找该问题的相关内容，大部分问题就在于你没有答到点子上，可能是深度不够，也可能是扩展不够。继续加油。
###六 闲聊

现在外面面试问的的确比较多，要求也挺高的，行情不好，薪资也不好要，所以心态一定要放好，找好自己的定位，心态一定要好。

面试之后要尽量做到总结，从我最开始没什么准备就出去面试，被打击一通之后，总结了七八家面试题，会发新面试问到的都是那些知识点，可能角度不一样，但是你只要深度够，他怎么问，你基于原理来回答，基本上都没差，从签了赔偿协议以后出来面试，连续四家公司都走到了HR面，可以说面试的感觉上来了。前面的总结积累终究没有白费。

在写这篇文章之前，我专门看了下我之前收藏的别的公号上发的面试题，很全，我都有点不想写这篇文章了。但是想了下我这三个月听到要裁员开始面试，到第一批没被裁，第二批裁员迟迟不来。到第二批来的时候主动申请被裁。中间面试被打击，失落，看到新闻那那都在裁员，到现在面试整体感觉还不错。还是想分享一下自己的心里路程，想告诉大家，裁员别慌，做自己该做的，其他自己决定不了的，先放一放，心里列一个123，一条一条来。

可以看到我在上面推荐了很多书籍，而且JAVA部分时分块来了。是因为当我看到很多面试题，针对题来的时候，题会很多，而且有时候你会觉得你的题都会了，但是面试的时候还是面不好，说明你的知识并不扎实。或者你知识会了这个题，并没有融会贯通，所以在JAVA部分我分大块来描述，而且针对每一块，我都有推荐一本相关的书，你可能不需要都看，可以针对性的看。而Android部分时详细的知识点，这些知识点我希望的是，能够深入研究。而推荐的书籍，书中的部分都有一些相关的知识点，大家也可以选择性的看，当然论坛，博客都是知识的来源，大家做好知识点深度和广度的基类就行了。

最后，虽然裁员的很多，都在说Android要没落，但是，不要慌，做自己的计划，学自己的习，竞争无处不在，每个行业都是如此。祝大家2019年万事大吉。

**最后**
**针对于上面的面试题，我总结出了互联网公司Android程序员面试涉及到的绝大部分面试题及答案，并整理做成了文档，以及系统的进阶学习视频资料，免费分享给大家。
（包括Java在Android开发中应用、APP框架知识体系、高级UI、全方位性能调优，NDK开发，音视频技术，人工智能技术，跨平台技术等技术资料），希望能帮助到你面试前的复习，且找到一个好的工作，也节省大家在网上搜索资料的时间来学习。**

### **领取方式：关注+点赞+加群：185873940 免费获取！**

![image](http://upload-images.jianshu.io/upload_images/15405328-49428de20aa645c5?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

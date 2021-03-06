在平常的面试中，或多或少总会有面试官提到进程和线程这个概念，那么什么是进程，什么是线程呢？最简单的比喻多线程就像火车的每一节车厢，而进程则是火车。车厢离开火车是无法跑动的，同理火车也不可能只有一节车厢。多线程的出现就是为了提高效率。

###一、说说区别

#####1、进程与线程的区别：

进程和线程的主要差别在于它们是不同的操作系统资源管理方式。进程有独立的地址空间，一个进程崩溃后，在保护模式下不会对其它进程产生影响，而线程只是一个进程中的不同执行路径。线程有自己的堆栈和局部变量，但线程之间没有单独的地址空间，**一个线程死掉就等于整个进程死掉，所以多进程的程序要比多线程的程序健壮**，但在进程切换时，耗费资源较大，效率要差一些。但对于一些要求同时进行并且又要共享某些变量的并发操作，只能用线程，不能用进程。

1) 简而言之,一个程序至少有一个进程,一个进程至少有一个线程.

2) 线程的划分尺度小于进程，使得多线程程序的并发性高。

3) 另外，进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的运行效率。

4) 线程在执行过程中与进程还是有区别的。每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。但是线程不能够独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制。

5) 从逻辑角度来看，多线程的意义在于一个应用程序中，有多个执行部分可以同时执行。但操作系统并没有将多个线程看做多个独立的应用，来实现进程的调度和管理以及资源分配。这就是进程和线程的重要区别。

#####2、什么是程序

程序说白了就是一段可以执行的代码，比如说，一个for循环，就是一个程序，它是一段可以执行的代码逻辑，程序是指令和数据的有序集合，其本身没有任何运行的含义，是一个静态的概念。而进程则是在处理机上的一次执行过程，它是一个动态的概念。

#####3、什么是线程

线程，之前也说过，它是资源的调度单位，是cpu的一个最小执行单位，记住，线程是依赖于进程而存在的，也就说，没有进程，线程也就不复存在，通常在一个进程中可以包含若干个线程，当然一个进程中至少有一个线程，不然没有存在的意义。由于线程比进程更小，基本上不拥有系统资源，故对它的调度所付出的开销就会小得多，能更高效的提高系统多个程序间并发执行的程度。

#####4、什么是多线程

多线程，就很简单了，比如一个程序，你可以听音乐，也可以看小说，也可以看小说，这就是多个任务，在一个程序中，这些独立运行的程序片段叫作“线程”（Thread），利用它编程的概念就叫作“多线程处理”。多线程是为了同步完成多项任务，不是为了提高运行效率，而是为了提高资源使用效率来提高系统的效率。线程是在同一时间需要完成多项任务的时候实现的。

最简单的比喻多线程就像火车的每一节车厢，而进程则是火车。车厢离开火车是无法跑动的，同理火车也不可能只有一节车厢。多线程的出现就是为了提高效率。

###二、了解概念

#####1、什么是进程

**我们打开电脑端的任务管理器，会发现执行着很多进程，一个qq就是一个进程，一个微信也是一个进程。**

**简单的来讲进程的概念主要有两点**：**第一，进程是一个实体。**每一个进程都有它自己的地址空间，一般情况下，包括文本区域（text region）、数据区域（data region）和堆栈（stack region）。文本区域存储处理器执行的代码；数据区域存储变量和进程执行期间使用的动态分配的内存；堆栈区域存储着活动过程调用的指令和本地变量。**第二，进程是一个“执行中的程序”。**程序是一个没有生命的实体，只有处理器赋予程序生命时，它才能成为一个活动的实体，我们称其为进程。

**进程状态**：进程有三个状态，就绪、运行和阻塞。就绪状态其实就是获取了出cpu外的所有资源，只要处理器分配资源就可以马上执行。就绪状态有排队序列什么的，排队原则不再赘述。运行态就是获得了处理器分配的资源，程序开始执行。阻塞态，当程序条件不够时候，需要等待条件满足时候才能执行，如等待i/o操作时候，此刻的状态就叫阻塞态。

**狭义定义**：进程就是一段程序的执行过程。

**广义定义**：进程是一个具有一定独立功能的程序关于某个数据集合的一次运行活动。它是操作系统动态执行的基本单元，在传统的操作系统中，进程既是基本的分配单元，也是基本的执行单元。

###三、说说优缺点

线程和进程在使用上各有优缺点：线程执行开销小，但不利于资源的管理和保护；而进程正相反。同时，线程适合于在SMP(多核处理机)机器上运行，而进程则可以跨机器迁移。

### 最后

**小编整理了一些关于这方面的深入讲解的视频，如需要的话可以[加群](https://links.jianshu.com/go?to=https%3A%2F%2Fjq.qq.com%2F%3F_wv%3D1027%26k%3D5gyv0JM)免费领取。**

**本人Java开发4年Android开发5年，定期分享Android高级技术及经验分享，欢迎大家关注~**（分享内容包括不限于高级UI、性能优化、架构师课程、NDK、混合式开发（ReactNative+Weex）微信小程序、Flutter等全方面的Android进阶实践技术；希望能帮助到大家，也节省大家在网上搜索资料的时间来学习，也可以分享动态给身边好友一起学习！）

**部分资料分享：**

**免费领取方式： 点赞+加群免费获取 [Android架构设计](https://jq.qq.com/?_wv=1027&k=58EArGY)（185873940）**


![](https://upload-images.jianshu.io/upload_images/15233854-3d81899c70662c06.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/15233854-a30a6e851e69c62e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

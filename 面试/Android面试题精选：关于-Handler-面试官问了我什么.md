![](https://upload-images.jianshu.io/upload_images/15233854-f1cbfa6349e05d47.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>不定期分享Android面试题及答案解析

### 1.一个线程有几个 Handler？

答：一个线程可以用有多 Handler，因为 Handler 最终是被 Message 持用的（post 里面的 Runnable 最终也会被包装成一个 Message），以便 Looper 在拿到 Message 后调用 Handler 的 dispatchMessage 完成回调，而且项目中仔细去看也确实如此，我们可以每个 Activity 中都创建一个 Handler 来处理回调到主线程的任务。

### 2.一个线程有几个 Looper？如何保证？

答：一个线程只能拥有一个 Looper，这里从源码中就可以看到，sThreadLocal.set 只调用了一次，如果再次调用 prepare 会判断 sThreadLocal.get 是否为空，如果不为空就直接抛出异常了，也就是同一线程多次调用 prepare 方法会直接崩溃，这里也是避免了程序去修改某个线程已经设置好的 Looper 值。

![](https://upload-images.jianshu.io/upload_images/15233854-af8046859ac62da9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

补充：ThreadLocal 提供线程局部变量，也就是对应值只有该线程支持，并不会多线程共享。那它是如何做到线程局部变量这个效果的呢？它内部靠的是 ThreadLocalMap，线程作为 key，值作为 value，这样我去取对应值的时候，其实通过线程 Key 拿去对应的 value，这样就保证了值是当前线程独享的。

### 3.为何主线程可以使用 Handler？如果想要在子线程中使用 Handler 机制要做些什么准备？

答：先来看下面的源码，Handler 的构造中（无论调用哪个最终都会走到这里），是需要判断当前线程是否存在 Looper 的，如果不存在会直接抛出异常，主线程之所以可以使用 Handler 是因为系统帮在 ActivityThread 中已经帮我们创建了 Looper 并且已经让它运行了起来。

![](https://upload-images.jianshu.io/upload_images/15233854-8d3738b8d53d8c7d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

系统帮我们在主线程创建 Looper 的代码：

![](https://upload-images.jianshu.io/upload_images/15233854-e84cb4c1d396aa49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果我们现在子线程中使用 Handler 的话，之需要模仿系统怎么创建 Looper 即可，其实就是两步，在子线程中调用 Looper.prepare() 和 Looper.loop() 即可，prepare 帮我们在对应线程创建 Looper，loop 让刚刚创建好的 Looper 运行起来。

这两步完成后我们就可以在子线程中使用 Handler 了。

以上所说的 Handler 使用指的是 Handler 的创建，比如在 A 线程创建后就可以在任何位置使用了，也就是在任意线程发送消息，然后在 A 线程处理消息。

### 4.既然可以存在多个 Handler 往 MessageQueue 中添加数据（发消息时各个 Handler 可能处于不同线程），那它内部是如何确保线程安全的？

答：这里主要关注 MessageQueue 的消息存取即可，看源码内部的话，在往消息队列里面存储消息时，会拿当前的 MessageQueue 对象作为锁对象，这样通过加锁就可以确保操作的原子性和可见性了。

消息的读取也是同理，也会拿当前的 MessageQueue 对象作为锁对象，来保证多线程读写的一个安全性。

![](https://upload-images.jianshu.io/upload_images/15233854-9d09965658a87a5a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 5.我们使用 Message 时应该如何创建它？

答：创建的它的方式有两种，一种是直接 new 一个 Message 对象，另一种是通过调用 Message.obtain() 的方式去复用一个已经被回收的 Message，当然日常使用者是推荐使用后者来拿到一个 Message，因为不断的去创建新对象的话，可能会导致垃圾回收区域中新生代被占满，从而触发 GC。

Message 中的 sPool 就是用来存放被回收的 Message，当我们调用 obtain 后，会先查看是否有可复用的对象，如果真的没有才会去创建一个新的 Message 对象。

补充：主要的 Message 回收时机是：

*   在 MQ 中 remove Message 后；
*   单次 loop 结束后；
*   我们主动调用 Message 的 recycle 方法后；

### 6.Message 的数据结构是什么样子？

答：单链表，Message 中会通过 next 来持有下一个 Message 对象的引用，这是一个典型的链表结构。

![](https://upload-images.jianshu.io/upload_images/15233854-489cec7173faf655.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其实文中写到的这些问题并不只是问到这里就结束了，很多问题都可以深挖，尤其是这个问题，看着很短，接下去问很多关于链表的其他问题了，比如链表反转，或是延伸到其他数据结构问题。

### 7.主线程 Looper 与子线程 Looper 有什么不同？

答：最主要的区别还在在于 Looper 的 loop 循环是否能够退出，主线程创建时传入的 quitAllowed 是 false。

![](https://upload-images.jianshu.io/upload_images/15233854-25549895e964c6ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Looper.loop 这个方法在拿到的消息为空时就会退出那个死循环，不过一般是不为空的，哪怕没有消息最多也是阻塞，只有调用 Looper.quit 时才会在消息队列清空消息并把消息设置为 null。

那 loop 的死循环结束意味着什么呢？看下面 ActivityThread 的代码就知道了，如果主线程 Looper 结束就说明程序也要退出了，因为只有 loop 不断执行才不会走到抛出异常那一行。

```
    文件：android/app/ActivityThread.java

    public static void main(String[] args) {
        //  ...
        //  主线程 Looper 创建
        Looper.prepareMainLooper();
        //  ...
        ActivityThread thread = new ActivityThread();
        thread.attach(false, startSeq);

        if (sMainThreadHandler == null) {
            sMainThreadHandler = thread.getHandler();
        }
        //  ...
        Looper.loop();

        throw new RuntimeException("Main thread loop unexpectedly exited");
    }
```

### 最后的最后

以上是我问题是我最近去面试过程中遇到的一些关于 Handler 问题，整理出来强化记忆。希望对你也有帮助。

> 面试：如果不准备充分的面试，完全是浪费时间，更是对自己的不负责！
> 
> 金九银十面试季，赶快去为自己的面试做足准备吧！

最后不管怎么样，不论是什么样的大小面试，要想不被面试官虐的不要不要的，只有刷爆面试题题做好全面的准备，当然除了这个还需要在平时把自己的基础打扎实，这样不论面试官怎么样一个知识点里往死里凿，你也能应付如流啊~

**快来获取学习资料提升自己去挑战一下BAT面试难关吧！**

[Android 学习，面试文档，视频收集大整理](https://links.jianshu.com/go?to=https%3A%2F%2Fshimo.im%2Fdocs%2FVxOufNNdaD4WS61R)

![](https://upload-images.jianshu.io/upload_images/15233854-4dd1aca7e7a4cd1a.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)


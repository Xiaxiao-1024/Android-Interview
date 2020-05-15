#### 导读

正常来说去大厂面试应该问一些底层原理，但这次面试却违反常规问了一些基础，这也提醒我们不要把基础丢了。文末有大量的面试题文章，大家记得看，本文中的经历是作者AffyFei，不是我，下面看正文：

* * *

今天早上参加了深圳OPPO开发工程师的技术面试，总的来说面试过程不是很顺利。面试官并没有问一些很深奥的底层原理，基本都是一些Java基础以及Android四大组件内的基础，但是我自身在开发过程中并没有很重视这些理论基础，导致很多知识点都忘记了。整个面试过程耗时一小时，感谢两位面试官不厌其烦地给我提示，一方面让我能够回想起来那些遗忘的知识点，另一方面也缓解了尴尬的气氛。。。 　　
　　顺便一说，OPPO的保密工作还是做得比较严格的，进去后海卓越中心大楼前需要申请临时通行证才能进去。而在面试前还需要登记，并且把手机的前后摄像头都给用胶带封起来才能进行面试。废话少说，下面分成两部分汇总一下这次技术面试的知识点。

**在这由于文字很多，我总结了Android面试所涉及到的常问范围做出了一份学习进阶路线图​​​​​​​及面试题答案免费分享给大家，文末有领取！**

#### Java方面

**1.如何理解Java的多态？其中，重载和重写有什么区别？**
多态是同一个行为具有多个不同表现形式或形态的能力，多态是同一个接口，使用不同的实例而执行不同操作，多态就是程序运行期间才确定，一个引用变量倒底会指向哪个类的实例对象，该引用变量发出的方法调用到底是哪个类中实现的方法。
多态存在的三个必要条件是：继承，重写，父类引用指向子类引用。
多态的三个实现方式是：重写，接口，抽象类和抽象方法。

**重写(Override)和重载(Overload)的区别**

![image](http://upload-images.jianshu.io/upload_images/15233854-06f002786cb8a8e0?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**2.谈一下JVM内存区域划分？哪部分是线程公有的，哪部分是私有的？**
JVM 的内存区域可以分为两类：线程私有和区域和线程共有的区域。 线程私有的区域：程序计数器、JVM 虚拟机栈、本地方法栈；线程共有的区域：堆、方法区、运行时常量池。

*   **程序计数器，**也有称作PC寄存器。每个线程都有一个私有的程序计数器，任何时间一个线程都只会有一个方法正在执行，也就是所谓的当前方法。程序计数器存放的就是这个当前方法的JVM指令地址。当CPU需要执行指令时，需要从程序计数器中得到当前需要执行的指令所在存储单元的地址，然后根据得到的地址获取到指令，在得到指令之后，程序计数器便自动加1或者根据转移指针得到下一条指令的地址，如此循环，直至执行完所有的指令。
    JVM虚拟机栈。创建线程的时候会创建线程内的虚拟机栈，栈中存放着一个个的栈帧，对应着一个个方法的调用。JVM 虚拟机栈有两种操作，分别是压栈和出站。栈帧中存放着局部变量表(Local Variables)、操作数栈(Operand Stack)、指向当前方法所属的类的运行时常量池的引用(Reference to runtime constant pool)、方法返回地址(Return Address)和一些额外的附加信息。

*   **本地方法栈。**本地方法栈与Java栈的作用和原理非常相似。区别只不过是Java栈是为执行Java方法服务的，而本地方法栈则是为执行本地方法（Native Method）服务的。在JVM规范中，并没有对本地方发展的具体实现方法以及数据结构作强制规定，虚拟机可以自由实现它。在HotSopt虚拟机中直接就把本地方法栈和Java栈合二为一。
    堆。堆是内存管理的核心区域，用来存放对象实例。几乎所有创建的对象实例都会直接分配到堆上。所以堆也是垃圾回收的主要区域，垃圾收集器会对堆有着更细的划分，最常见的就是把堆划分为新生代和老年代。java堆允许处于不连续的物理内存空间中，只要逻辑连续即可。堆中如果没有空间完成实例分配无法扩展时将会抛出OutOfMemoryError异常。

*   **方法区。**方法区与堆一样所有线程所共享的内存区域，它用于存储已被虚拟机加载的类信息、常量、静态变量、及时编译器编译后的代码等数据。在Class文件中除了类的字段、方法、接口等描述信息外，还有一项信息是常量池，用来存储编译期间生成的字面量和符号引用。

其实除了程序计数器，其他的部分都会发生 OOM。

*   **堆。** 通常发生的 OOM 都会发生在堆中，最常见的可能导致 OOM 的原因就是内存泄漏。

*   **JVM虚拟机栈和本地方法栈。** 当我们写一个递归方法，这个递归方法没有循环终止条件，最终会导致  StackOverflow 的错误。当然，如果栈空间扩展失败，也是会发生 OOM 的。

*   **方法区。**方法区现在基本上不太会发生 OOM，但在早期内存中加载的类信息过多的情况下也是会发生 OOM 的。

**3.final关键字的用法？**
final 可以修饰类、变量和方法。修饰类代表这个类不可被继承。修饰变量代表此变量不可被改变。修饰方法表示此方法不可被重写 (override)。
**4.死锁是怎么导致的？如何定位死锁**
某个任务在等待另一个任务，而后者又等待别的任务，这样一直下去，直到这个链条上的任务又在等待第一个任务释放锁。这得到了一个任务之间互相等待的连续循环，没有哪个线程能继续。这被称之为死锁。当以下四个条件同时满足时，就会产生死锁：
(1) 互斥条件。任务所使用的资源中至少有一个是不能共享的。
(2) 任务必须持有一个资源，同时等待获取另一个被别的任务占有的资源。
(3) 资源不能被强占。
(4) 必须有循环等待。一个任务正在等待另一个任务所持有的资源，后者又在等待别的任务所持有的资源，这样一直下去，直到有一个任务在等待第一个任务所持有的资源，使得大家都被锁住。
要解决死锁问题，必须打破上面四个条件的其中之一。在程序中，最容易打破的往往是第四个条件。
关于如何手写死锁和定位方法，可参考这篇博客。
**5.数据库如何进行升级？SQLite增删改查的基础sql语句?**

```
/**     * Create a helper object to create, open, and/or manage a database.     * This method always returns very quickly.  The database is not actually     * created or opened until one of {@link #getWritableDatabase} or     * {@link #getReadableDatabase} is called.     *     * @param context to use to open or create the database     * @param name of the database file, or null for an in-memory database     * @param factory to use for creating cursor objects, or null for the default     * @param version number of the database (starting at 1); if the database is older,     *     {@link #onUpgrade} will be used to upgrade the database; if the database is     *     newer, {@link #onDowngrade} will be used to downgrade the database     */    public SQLiteOpenHelper(Context context, String name, CursorFactory factory, int version) {        this(context, name, factory, version, null);    }    public SQLiteDatabase getWritableDatabase() {        synchronized (this) {            return getDatabaseLocked(true);        }    }  private SQLiteDatabase getDatabaseLocked(boolean writable) {      .......      db.beginTransaction();      try {              if (version == 0) {                   onCreate(db);              } else {                   if (version > mNewVersion) {                         onDowngrade(db, version, mNewVersion);                   } else {                         onUpgrade(db, version, mNewVersion);                   }              }               db.setVersion(mNewVersion);                db.setTransactionSuccessful();              } finally {                 db.endTransaction();              }  }
```

在SQLiteOpenHelper的构造函数中，包含了一个version的参数。这个参数即是数据库的版本。 所以，我们可以通过修改version来实现数据库的升级。 当version大于原数据库版本时，onUpgrade()会被触发，可以在该方法中编写数据库升级逻辑。具体的数据库升级逻辑示例可参考这里。
常用的SQL增删改查：

增：INSERT INTO table_name (列1, 列2,…) VALUES (值1, 值2,….)
删： DELETE FROM 表名称 WHERE 列名称 = 值
改：UPDATE 表名称 SET 列名称 = 新值 WHERE 列名称 = 某值
查：SELECT 列名称（通配是*符号） FROM 表名称

ps:操作数据表是:ALTER TABLE。该语句用于在已有的表中添加、修改或删除列。
ALTER TABLE table_name ADD column_name datatype
ALTER TABLE table_name DROP COLUMN column_name
ALTER TABLE table_name_old RENAME TO table_name_new

### Android方面

**1.Broadcast的分类？有序，无序？粘性，非粘性？本地广播？**

*   广播可以分为有序广播、无序广播、本地广播、粘性广播。其中无序广播通过sendBroadcast(intent)发送，有序广播通过sendOrderedBroadcast(intent)发送。

*   有序广播。
    (1) 有序广播可以用priority来调整优先级   取值范围-1000~+1000，默认为0，数值越大优先级越高，优先级越高越优先获得广播响应。
    (2) abortBroadcast()可来终止该广播的传播，对更低优先级的屏蔽，注意只对有序广播生效。
    (3) 有序广播在传播数据中会发生比如setResultData()，getResultData()，在传播过程中，可以从新设置数据

*   关于本地广播，可以查看这篇文章。总的来说，本地广播是通过LocalBroadcastManager内置的Handler来实现的，只是利用了IntentFilter的match功能，至于BroadcastReceiver 换成其他接口也无所谓，顺便利用了现成的类和概念而已。在register()的时候保存BroadcastReceiver以及对应的IntentFilter，在sendBroadcast()的时候找到和Intent对应的BroadcastReceiver，然后通过Handler发送消息，触发executePendingBroadcasts()函数，再在后者中调用对应BroadcastReceiver的onReceive()方法。

*   粘性消息：粘性消息在发送后就一直存在于系统的消息容器里面，等待对应的处理器去处理，如果暂时没有处理器处理这个消息则一直在消息容器里面处于等待状态，粘性广播的Receiver如果被销毁，那么下次重建时会自动接收到消息数据。(在 android 5.0/api 21中deprecated,不再推荐使用，相应的还有粘性有序广播，同样已经deprecated)

**2.Android中的事件传递机制？**
当我们的手指触碰到屏幕，事件是按照Activity->ViewGroup->View这样的流程到达最终响应触摸事件的View的。而在事件分发过程中，涉及到三个最重要的方法：dispatchTouchEvent()、onInterceptTouchEvent()、onTouchEvent。我们的手指触摸到屏幕的时候，会触发一个Action_Down类型的事件，当前页面的Activity会首先做出相应，也就是说会走到Activity的dispatchTouchEvent()方法内。在这个方法内部有下面两个逻辑：

*   调用getWindow.superDispatchTouchEvent()。

*   如果上一步返回true，则直接返回true；否则return自己的onTouchEvent()。显然，当getWindow.superDispatchTouchEvent()返回true，表示当前事件已经被消费掉，无需调用onTouchEvent；否则代表事件并没有被处理，因此需要调用Activity的onTouchEvent进行处理。
    我们都知道，getWindow()返回的是PhoneWindow，因此这句代码本质上调用了PhoneWindow中的superDispatchTouchEvent()。而后者实际上调用了mDecor.superDispatchTouchEvent(event)。这个mDecor也就是DecorView，它是FrameLayout的一个子类。在DecorView中的superDispatchTouchEvent(event)中调用的是super.dispatchTouchEvent()。因此，本质上调用的是ViewGroup的dispatchTouchEvent()。

到这里，事件已经从Activity传递到ViewGroup了。接下来我们分析ViewGroup。
在ViewGroup的dispatchTouchEvent()中逻辑大致如下：

*   通过onInterceptTouchEvent()判断当前ViewGroup是否拦截，默认的ViewGroup都是不拦截的；

*   如果拦截，则return自己的onTouchEvent()；

*   如果不拦截，则根据child.dispatchTouchEvent()的返回值判断。如果返回true，则return true；否则return自身的onTouchEvent()，在这里实现了未处理事件的向上传递。

通常情况下，ViewGroup的onInterceptTouchEvent()都返回false，表示不拦截。这里需要注意的是事件序列，比如Down事件、Move事件…Up事件，从Down到Up是一个完整的事件序列，对应着手指从按下到抬起这一系列事件，如果ViewGroup拦截了Down事件，那么后续事件都会交给这个ViewGroup的onTouchEvent。如果ViewGroup拦截的不是Down事件，那么会给之前处理这个Down事件的View发送一个Action_Cancel类型的事件，通知子View这个后续的事件序列已经被ViewGroup接管了，子View恢复之前的状态即可。
这里举一个常见的例子：在一个 Recyclerview 中有很多的 Button，我们首先按下了一个 button，然后滑动一段距离再松开，这时候 Recyclerview 会跟着滑动，并不会触发这个 button 的点击事件。这个例子中，当我们按下 button 时，这个 button 接收到了 Action_Down 事件，正常情况下后续的事件序列应该由这个 button处理。但我们滑动了一段距离，这时  Recyclerview 察觉到这是一个滑动操作，拦截了这个事件序列，走了自身的 onTouchEvent()方法，反映在屏幕上就是列表的滑动。而这时 button 仍然处于按下的状态，所以在拦截的时候需要发送一个 Action_Cancel 来通知 button 恢复之前状态。
事件分发最终会走到View的dispatchTouchEvent()中。在View的dispatchTouchEvent()中没有onInterceptTouchEvent()，这里很容易理解，View没有child，也就不存在拦截。View的dispatchTouchEvent()直接return了自己的onTouchEvent()。如果onTouchEvent()返回true代表事件被消费，否则未消费的事件会向上传递，直到有View处理了事件或一直没有消费，最终回到Activity的onTouchEvent()终止。
有时候会有人混淆onTouchEvent和onTouch。首先，这两个方法都在View的dispatchTouchEvent()中：

*   如果touchListener不为null，并且这个View是enable的，而且onTouch返回true，都满足时直接return true，走不到onTouchEvent()方法。

*   否则，就会触发onTouchEvent()。因此onTouch优先于onTouchEvent获得事件处理权。

最后附上流程图总结：

![image](http://upload-images.jianshu.io/upload_images/15233854-0bba962dfaad709d?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

参考：https://juejin.im/entry/58df5b33570c35005798493c
https://juejin.im/post/5b8f15e26fb9a01a031b12d9#heading-3

**3.Handler的原理？**
与Handler密切相关的还有Message、MessageQueue、Looper。

*   Message。Message有两个关键的成员变量：target、callback：
    (1) target。就是发送消息的Handler
    (2) callback。调用Handler.post(Runnable)时传入的Runnable类型的任务。post事件的本质也是创建了一个Message，将我们传入的这个runnable赋值给创建的Message的callback这个成员变量。

*   MessageQueue。消息队列用于存放消息，其中重点关注next()方法，它会返回下一个待处理的消息。

*   Looper。Looper消息轮询器其实是连接Handler和消息队列的核心。想要在一个线程中创建一个Handler，首先要通过Looper.prepare()创建Looper，之后还得调用Looper.loop()开启轮询。
    (1) prepare()。这个方法做了两件事：首先通过ThreadLocal.get()获取当前线程中的Looper，如果不为空则抛出RuntimeException。否则创建Looper，并通过ThreadLocal.set(looper)将当前线程与刚刚创建的Looper绑定。值得注意的是，上面的消息队列的创建其实就是发生在Looper的构造函数中。
    (2)loop()。这个方法开启了整个事件机制的轮询。其本质是开启一个死循环，不断地通过MessageQueue的next()方法获取消息msg。拿到消息后会调用msg.target.dispatchMessage()来做处理。综上也就是调用handler.dispatchMessage()。

*   Handler。Handler重点在于发送消息和处理消息。
    (1)发送消息。其实发送消息除了 sendMessage 之外还有 sendMessageDelayed 和 post 以及 postDelayed 等等不同的方式。但它们的本质都是调用了 sendMessageAtTime。在 sendMessageAtTime 这个方法中调用了 enqueueMessage。在 enqueueMessage 这个方法中做了两件事：通过 msg.target = this 实现了消息与当前 handler 的绑定。然后通过 queue.enqueueMessage 实现了消息入队。
    (2)处理消息。 消息处理的核心其实就是dispatchMessage()这个方法。这个方法里面的逻辑很简单，先判断 msg.callback 是否为 null，如果不为空则执行这个 runnable。如果为空则会执行我们的handleMessage方法。

**4.ANR出现的情况有几种？ 怎么分析解决ANR问题？**
ANR(Application Not responding)。Android中，主线程(UI线程)如果在规定时内没有处理完相应工作，就会出现ANR。具体来说，ANR会在以下几种情况中出现:
(1) 输入事件(按键和触摸事件)5s内没被处理
(2) BroadcastReceiver的事件(onRecieve方法)在规定时间内没处理完(前台广播为10s，后台广播为60s)
(3) service 前台20s后台200s未完成启动
(4) ContentProvider的publish在10s内没进行完

分析ANR问题，需要结合Log以及trace文件。具体分析流程，可参照以下两篇文章：
https://www.jianshu.com/p/fa962a5fd939
https://blog.csdn.net/droyon/article/details/51099826

**5.内存泄露的场景有哪些？内存泄漏分析工具使用方法？**
常见的内存泄露有：

*   单例模式引起的内存泄露。

*   静态变量导致的内存泄露。

*   非静态内部类引起的内存泄露。

*   使用资源时，未及时关闭引起内存泄露。

*   使用属性动画引起的内存泄露。

*   Webview导致的内存泄露。

而对于内存泄露的检测，常用的工具有LeakCanary、MAT（Memory Analyer Tools）、Android Studio自带的Profiler。关于用法，网上教程很多，可自行查阅，下面两个经供参考：
三种用法、MAT
同时附上官方Android Profiler教程

**6.常用的设计模式有哪些？是否了解责任链模式？**
单例模式，观察者模式，工厂模式，建造者模式，构造者模式，中间者模式，桥接模式，适配器模式等等。

#### 总结

现在回顾一下，问的问题并不难，只是环环相扣问出了很多细节相关的知识点。由此看来，在日常开发中还需要注重基础。尤其对于开发经验是1-5年内的Android Developer，面试官考察的多数是基础知识是否牢固，沟通表达能力，总结能力。虽然此次面试黄了，但不失为一次很好的经历。

**除了上面的面试题，我还总结出了互联网公司Android程序员面试涉及到的绝大部分面试题及答案，并整理做成了文档，以及系统的进阶学习视频资料，免费分享给大家。（包括Java在Android开发中应用、APP框架知识体系、高级UI、全方位性能调优，NDK开发，音视频技术，人工智能技术，跨平台技术等技术资料），希望能帮助到你面试前的复习，且找到一个好的工作，也节省大家在网上搜索资料的时间来学习。**

### **领取方式：关注+点赞+加群：185873940 免费获取！**

**点击链接加入群聊【Android IOC架构设计】：[https://jq.qq.com/?_wv=1027&k=5tIZkaU](https://jq.qq.com/?_wv=1027&k=5tIZkaU)**

![image](https://upload-images.jianshu.io/upload_images/15233854-a8d2464ff93b02bd?imageMogr2/auto-orient/strip%7CimageView2/2/w/640/format/webp)

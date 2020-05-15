![](https://upload-images.jianshu.io/upload_images/15233854-df2f6439998849a5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从年前至今参加面试了很多公司，也收到了几家巨头的offer，还有其他公司的。总结下经验，也是对过去的一个回顾和总结吧。

# 一、简历

网上有很多对程序员简历的一些指导，这里就不重述，大家可以搜下网上其他大神的总结，结合自身情况修改下。我有几点建议：

1.尽量不要花哨，程序员和设计师或者产品运营还不一样，我们的简历成功与否决定权还是在技术面试官那，而他们看重的是你的项目经验内容和技术等描述。

2.技能描述这块尽量只写你懂得而且理解深刻的，可以适当加入一些新技术或流行框架，不过这块需要理解，没来得及看源码的可以看看大神们对它的总结，网上一大堆。

3.项目经验这块尽量加入关键词，比如使用了什么技术、用到哪些设计模式、优化数据对比、扩展总结之类的。而非一味地介绍这个项目内容(那是产品经理的描述)，比如性能优化这块，分为UI性能优化、内存优化、数据库优化、网络优化、耗电优化等等。可以从1.如何发现问题，2.怎么解决问题，3.解决效果对比，这几个方面去描述。举个简单例子——UI优化，可以从 UI出现什么问题(卡顿不流畅)，怎么查找问题(手机开发者权限>GPU过度绘制 发现层级问题，TraceView CPU使用情况分析)，怎么解决问题(降低层级、自定义View绘图出现问题等)，解决问题后性能再次对比。

# 二、技能储备

## （一）Java

#### 一、HashMap和Hashtable区别？

这个一定要去看源码！看源码！看源码！实在看不下去的可以上网看别人的分析。简单总结有几点：

1.HashMap支持null Key和null Value；Hashtable不允许。这是因为HashMap对null进行了特殊处理，将null的hashCode值定为了0，从而将其存放在哈希表的第0个bucket。

2.HashMap是非线程安全，HashMap实现线程安全方法为Map map = Collections.synchronziedMap(new HashMap())；Hashtable是线程安全

3.HashMap默认长度是16，扩容是原先的2倍；Hashtable默认长度是11，扩容是原先的2n+1

4.HashMap继承AbstractMap；Hashtable继承了Dictionary 

扩展，HashMap 对比 ConcurrentHashMap ，HashMap 对比 SparseArray，LinkedArray对比ArrayList，ArrayList对比Vector

#### 二、Java垃圾回收机制

需要理解JVM，内存划分——方法区、内存堆、虚拟机栈（线程私有）、本地方法栈（线程私有）、程序计数器（线程私有）, 理解回收算法——标记清除算法、可达性分析算法、标记-整理算法、复制算法、分代算法，优缺点都理解下。

#### 三、类加载机制

这个可以结合 热修复 深入理解下。[点击打开链接](https://link.jianshu.com/?t=http%3A%2F%2Fwww.importnew.com%2F18548.html)

#### 四、线程和线程池，并发，锁等一系列问题

这个可以扩展下 如何自己实现一个线程池？

#### 五、HandlerThread、IntentService理解

#### 六、弱引用、软引用区别

#### 七、int、Integer有什么区别

主要考值传递和引用传递问题

#### 八、手写生产者/消费者 模式

## （二）Android

#### 一、android启动模式

**需要了解下Activity栈和taskAffinity**

1.Standard：系统默认，启动一个就多一个Activity实例

2.SingleTop：栈顶复用，如果处于栈顶，则生命周期不走onCreate()和onStart()，会调用onNewIntent()，适合推送消息详情页，比如新闻推送详情Activity;

3.SingleTask：栈内复用，如果存在栈内，则在其上所有Activity全部出栈，使得其位于栈顶，生命周期和SingleTop一样，app首页基本是用这个

4.SingleInstance：这个是SingleTask加强本，系统会为要启动的Activity单独开一个栈，这个栈里只有它，适用新开Activity和app能独立开的，如系统闹钟，微信的视频聊天界面不知道是不是，知道的同学告诉我下，在此谢过！

另外，SingleTask和SingleInstance好像会影响到onActivityResult的回调，具体问题大家搜下，我就不详说。

Intent也需要进一步了解，Action、Data、Category各自的用法和作用，还有常用的

Intent.FLAG_ACTIVITY_SINGLE_TOP

Intent.FLAG_ACTIVITY_NEW_TASK

Intent.FLAG_ACTIVITY_CLEAR_TOP

等等，具体看下源码吧。

#### 二、View的绘制流程

ViewRoot

->performTraversal()->performMeasure()->performLayout()->perfromDraw()->View/ViewGroup measure()->View/ViewGroup onMeasure()->View/ViewGroup layout()->View/ViewGroup onLayout()->View/ViewGroup draw()->View/ViewGroup onDraw()

看下invalidate方法，有带4个参数的，和不带参数有什么区别；requestLayout触发measure和layout，如何实现局部重新测量，避免全局重新测量问题。

#### 三、事件分发机制

->dispatchTouchEvent()->onInterceptTouchEvent()->onTouchEvent()requestDisallowInterceptTouchEvent(boolean)

还有onTouchEvent()、onTouchListener、onClickListener的先后顺序

#### 四、消息分发机制

这个考得非常常见。一定要看源码，代码不多。带着几个问题去看：

1.为什么一个线程只有一个Looper、只有一个MessageQueue？
2.如何获取当前线程的Looper？是怎么实现的？（理解ThreadLocal）
3.是不是任何线程都可以实例化Handler？有没有什么约束条件？
4.Looper.loop是一个死循环，拿不到需要处理的Message就会阻塞，那在UI线程中为什么不会导致ANR？
5.Handler.sendMessageDelayed()怎么实现延迟的？结合Looper.loop()循环中，Message=messageQueue.next()和MessageQueue.enqueueMessage()分析。

#### 五、AsyncTask源码分析

优劣性分析，这个网上一大堆，不重述。

#### 六、如何保证Service不被杀死？如何保证进程不被杀死？

这两个问题我面试过程有3家公司问到。

#### 七、Binder机制，进程通信

Android用到的进程通信底层基本都是Binder，AIDL、Messager、广播、ContentProvider。不是很深入理解的，至少ADIL怎么用，Messager怎么用，可以写写看，另外序列化（Parcelable和Serilizable）需要做对比，这方面可以看看任玉刚大神的android艺术开发探索一书。

#### 八、动态权限适配问题、换肤实现原理

这方面看下鸿洋大神的博文吧

#### 九、SharedPreference原理，能否跨进程？如何实现？

**最近面试被怼了？缺面试题刷提升自己吗？**

**点击:**

[Android 学习，面试文档，视频收集大整理](https://links.jianshu.com/go?to=https%3A%2F%2Fshimo.im%2Fdocs%2FVxOufNNdaD4WS61R)

**来获取学习资料提升自己去挑战一下BAT面试难关吧**

![](https://upload-images.jianshu.io/upload_images/15233854-8eacad8141424cef.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

## （三）性能优化问题

#### 一、UI优化

a.合理选择RelativeLayout、LinearLayout、FrameLayout,RelativeLayout会让子View调用2次onMeasure，而且布局相对复杂时，onMeasure相对比较复杂，效率比较低，LinearLayout在weight>0时也会让子View调用2次onMeasure。LinearLayout weight测量分配原则。

b.使用标签

c.减少布局层级，可以通过手机开发者选项>GPU过渡绘制查看，一般层级控制在4层以内，超过5层时需要考虑是否重新排版布局。

d.自定义View时，重写onDraw()方法，不要在该方法中新建对象，否则容易触发GC，导致性能下降

e.使用ListView时需要复用contentView，并使用Holder减少findViewById加载View。

f.去除不必要背景，getWindow().setBackgroundDrawable(null)

g.使用TextView的leftDrawabel/rightDrawable代替ImageView+TextView布局

#### 二、内存优化

主要为了避免OOM和频繁触发到GC导致性能下降

a.Bitmap.recycle(),Cursor.close,inputStream.close()

b.大量加载Bitmap时，根据View大小加载Bitmap，合理选择inSampleSize，RGB_565编码方式；使用LruCache缓存

c.使用 静态内部类+WeakReference 代替内部类，如Handler、线程、AsyncTask

d.使用线程池管理线程，避免线程的新建

e.使用单例持有Context，需要记得释放，或者使用全局上下文

f.静态集合对象注意释放

g.属性动画造成内存泄露

h.使用webView，在Activity.onDestory需要移除和销毁，webView.removeAllViews()和webView.destory() 

备：使用LeakCanary检测内存泄露

#### 三、响应速度优化

Activity如果5秒之内无法响应屏幕触碰事件和键盘输入事件，就会出现ANR，而BroadcastReceiver如果10秒之内还未执行操作也会出现ANR，Serve20秒会出现ANR 为了避免ANR，可以开启子线程执行耗时操作，但是子线程不能更新UI，因此需要Handler消息机制、AsyncTask、IntentService进行线程通信。

备：出现ANR时，adb pull data/anr/tarces.txt 结合log分析

#### 四、其他性能优化

a.常量使用static final修饰
b.使用SparseArray代替HashMap
c.使用线程池管理线程
d.ArrayList遍历使用常规for循环，LinkedList使用foreach
e.不要过度使用枚举，枚举占用内存空间比整型大
f.字符串的拼接优先考虑StringBuilder和StringBuffer
g.数据库存储是采用批量插入+事务

## （四）设计模式

1.单例模式：好几种写法，要求会手写，分析优劣。一般双重校验锁中用到volatile，需要分析volatile的原理

2.观察者模式：要求会手写，有些面试官会问你在项目中用到了吗？实在没有到的可以讲一讲EventBus，它用到的就是观察者模式

3.适配器模式：要求会手写，有些公司会问和装饰器模式、代理模式有什么区别？

4.建造者模式+工厂模式：要求会手写

5.策略模式：这个问得比较少，不过有些做电商的会问。

6.MVC、MVP、MVVM：比较异同，选择一种你拿手的着重讲就行

## （五）数据结构

1.HashMap、LinkedHashMap、ConcurrentHashMap，在用法和原理上有什么差异，很多公司会考HashMap原理，通过它做一些扩展，比如中国13亿人口年龄的排序问题，年龄对应桶的个数，年龄相同和hash相同问题类似。

2.ArrayList和LinkedList对比，这个相对简单一点。

3.平衡二叉树、二叉查找树、红黑树，这几个我也被考到。

4.Set原理，这个和HashMap考得有点类似，考hash算法相关，被问到过常用hash算法。HashSet内部用到了HashMap

## （六）算法

算法主要考刷题吧，去[LeetCode](https://link.jianshu.com/?t=https%3A%2F%2Fleetcode.com%2F)和[牛客网](https://link.jianshu.com/?t=https%3A%2F%2Fwww.nowcoder.com%2Fta%2Fcoding-interviews%3Fpage%3D1)刷下。

## （七）源码理解

项目中多多少少会用到开源框架，很多公司都喜欢问原理和是否看过源码，比如网络框架Okhttp，这是最常用的，现在Retrofit+RxJava也很流行。

#### 一、网络框架库 Okhttp

okhttp源码一定要去看下，里面几个关键的类要记住，还有连接池，拦截器都需要理解。被问到如何给某些特定域名的url增加header，如果是自己封装的代码，可以在封装Request中可以解决，也可以增加拦截器，通过拦截器去做。

推荐一篇讲解[Okhttp](https://www.jianshu.com/p/230e2e2988e0)不错的文章

#### 二、消息通知 EventBus

1.EventBus原理：建议看下源码，不多。内部实现：观察者模式+注解+反射

2.EventBus可否跨进程问题？代替EventBus的方法（RxBus）

#### 三、图片加载库（Fresco、Glide、Picasso）

1.项目中选择了哪个图片加载库？为什么选择它？其他库不好吗？这几个库的区别

2.项目中选择图片库它的原理，如Glide（LruCache结合弱引用），那么面试官会问LruCache原理，进而问LinkedHashMap原理，这样一层一层地问，所以建议看到不懂的追进去看。如Fresco是用来MVC设计模式，5.0以下是用了共享内存，那共享内存怎么用？Fresco怎么实现圆角？Fresco怎么配置缓存？

#### 四、消息推送Push

1.项目中消息推送是自己做的还是用了第三方？如极光。还有没有用过其他的？这几家有什么优势区别，基于什么原因选择它的？

2.消息推送原理是什么？如何实现心跳连接？

#### 五、TCP/IP、Http/Https

网络这一块如果简历中写道熟悉TCP/IP协议，Http/Https协议，那么肯定会被问道，我就验证了。一般我会回答网络层关系、TCP和UDP的区别，TCP三次握手（一定要讲清楚，SYN、ACK等标记位怎样的还有报文结构都需要熟悉下），四次挥手。为什么要三次握手？DDoS攻击。为什么握手三次，挥手要四次？Http报文结构，一次网络请求的过程是怎样的？Http和Https有什么不同？SSL/TLS是怎么进行加密握手的？证书怎么校验？对称性加密算法和非对称加密算法有哪些？挑一个熟悉的加密算法简单介绍下？DNS解析是怎样的？

#### 六、热更新、热修复、插件化(这一块要求高点，一般高级工程师是需要理解的)

了解classLoader

#### 七、新技术

RxJava、RxBus、RxAndroid，这个在面试想去的公司时，可以反编译下他们的包，看下是不是用到，如果用到了，面试过程难免会问道，如果没有，也可以忽略，但学习心强的同学可以看下，比较是比较火的框架。

Retrofit，熟练okhttp的同学建议看下，听说结合RxJava很爽。

Kotlin

# 三、最后

简历首选内推方式，速度快，效率高啊！然后可以在拉钩，boss，脉脉，大街上看看。简历上写道熟悉什么技术就一定要去熟悉它，不然被问到不会很尴尬！做过什么项目，即使项目体量不大，但也一定要熟悉实现原理！不是你负责的部分，也可以看看同事是怎么实现的，换你来做你会怎么做？做过什么，会什么是广度问题，取决于项目内容。但做过什么，达到怎样一个境界，这是深度问题，和个人学习能力和解决问题的态度有关了。大公司看深度，小公司看广度。大公司面试你会的，小公司面试他们用到的你会不会，也就是岗位匹配度。

选定你想去的几家公司后，先去一些小的公司练练，学习下面试技巧，总结下，也算是熟悉下面试氛围，平时和同事或者产品PK时可以讲得头头是道，思路清晰至极，到了现场真的不一样，怎么描述你所做的一切，这绝对是个学术性问题！

面试过程一定要有礼貌！即使你觉得面试官不尊重你，经常打断你的讲解，或者你觉得他不如你，问的问题缺乏专业水平，你也一定要尊重他，谁叫现在是他选择你，等你拿到offer后就是你选择他了。

另外，描述问题一定要慢！不要一下子讲一大堆，慢显得你沉稳、自信，而且你还有时间反应思路接下来怎么讲更好。现在开发过多依赖ide，所以会有个弊端，当我们在面试讲解很容易不知道某个方法怎么读，这是一个硬伤......所以一定要对常见的关键性的类名、方法名、关键字读准，有些面试官不耐烦会说“你到底说的是哪个？”这时我们会容易乱了阵脚。正确的发音+沉稳的描述+好听的嗓音决对是一个加分项！

**最重要的是心态！心态！心态！重要事情说三遍！**面试时间很短，在短时间内对方要摸清你的底子还是比较不现实的，所以，有时也是看眼缘，这还是个看脸的时代。

## 文末

> 面试：如果不准备充分的面试，完全是浪费时间，更是对自己的不负责！
> 
> 金九银十面试季，赶快去为自己的面试做足准备吧！

不管怎么样，不论是什么样的大小面试，要想不被面试官虐的不要不要的，只有刷爆面试题题做好全面的准备，当然除了这个还需要在平时把自己的基础打扎实，这样不论面试官怎么样一个知识点里往死里凿，你也能应付如流啊~

**快来获取学习资料提升自己去挑战一下BAT面试难关吧！**

[Android 学习，面试文档，视频收集大整理](https://links.jianshu.com/go?to=https%3A%2F%2Fshimo.im%2Fdocs%2FVxOufNNdaD4WS61R)

![](https://upload-images.jianshu.io/upload_images/15233854-0e3ace121a77df2d.png?imageMogr2/auto-orient/strip|imageView2/2/w/835/format/webp)

希望大家都能找到合适自己满意的工作！fighting！

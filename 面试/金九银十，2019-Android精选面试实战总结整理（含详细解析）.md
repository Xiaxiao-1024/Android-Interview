![](https://upload-images.jianshu.io/upload_images/15233854-5cdcfb27e547d65d?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####**今日头条屏幕适配的原理？**

1：首先计算出 density，计算公式：当前设备屏幕总宽度（单位为像素）/ 设计图总宽度（单位为 dp) = densitydensity 的意思就是 1 dp 占当前设备多少像素计算density 的原因：在布局文件中填写的是什么单位，最后都会被转化为 px，系统就是通过上面的方法，将你在项目中任何地方填写的单位都转换为 px

但是，今日头条适配方案默认项目中只能以高或宽中的一个作为基准，来进行适配

####**简述Android中的加固和使用平台?**

*   加固:防止代码反编译,提高代码安全性

*   加固三方平台,梆梆安全,360加固,爱加密等

区别:梆梆安全,360加固看不到项目中的类,爱加密看的到Java类,单看不到里面的方法实现体,效果比前面差一点点

加固的底层原理:第三方加固的应用会生成一个Apk,然后把你的APK读取出来,在封装到这个第三方应用的APK里面.

####**如何对APK瘦身?**

* 使用混淆,
* 开启shrinkResourse(shrink-收缩),会将没有用到的图片变成一个像素点
* 删除无用的语言资源(删除国际化文件)
* 对于非透明的大图,使用JPG(没有透明度信息),代替PNG格式
* 使用tinypng进行图片压缩
* 使用webp图片格式,进一步压缩图片资源
* 使用第三方包时把用到的代码加到项目中来,避免引用整一个第三方库

####**简述多渠道打包及原理和常用操作?**

针对每一个渠道(应用市场)都生成一个带有渠道标识的apk文件

原理：用户下载启动应用,获取渠道标识,和设备的唯一标识,并上传到服务器里面,服务器这里就 会根据获取的记录,根据渠道号然后判断是否存在该服务器的表里面。(打标记,取标记,上传标记)

1)友盟多渠道打包：在清单文件中定义一个占位符,在gradle脚本中替换占位符(会使用到Python)

2)美团打包,在meta-data中创建一个空的文件,以文件名标识渠道,做一个解压与压缩的操作,速度会比较快

3)新一代多渠道打包,将渠道标识添加到.apk文件的末尾,并不会对源文件损坏

####**Android下的数据存储方式有那些?**

* 内部存储,直接存储在内部文件中
* 外部存储,首先要判断外部存储条件是否可用,然后进行存储
* SP存储,底层是Xml实现的,以键值对形式存储内部的数据,适宜于轻量级的存储,存储的数据类型有,boolean,String,int
* 数据库存储,SQlite存储,轻量级的数据库,强大的增删改查功能
* 内容提供者,ContentProvider,将自己愿意暴露的一部分数据供外部使用操作
* 网络存储,等等

####**Sharepreference 线程安全问题？**

官方文档明确指出，SharedPreferences不支持多线程，进程也是不安全的

如果想要实现线程安全需重新实现其接口，如下

![](https://upload-images.jianshu.io/upload_images/15233854-b3609c36759ea8ba?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

假设在多进程访问SharePreferences的情况下，该如何保证进程安全和共享数据?

解决办法就是：将需要共享数据的字段提出来统一存储到一个文件中。

####**Android开发下如何有效进行屏幕适配?**

* 机型适配，去一些统计网站诸如友盟,现在叫友盟+去看一下市场上最流行的Android机型,有针对性的切图
* 屏幕适配，适配主流xhdpi屏幕尺寸，使用relativelayout，linerlayout等布局，多使用matchparent，wrapcontent，及配合weight,权重处理，
* 还有就是在代码中，设计到具体尺寸的要使用dp2px的转换，
* 图片使用可拉伸.9图片，imageview使用scaletype缩放；
* 使用权重,等比例,百分比布局等等

### 对象序列化：

####**为什么要序列化？**

1）永久性保存对象，保存对象的字节序列到本地文件中；
2）通过序列化对象在网络中传递对象；
3）通过序列化在进程间传递对象。

在Android中实现序列化有两个选择：一是实现Serializable接口（是JavaSE本身就支持的），一是实现Parcelable接口（是Android特有功能，效率比实现Serializable接口高效，可用于Intent数据传递，也可以用于进程间通信（IPC））。实现Serializable接口非常简单，声明一下就可以了，而实现Parcelable接口稍微复杂一些，但效率更高，推荐用这种方法提高性能。两种实现方式依旧是贴url，方便大家快速查询

### 两种序列化相关

既然Google推荐Parcelable这种序列化，在这里，推荐一键生成序列化的插件，

在Android Studio里面搜索插件，如下图，写起序列化（根本不用你写）那就是一个美滋滋呐~

#### **OkHttp相关?**

OkHttp支持同步和异步数据请求，但异步请求是在子线程 (因为原生OkHttp的使用时回调方法是在子线程进行的，要刷新界面还需要用Handler作处理，可以使用第三方的okhttp-utils,Okgo等等)；

OkHttp里面封装了线程池、数据转换、GZIP压缩（减少流量的传输）、HTTP协议缓存等,

OKHttp优点—-使用GZip压缩减少传输的数据量,缓存(减少重复请求);

失败重试(如果你的服务有多个IP地址,如果第一次连接失败,OKHttp将使用备用地址)

OKhttp是对http协议的封装,比较底层,因此拓展性强,便于封装;

OKhttp基于NIO(JDK1.5,非阻塞式IO)效率更高

####**ButterKnife相关?**

**简介：**

一款快速高效的注入框架，节约开发时间减少代码量（依靠插件动态生成View,点击事件等等）

**优点：**

1.强大的View绑定和Click事件处理功能，简化代码，提升开发效率
2.方便的处理Adapter里的ViewHolder绑定问题
3.运行时不会影响APP效率，使用配置方便
4.代码清晰，可读性强

**使用经验：**

1.Activity ButterKnife.bind(this);必须在setContentView();之后，且父类bind绑定后，子类不需要再bind

2.Fragment ButterKnife.bind(this, mRootView);

3.属性布局不能用private or static 修饰，否则会报错，（注意权限）

4.setContentView()不能通过注解实现。（其他的有些注解框架可以）

原理：利用注解和反射去获取绑定ViewID,

关于原理详情可参考笔者的这一篇:Android-定制专属ButterKnife框架,该文详细介绍了ButterKnife框架并模仿了一个注解绑定View的框架

####**Rxjava概念,常用操作符及拓展?**

**简介：**

一款优雅的异步框架,代替之前的AsyncTask / Handler / XXX / …

其强大的操作符和链式写法,线程切换等有助于提高开发效率和快速定位Bug

与Retrofit搭配使用更是有意想不到的效果，

底层原理：观察者模式

等一些相应的博客

**缺点：**

1：操作符太多会增加学习成本时间

2：使用不好，容易导致内存泄露(解决方式，推荐Rxlifecycle结合Rxjava，规避内存泄漏风险)

**最近面试被怼了？缺面试题刷提升自己吗？**

**点击:**

[Android 学习，面试文档，视频收集大整理](https://links.jianshu.com/go?to=https%3A%2F%2Fshimo.im%2Fdocs%2FVxOufNNdaD4WS61R)

**来获取学习资料提升自己去挑战一下BAT面试难关吧**

![](https://upload-images.jianshu.io/upload_images/15233854-8eacad8141424cef.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

####**ANR相关**

ANR全名Application Not Responding, 也就是”应用无响应”. 当操作在一段时间内系统无法处理时, 系统层面会弹出上图那样的ANR对话框.

在Android里, App的响应能力是由Activity Manager和Window Manager系统服务来监控的. 通常在如下两种情况下会弹出ANR对话框:

A) 5s内无法响应用户输入事件(例如键盘输入, 触摸屏幕等).

B) BroadcastReceiver在10s内无法结束.

造成以上两种情况的首要原因就是在主线程(UI线程)里面做了太多的阻塞耗时操作,, 例如文件读写, 数据库读写, 网络查询等等.

####**如何分析ANR?**

ANR产生时, 系统会生成一个traces.txt的文件放在/data/anr/下. 开发人员可通过adb命令将其导出到本地 ($adb pull data/anr/traces.txt .)通过分析,我们可以根据具体的日志查看Anr原因( 如: 普通阻塞,CPU满负荷,内存泄露 )

####**Android中那些场景是执行在主线程的?**

1)Activity生命周期回调都是执行在主线程的.
2)Service默认是执行在主线程的.
3)BroadcastReceiver的onReceive回调是执行在主线程的.
4)没有使用子线程的looper的Handler的handleMessage, post(Runnable)是执行在主线程的.
5)AsyncTask的回调中除了doInBackground, 其他都是执行在主线程的.
6)View的post(Runnable)是执行在主线程的.等等

####**三级缓存:**

当我们第一次打开应用获取图片或其它资源时，首先到网络去下载，然后依次存入内存缓存，磁盘缓存，

当我们再一次需要用到刚才下载的这张图片时，就不需要再重复的到网络上去下载，直接可以从内存缓存和磁盘缓存中找，由于内存缓存速度较快，我们优先到内存缓存中寻找该图片，如果找到则运用，

如果没有找到（内存缓存大小有限），那么我们再到磁盘缓存中去找。

只要我们合理的去协调这三层缓存运用，便可以提升应用性能,给用户更好的体验

三级缓存指的是：内存缓存、本地缓存、网络缓存。其各自的特点是内存缓存速度快, 优先读取，本地缓存速度其次, 内存没有该资源信息就去读取本地内存，网络缓存速度较慢(比较对象是内存缓存和本地缓存),假设本地内存也没有,才请求网络获取。

####**内存泄漏:**

当应用内部不再需要某个实例后，但是这个对象却仍然被引用，这个情况就叫做内存泄露(Memory Leak)。安卓虚拟机为每一个应用分配一定的内存空间，当内存泄露到达一定的程度就会造成内存溢出。

**导致内存泄露常见原因：**

1)静态变量直接或者间接地引用了Activity对象就会造成内存泄露

2)Activity使用了静态的View(View会持有Activity的对象的引用)

3)Activity定义了静态View变量???

4)ImageSpan引用了Activity Context

5)单例中引用了Activity的Context(需要使用Application的Context)

6)对于使用了BraodcastReceiver，ContentObserver，File，Cursor，Stream，Bitmap等资源，应该在Activity销毁时及时关闭或者注销，否则这些资源将不会被回收，从而造成内存泄漏。

7)静态集合保存的对象没有及时消除(不使用的时候置为null)

8)在Java中,非静态(匿名)内部类会引用外部类对象,而静态内部类不会引用外部类对象

9)在Activity中,创建了非静态内部类(内部类直接或者间接引用了Activity)的静态成员变量

10)线程包括AsyncTask的使用,Activity退出后线程还在运行(线程在死循环),并且在线程中使用了Activity或view对象(解决方法:不要直接写死循环,可以设置一个布尔类型的TAG,当activity推出的时候,设置TAG为False)

11)Handler对象的使用,Activity退出后Handler还是有消息需要处理(解决方法:在退出activity之后,移除消息)

12)WebView造成的内存泄漏(在onDestory中销毁)

####**如何进行内存泄露分析?**

A: 通过Android Studio 窗口进行分析,查看内存分配情况,如果操作应用是内存一直往上涨说明存在内存泄露

B: 定位内存泄露分析的工具—-MAT(Memory Analyzer tool)

C: 使用开源库LeakCanary快速定位内存泄露

![](https://upload-images.jianshu.io/upload_images/15233854-8a9273eea29dcb7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####**Android中的四大组件相关?**

**Activity：**

Activity是一个应用程序组件，提供一个屏幕(狭义的理解就是当前APP的界面)，用户可以用来交互为了完成某项任务。(点击,登录,跳转页面)

Activity中所有操作都与用户密切相关，是一个负责与用户交互的组件，可以通过setContentView(View)来显示指定控件(设置布局文件)。

在一个android应用中，一个Activity通常就是一个单独的屏幕，它上面可以显示一些控件也可以监听并处理用户的事件做出响应。

####**Activity四种启动模式?**

Activity的启动模式指,可以根据实际开发需求为Activity设置对应的启动模式，从而可以避免创建大量重复的Activity等问题。

**1)standard**

standard为Activity的默认启动模式，可以不用写配置。在这个模式下，都会默认创建一个新的实例。因此，在这种模式下，可以有多个相同的实例，也允许多个相同Activity叠加。(点back键会依照栈顺序依次退出)

**2)singleTop**

singleTop模式下,Activity可以有多个实例，但是不允许多个相同Activity叠加。即，如果Activity在栈顶的时候，启动相同的Activity，不会创建新的实例，而会调用其onNewIntent方法。

**3)singleTask**

singleTask表示只有一个实例。在同一个应用程序中启动他的时候，若Activity不存在，则会在当前task创建一个新的实例，若存在，则会把task中在其之上的其它Activity destory掉并调用它的onNewIntent方法。如果是在别的应用程序中启动它，则会新建一个task，并在该task中启动这个Activity，singleTask允许别的Activity与其在一个task中共存，也就是说，如果我在这个singleTask的实例中再打开新的Activity，这个新的Activity还是会在singleTask的实例的task中。

**4)singleInstance**

只有一个实例，并且这个实例独立运行在一个task中，这个task只有这个实例，不允许有别的Activity存在。

**5)BraodcastReceiver:（待补充）**

使用了设计模式中的观察者模式：基于消息的发布/订阅事件模型。

注册的方式分为两种：静态注册、动态注册

**6)ContentProvider:(待补充)**

外界可以通过ContentResolver接口来访问ContentProvider(内容提供者)中的数据。Uri 通用资源标志符（Universal Resource Identifier）Uri代表要操作的数据，Android中可用的每种资源 - 图像、视频片段等都可以用Uri来表示。ContentProvider共享数据是通过定义一个对外开放的统一的接口来实现的。然而，应用程序并不直接调用这些方法，而是使用一个 ContentResolver 对象，调用它的方法作为替代。ContentResolver可以与任意内容提供者进行会话，与其合作来对所有相关交互通讯进行管理。当外部应用需要对ContentProvider中的数据进行添加、删除、修改和查询操作时，可以使用ContentResolver类来完成，要获取ContentResolver对象，可以使用Context提供的getContentResolver()方法。

**7)IntentService：**

IntentService是Service的子类，比普通的Service增加了额外的功能。IntentService会创建独立的worker线程来处理所有的Intent请求；会创建独立的worker线程来处理onHandleIntent()方法实现的代码，无需处理多线程的问题；所有请求处理完成后，IntentService会自动停止，开发者无需手动调用stopSelf()方法停止Service；

####**简述System.exit(0) 、onDestory()、Activity.finish()三者的区别**

1)System.exit(0) 是你正常结束程序,kill 掉当前进程,针对的是整个Application

2)onDestory()方法是Activity生命周期的最后一步，资源空间等就被回收了。当重新进入此Activity的时候，必须重新创建，执行onCreate()方法.

3)Activity.finish()当你调用此方法的时候，系统只是将最上面的Activity移出了栈，并没有及时的调用onDestory（）方法，也就是占用的资源没有被及时释放。

####**图片优化，以及图片加载框架的使用，如Picasso、 Fresco、Glide等？**

1)尽量使用小的图片,对图片进行压缩，bitmapfactory.options图片配置类，insimplesize进行缩放，设置图片的编码方式；对图片使用软引用，内存不够时即时释图片内存；对图片的复用，三级缓存的使用；

即时回收不再使用的bitmap对象；

2)Picasso,不支持gif，缓存的是Argb8888的原图，占用内存较大,图片的框架使用了OkHttp缓存机制,使用Http协议缓存,也是异步加载.

3)Fresco,框架是FaceBook公司推出的,适合批量加载图片,底层是通过三级缓存(2级内存,1级磁盘)

加载成功后自动替换成目标图片

4)glide,Google公司14年推出来的,可以加载GIF图,也可以根据指定图片清晰度,底层的原理:为Bitmap维护一个对象池,对象池的目的是通过减少对象的分配,以重用来提高性能.对象池也可以帮助提高滚动的性能。API简洁易调用

这里有一份[Android 学习，面试文档，视频收集大整理](https://links.jianshu.com/go?to=https%3A%2F%2Fshimo.im%2Fdocs%2FVxOufNNdaD4WS61R)

###**Handle相关:**

Handler 工作流程基本包括 Handler、Looper、Message、MessageQueue 四个部分。但我们在日常开发中，经常都只会用到 Handler 和 Message 两个类。Message 负责消息的搭载，里面有个target用于标记消息，obj用于存放内容，Handler 负责消息的分发和处理。

####**一般在开发中是怎么使用 Handler 的？**

官方不允许在子线程中更新 UI，所以我们经常会把需要更新 UI 的消息直接发给处理器 Handler，通过重写 Handler 的handleMessage()方法进行 UI 的相关操作。

####**Handle使用中就没什么需要注意的吗？**

有，Handler 如果设置为私有变量的话，Android Studio 会报警告，提示可能会造成内存泄漏，这种情况可以通过设置为静态内部类 + 弱引用，或者在onDestroy()方法中调用Handler.removeCallbacksAndMessages(null)即可避免

Handler 整体工作流程浅析分为以下四个步骤：

异步通信准备 => 消息入队 => 消息循环 => 消息处理

**A：异步通信准备**

I：假定是在主线程创建 Handler，则会直接在主线程中创建处理器对象Looper、消息队列对象MessageQueue和 Handler 对象。

需要注意的是，Looper和MessageQueue均是属于其创建线程的。

II：Looper对象的创建一般通过Looper.prepareMainLooper()和Looper.prepare()两个方法，而创建Looper对象的同时，将会自动创建MessageQueue。

III：创建好MessageQueue后，Looper将自动进入消息循环。此时，Handler自动绑定了主线程的Looper和MessageQueue。

**B：消息入队**

工作线程通过Handler发送消息Message到消息队列MessageQueue中，消息内容一般是 UI 操作。发送消息一般都是通过Handler.sendMessage(Message msg)和Handler.post(Runnabe r)两个方法来进行的。而入队一般是通过MessageQueue.enqueueeMessage(Message msg,long when)来处理。

**C：消息循环**

主要分为「消息出队」和「消息分发」两个步骤，Looper会通过循环取出消息队列MessageQueue里面的消息Message，并分发到创建该消息的处理者Handler。如果消息循环过程中，消息队列MessageQueue为空队列的话，则线程阻塞。

**D：消息处理**

Handler接收到Looper发来的消息，开始进行处理。

![](https://upload-images.jianshu.io/upload_images/15233854-5f1ba1e3c147cd99.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## **拓展**

####**先简单介绍下你自己？**

分析：除了向面试官做简单的基本自我介绍之外，还需向面试官展现自身对该职业所必须具备的一些自身特质，

比如，面试程序员职业需要间接的向面试官表示自己思维严谨，对细节的处理，理性思维，假设论证等等；面试产品等职业，需要向面试官通过自己的一些故事间接展现对产品的看法以及独特的思维个性等等

**切入点：自身特质能否符合该职位的预期需求**

####**自己的兴趣爱好特长有那些？**

在企业和面试官看来，如果求职者的爱好和应聘的岗位在某些方面恰恰有正向关联，就会有兴趣。面试官也会通过应聘者的兴趣爱好来判断其价值观是否与企业文化契合，能否很好地融入工作团队。求职者的回答将有可能为面试加分。

**下列兴趣爱好所反映出的一些性格和职业方向可供参考：**

1．篮球，足球，排球：团队精神，适用大多数岗位。
2．围棋，国际象棋：战略意识，适合市场类或高端职位。
3．阅读，古典音乐：高雅，适合文职类的职位。
4．旅游：适应不同环境的能力，快速学习的能力，适合销售业务类职位。
5．舞蹈：外向，易沟通，适合公关、市场类的职位。

####**对自己的期望和规划？**

分析：职业发展规划表面上看是在考察你（求职者）、职位、公司三者之间长期的契合程度，但实际上，这么大的一个问题完全不是三眼两语间能够表达清楚的。面试官（无论HR还是专业部门的）主要是看你回答问题时的思路是否清晰，回答中表现出的工作态度如何，顺便看看你是否对公司和职位有足够的了解。所以不管答案如何，最关键的就是不能茫然。

**切入点：依旧自身特点，对未来期望和规划需表述清晰，思维敏捷**

####**谈谈自己的优点和缺点？**

**先谈缺点：**

技术行业面试基本是由该岗位未来同事和上司进行。这种面试技术性强，行为问题主要考察就是你是否真心想做这个工作（而不是当跳板或者听说高薪体面而来）和你性格与文化是否相符。所有答案都应该围绕这两点组织（即每个经历都应回归到你通过这个经历学到什么，该职位所需关键技巧，这些经历为何让你想做这个工作，和该经历体现出你什么样的个人风格）。对这个问题因为好的回答而留下好印象很难，

**关键是避免留下坏印象。**

**要点以下：**

1）避免避重就轻，不要谈一个算不得缺点的缺点。比如熬夜会困，或者（待人接物）太客气等等。

2）避免谈非职业缺点，比如有感情洁癖，挑食，不擅长陪女友逛街，做饭经常不懂会煮糊。

3）避免谈到无法改善的弱点，比如我算数必须用计算器，我脑子不好用看书不理解。

4）避免谈到致命弱点，比如脾气怪异,不喜欢合作,迟到早退等。

那谈什么最好呢？我认为要点有三：

1）谈已经在改正的缺点／有明确计划来改正的缺点。尤其是你能够充分论证在近期就可以解决的缺点。

2）谈一个利用你的优点改正的缺点，顺便带出一个优点。（这是较高效的沟通技巧）

**相对较好的回答：**

1）喜欢追求细节导致项目／作业未能按期完成。通过时间管理能力改变工作方式，先完成框架再改善细节得以解决；

2）不知如何拒绝，同事要求帮忙一概揽下，影响自身工作进度。通过多任务处理能力设定优先顺序，以该优先顺序表向求助同事展示自己手上工作，并给其一个自己在何时可以给予帮助的时间估计，让求助人自行决定是否求助，问题解决

3）对处理同一问题的解决办法上，由于组员自己的技术偏好和技术构成不一样容易造成沟通障碍及影响项目计划，所以,应学会高效和有效沟通方式及工作技巧

## 文末

> 面试：如果不准备充分的面试，完全是浪费时间，更是对自己的不负责！
> 
> 金九银十面试季，赶快去为自己的面试做足准备吧！

不管怎么样，不论是什么样的大小面试，要想不被面试官虐的不要不要的，只有刷爆面试题题做好全面的准备，当然除了这个还需要在平时把自己的基础打扎实，这样不论面试官怎么样一个知识点里往死里凿，你也能应付如流啊~

**快来获取学习资料提升自己去挑战一下BAT面试难关吧！**

[Android 学习，面试文档，视频收集大整理](https://links.jianshu.com/go?to=https%3A%2F%2Fshimo.im%2Fdocs%2FVxOufNNdaD4WS61R)

![](https://upload-images.jianshu.io/upload_images/15233854-0e3ace121a77df2d.png?imageMogr2/auto-orient/strip|imageView2/2/w/835/format/webp)

**最后祝大家面试顺利！**

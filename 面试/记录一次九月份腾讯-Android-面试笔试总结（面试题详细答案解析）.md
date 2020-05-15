![](https://upload-images.jianshu.io/upload_images/15233854-79d11fdc2425f6ba.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>今天把之前九月份腾讯面试笔试题目整理出来给大家分享分享，还附上了我自己的一些答案解析，给大家参考下，希望能对大家有帮助。

###面试题目录
* Activity中的几种启动模式
* Android消息机制
* IntentService
* 事件分发
* Android性能优化、内存优化
* 内存优化
* View的绘制
* Eventbus原理
* Rxjava的操作符有哪些，说说他们的作用
* 线程锁 锁方法和类对象啥的有啥区别
* AsyncTask原理
* 说说MVP和MVVM的特点
* Android中用到的观察者模式有哪些地方
* 说说google新出的Lifecycle框架
* okhttp原理
* Retrofit原理
* RecyclerView源码、缓存分析
* Binder机制
* Android Jetpack
* Kotlin

### Activity中的几种启动模式

activity的几种启动模式是android中常考的知识点，一般会考察有哪几种启动模式，以及每种启动模式在什么场景下使用：

`standard`:这个是android默认的Activity启动模式，每启动一个Activity都会被实例化一个Activity，并且新创建的Activity在堆栈中会在栈顶。

`singleTop`:如果当前要启动的Activity就是在栈顶的位置，那么此时就会复用该Activity，并且不会重走onCreate方法，会直接它的`onNewIntent`方法，如果不在栈顶，就跟`standard`一样的。如果当前activity已经在前台显示着，突然来了一条推送消息，此时不想让接收推送的消息的activity再次创建，那么此时正好可以用该启动模式，如果之前activity栈中是A-->B-->C如果点击了推动的消息还是A-->B--C，不过此时C是不会再次创建的，而是调用C的onNewIntent。而如果现在activity中栈是A-->C-->B，再次打开推送的消息，此时跟正常的启动C就没啥区别了，当前栈中就是A-->C-->B-->C了。

`singleTask`:该种情况下就比`singleTop`厉害了，不管在不在栈顶，在Activity的堆栈中永远保持一个。这种启动模式相对于singleTop而言是更加直接，比如之前activity栈中有A-->B-->C---D，再次打开了B的时候，在B上面的activity都会从activity栈中被移除。下面的acitivity还是不用管，所以此时栈中是A-->B，一般项目中主页面用到该启动模式。

`singleInstance`:该种情况就用得比较少了，主要是指在该activity永远只在一个单独的栈中。一旦该模式的activity的实例已经存在于某个栈中，任何应用在激活该activity时都会重用该栈中的实例，解决了多个task共享一个activity。其余的基本和上面的singleTask保持一致。

上面的各种启动模式主要是通过配置清单文件，常见还有在代码中设置flag也能实现上面的功能：

`FLAG_ACTIVITY_CLEAR_TOP`：这种启动的话，只能单纯地清空栈上面的acivity，而自己会重新被创建一次，如果当前栈中有A-->B-->C这几种情况，重新打开B之后，此时栈会变成了A-->B，但是此时B会被重新创建，不会走B的onNewIntent方法。这就是单独使用`FLAG_ACTIVITY_CLEAR_TOP`的用处，能清空栈上面的activity，但是自己会重新创建。
如果在上面的基础上再加上`FLAG_ACTIVITY_SINGLE_TOP`此时就不重新创建B了，也就直接走B的onNewIntent。它两者结合着使用就相当于上面的singleTask模式。
如果只是单独的使用`FLAG_ACTIVITY_SINGLE_TOP`跟上面的singleTop就没啥区别了。

`FLAG_ACTIVITY_CLEAR_TOP+FLAG_ACTIVITY_SINGLE_TOP=singleTask`,此时要打开的activity不会被重建，只是走onNewIntent方法。

`FLAG_ACTIVITY_SINGLE_TOP=singleTop`

`FLAG_ACTIVITY_NEW_TASK`

*   在相同taskAffinity情况下：启动activity是没有任何作用的。

*   在不同taskAffinity情况下：
    如果启动不同栈中的activity已经存在了某一个栈中的activity，那么此时是启动不了该activity的，因为栈中已经存在了该activity；如果栈中不存在该要启动的activity，那么会启动该acvitity，并且将该activity放入该栈中。

`FLAG_ACTIVITY_NEW_TASK`和`FLAG_ACTIVITY_CLEAR_TOP`一起使用，并且要启动的activity的taskAffinity和当前activity的taskAffinity不一样才会和singleTask一样的效果，因为要启动的activity和原先的activity不在同一个taskAffinity中，所以能启动该activity，这个地方有点绕，写个简单的公式:

*   `FLAG_ACTIVITY_NEW_TASK`如果启动同一个不同taskAffinity的activity才会有效果。
*   `FLAG_ACTIVITY_NEW_TASK`和`FLAG_ACTIVITY_CLEAR_TOP`如果一起使用要开启的activity和现在的activity处于同一个taskAffinity，那么效果还是跟没加`FLAG_ACTIVITY_NEW_TASK`是一样的效果。
*   `FLAG_ACTIVITY_NEW_TASK`和`FLAG_ACTIVITY_CLEAR_TOP`启动和现在的activity不是同一个taskAffinity才会和singleTask一样的效果。

`FLAG_ACTIVITY_CLEAR_TASK`

*   在相同taskAffinity情况下：和`FLAG_ACTIVITY_NEW_TASK`一起使用，启动activity是没有任何作用的。
*   在不同taskAffinity情况下：和`FLAG_ACTIVITY_NEW_TASK`一起使用，如果要启动的activity不存在栈中，那么启动该acitivity，并且将该activity放入该栈中，如果该activity已经存在于该栈中，那么会把当前栈中的activity先移除掉，然后再将该activity放入新的栈中。

`FLAG_ACTIVITY_NEW_TASK`+`FLAG_ACTIVITY_SINGLE_TOP`用在当app正在运行点击push消息进到某个activity中的时候，如果当前处于该activity，此时会触发activity的onNewIntent。

`FLAG_ACTIVITY_NEW_TASK`+`FLAG_ACTIVITY_CLEAR_TOP`用在app没在运行中，启动主页的activity，然后在相应的activity中做相应的activity跳转。

### Android消息机制

消息机制指Handler、Looper、MessageQueue、Message之间如何工作的。

*   handler是用来处理消息和接收消息的中间者，handler的创建会伴随着handler中产生looper和MessageQueue，handler依赖于looper，looper依赖于MessageQueue，所以在子线程中使用handler抛出异常是因为子线程中没有初始化looper对象，而主线程中looper是在`ActivityThread`中已经初始化过了，所以能直接在主线程中能拿到Handler。

*   Looper是用来轮询消息，说白了就是通过loop方法实现死循环，有消息的时候，通过MessageQueue.next方法取出message，没有消息的时候，线程处于阻塞的状态。在有消息的时候获取到消息，将消息交给了handler，handler会根据消息中有没有callback，如果有callback会直接callback，否则通过handleMessage处理。

*   MessageQueue是一个单链表结构来存储Message，每次通过next方法取出Message消息后，取完之后将message.next给当前的message，再将message.next=null，实际上就是移除当前的message。但是在looper里面每次在next取出message后，放到了message的sPool里面，缓存起来方便使用。

*   Message就没什么好说的，主要存储平常经常用的obj和what信息，以及我们不用关心的target和callback等。

**这里会问到，一个线程会有几个Looper，几个Handler，以及Looper会存在线程哪里？**

> 一个线程一个Looper，可以有多个Handler，Looper会存在线程的ThreadLocal对象里，该对象是线程的缓存区

`ThreadLocal`:它是和线程一一对应的，从Thread类可以看出来，ThreadLocal是作为Thread变量来使用。ThreadLocal只是ThreadLocalMap的一个包装类，实现了get和set方法，而ThreadLocalMap实际是一个由Entry内部类组成的数组，Entry是继承自弱应用，弱引用里面放的就是ThreadLocal当前对象，Entry的value存的是当前线程要存储的对象，value作为Entry的成员变量。
**ThreadLocal经常会问到内存泄漏的问题，从上面分析可以发现ThreadLocalMap里面的Entry对象存储的ThreadLocal弱引用，而value直接作为Entry的强引用，因此在用到了ThreadLocal的地方，防止内存泄漏，手动调用remove方法。**

**最近面试被怼了？缺面试题刷提升自己吗？**

**点击:**

[Android 学习，面试文档，视频收集大整理](https://links.jianshu.com/go?to=https%3A%2F%2Fshimo.im%2Fdocs%2FVxOufNNdaD4WS61R)

**来获取学习资料提升自己去挑战一下BAT面试难关吧**

![](https://upload-images.jianshu.io/upload_images/15233854-8eacad8141424cef.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

### IntentService

`IntentService`是google在原生的Service基础上通过创建子线程的Service。也就是说IntentService是专门为android开发者提供的能在service内部实现耗时操作的service。我们可以通过重写`onHandleIntent`方法实现耗时操作的回调处理，而且IntentService在耗时操作完成后，会主动销毁自己，`IntentService`可以通过多次启动来完成多个任务，而`IntentService`只会被创建一次，每次启动的时候只会触发onStart方法。内部是实现了Handler异步处理耗时操作的过程，一般多用在Service中需要处理耗时操作的功能。

提问：为什么IntentService中能实现耗时操作?

*   在onCreate中，通过HandlerThread来开启一条线程，而HandlerThread线程中会跟我们平常用的Handler不太一样，在run方法中创建了looper对象，所以HandlerThread能让IntentService在子线程中使用handler达到耗时操作。

### HandlerThread

`HandlerThread`本身也是Thread，只是在Thread基础上封装上了Handler的载体，并且在run方法中创建了looper对象，这也是为什么在IntentService中能在HandlerThread中直接用handler的原因。而我们知道一个线程是可以有多个handler，所以用HandlerThread更加方便我们不用关心Handler的创建，一般用在多线程中直接处理任务。

### 事件分发

事件分发主要分三块:分发、拦截、消费；
当我们触摸到屏幕的时候，默认会先走Activity的分发，接着走ViewGroup的分发，然后到ViewGroup的拦截，后面再到View的分发事件，最后会传到View的消费事件，如果View不消费，紧接着回传到ViewGroup的消费事件，如果ViewGroup也不消费，最后回到View的消费事件。整个事件分发构成了一个u型结构，下面总结了分发的细节流程：

*   在action_down的时候，首先去判断viewgroup的`onInterceptTouchEvent`是不是拦截了，如果拦截的话`intercepted=true`，就会走`handled = dispatchTransformedTouchEvent(ev, canceled, null, TouchTarget.ALL_POINTER_IDS);`方法，此处传的child是null，因此直接走super.dispatchTouchEvent方法，不走child的`dispatchTouchEvent`方法
*   如果`onInterceptTouchEvent`不拦截，那么在action_down的时候，去获取child.dispatchTouchEvent方法，如果返回true，那么`mFirstTouchTarget`和`newTouchTarget`都不为空，因此在action_move和action_up的时候会走child的`dispatchTouchEvent`和`ontouchEvent`方法
*   如果child的`dispatchTouchEvent`方法返回false或者child的`ontouchEvent`返回false，`mFirstTouchTarget`和`newTouchTarget`都为空，因此在`action_move`和`action_up`的时候不走child的`dispatchTouchEvent`和`ontouchEvent`方法。
*   如果点击的是viewgroup，那么viewgroup的`onInterceptTouchEvent`的action_move和action_up都不会被执行。

`这里会问到事件冲突的问题？`

**事件遵循一个原则，就是看他有没有事件消费。比如一个LinearLayout里面有一个Button，点击LinearLayout会触发到Button吗，这里就看LinearLayout有没有设置点击事件，如果有就不会传递到Button，如果没有就会传递给Button。**

![](https://upload-images.jianshu.io/upload_images/2528336-128d691a81e057b7?imageMogr2/auto-orient/strip|imageView2/2/w/967/format/webp)

### Android性能优化、内存优化

`性能优化`:可以从界面、apk瘦身、混淆说起，dex分包处理，插件化动态加载模块，开屏冷启动说起

`界面优化`：多可以使用include、merge、ViewStub、约束布局来做起，include可以提取公共的布局，merge可以减少布局层次、ViewStub是使用的时候才去创建View，减少空间的占用、约束布局一来可以减少布局的层次、二来可以提高开发的效率，在自定义view中注意view绘制过程不要做初始化的操作，一般放到view的初始化的方法里面。

`apk瘦身`：可以用android studio的lint检测工具检测资源文件等

`混淆`：可以起到文件大小减少的作用，这个在实践中可以尝试，混淆后可以反编译看看apk包的内容

`dex分包`：主要是apk包的结构发生了变化，如果dex包的方法数超过了最大数，需要进行分包处理

`插件化`：主要用到了java中动态代理模式和反射的思想，利用android的activity启动流程，通过动态代理模式动态加载我们需要插件化的activity

`开屏冷启动`：开屏冷启动主要针对MultiDex启动做优化，在5.0之前对dex分包是不做处理的，所以要兼容到低版本的时候需要使用MultiDex.install做兼容。而MutiDex.install将apk中的dex包获取到，然后又压缩成对应的zip文件，将dex文件通过反射转换成DexFile对象、反射替换数组。所以我们能做的优化可以通过判断如果jvm不支持dex分包处理，通过MutiDex.install做处理，通过监听MutiDex.install开启一个监听MutiDex.install的进程activity。等到MutiDex.install处理完成后，再来处理正常的逻辑。

### 内存优化

内存优化通常指的内存溢出，主要涉及到的问题还是该释放的资源，没有及时让GC处理器回收，通常主要表现是动画、上下文对象、EventBus、AsycTask、Handler、单例Bitmap都会影响，通常要做的是释放他们未终止的动作，释放锁定的上下文对象。

**在实际项目有mvp架构的时候，需要注意内存泄漏的问题，p层如果长期持有v层的实例，导致v层的对象难以回收，而v层一般是activity或fragment作为抽象，因此需要在p层使用v层的弱应用或是在p层中实现v层的销毁方法，处理销毁的逻辑。**

### View的绘制

`activity界面显示流程`:activity启动后，不会立马去显示界面上的view，而是等到onResume的时候才会真正显示view的时机，首先会触发windowManager.addView方法，在该方法中触发代理对象WindowManagerGlobal的addView方法，代理对象的addView方法中创建了viewRootImpl，将setContentView中创建的decorView通过viewRootImpl的setView方法放到了viewRootImpl中，最终经过viewRootImpl一系列的方法最终调用performTraversals方法。

`view的绘制`:主要指view的onMeasure、onLayout、onDraw几个方法，其实要了解几个方法，需要追溯到android中本身界面的结构，首先整体是一个PhoneWindow的对象，然后是一个DecorView，DecorView里面包括一个ViewStub的ToolBar，然后下面是一个FramLayout，也就是我们经常在Activity中setContentView中的content内容。说完了android界面的结构，下面就是说下如何绘制的，绘制首先是触发到DecorView的onMeasure方法，它的测量规则包含了手机屏的宽高，并且测量模式是MeasureSpec.EXACTLY。所以这里明白了DecorView(FrameLayout)的测量参数是什么意思了，紧接着就是测量它下面的ViewGroup了，其中ViewGroup里面有个measureChild方法去测量孩子，这里会问到几种父布局的测量模式和子View的测量模式组合:

| ViewGroup的测量mode | MeasureSpec.EXACTLY | MeasureSpec.AT_MOST | MeasureSpec.UNSPECIFIED |
| --- | --- | --- | --- |
| childDimension>0 | size=childDimension;mode=EXACTLY | size= childDimension;mode=EXACTLY | size= childDimension;mode=EXACTLY |
| childDimension == LayoutParams.MATCH_PARENT | size=Viewgroup的size;mode=EXACTLY | size=Viewgroup的size;mode=AT_MOST | size=Viewgroup的size;mode=UNSPECIFIED |
| childDimension == LayoutParams.WRAP_CONTENT | size=Viewgroup的size;mode=AT_MOST | size=Viewgroup的size;mode=AT_MOST | size=Viewgroup的size;mode=UNSPECIFIED |

测量处理完了之后，紧接着就是View的onLayout，其中onLayout的作用是给View固定好位置，该方法传进来的几个参数是相对于自己的parent的位置，左上角是(0,0)的坐标。最后就是我们的onDraw，该方法是我们需要在画布上画东西的方法，一般包括画背景、画图层等等。

### Eventbus原理

`EventBus是一款在android开发中使用的发布/订阅事件的总线框架，基于观察者模式，将事件的接收者和发送者分开，基本包括了如下几个步骤:`

`注册事件的订阅方法`:该步骤主要是找到订阅者下面有哪些方法需要被订阅
`订阅操作`:将需要被订阅的方法放到类似HashMap的数据结构中存储起来，方便后面发送事件和取消注册等资源的释放的时候使用
`发送事件`:该步骤首先遍历事件队列，然后从队列中取出事件，并且将事件从队列中移除，拿到事件后，判断事件处于的什么线程，如果是非UI线程，则需要Handler去处理，如果是的话，则直接通过反射调用被观察的方法。
`反注册`:该步骤就没什么好说的，主要是上面存储到HashMap中的被订阅的方法的移除，释放在内存中的资源。

**还有面试以及技术方面的大量学习视频，快来获取学习资料提升自己去挑战一下BAT面试难关吧！**

**点击:**

[Android 学习，面试文档，视频收集大整理](https://links.jianshu.com/go?to=https%3A%2F%2Fshimo.im%2Fdocs%2FVxOufNNdaD4WS61R)

![](https://upload-images.jianshu.io/upload_images/15233854-8f7660192728ac5f.png?imageMogr2/auto-orient/strip|imageView2/2/w/835/format/webp)

### Rxjava的操作符有哪些，说说他们的作用

`just`:将同种数据源组合放到被观察者上面

`from`:将类似数组、集合的数据源放到被观察者上面

`map`:将一种数据源，转化成另外一种

`flatmap`:将一种数据源，转化成另外一种数据，并且被转化的数据是乱序排列的

`concatmap`:将一种数据源，转化成另外一种数据，并且被转化的数据是按照先前的数据源顺序排序的

`toList`:将数组的形式转化成List集合

`subscribeOn`:设置Observable的call方法所在的线程，也就是数据来源的线程

`observeOn`:设置subscribe的call方法所在的线程，也就是数据处理的线程

`filter`:在被观察者的数据层过滤数据

`onErrorResumeNext`:出错的时候，可以指定出错的时候的被观察者

`retryWhen`:出错的时候，重新走一遍被订阅的过程

`concat`:合并相同类型的被观察者到一个被观察者身上，有点类似集合、数组拼接数据。

`zip`:处理多种不同结果集的数据发射，一般用得多的地方是多个网络请求组合然后统一处理业务逻辑。
还有很多操作符就自己去看，这些操作符已经够面试用的了。

### 线程锁 锁方法和类对象啥的有啥区别

`线程锁锁方法`:是需要等到该线程用完了该方法才能释放同步锁
`线程锁锁类对象`:是需要等到该线程用完了该类对象才能释放同步锁
`区别`:是锁方法的区域要小 锁类对象包括了该类的所有属性

### AsyncTask原理

AsyncTask主要是对android中java的线程池的封装，该类中默认开启了两个线程池，一个线程池负责任务的排队处理，保证任务被单个处理，另外一个线程池用来专门处理任务，最后任务处理完了，交给Handler发送消息到主线程，然后Handler处理线程，交给了`onPostExecute`方法。

### 说说MVP和MVVM的特点

`MVP`:主要是分离了M层和V层的代码，通过P层来建立他们的关联，实现M层和V层的解耦。缺点就是每增加一个功能，需要增加相应的接口回调。没办法，MVP的核心就是通过接口实现隔离，将相关的业务层交给了P层。

如果要细说mvp需要注意几点:

*   p层的逻辑处理单一的功能，不要融合一个模块下的增删改查的整个功能。
*   由于p层持有了v层的引用，通常在p层使用弱引用来持有view层实例，在p层销毁的时候需要将v层的引用销毁掉。
*   契合类指的p层和v层的接口类放在一个contract接口类中，契合类方便管理业务层的功能，将单个功能放到一个contract契合类中。比如我们有一个添加书架的功能：

```
public interface AddBookShelfContract {
    interface View extends BaseContract.BaseView {
        void addBookShelfSuccess(BookShelfItem... bookShelfItem);

        void addBookShelfFail();

        void alreadyBookShelf(BookShelfItem bookShelfItem);
    }

    interface Presenter extends BaseContract.BasePresenter<View> {
        void addBookShelf(String tokenId, BookShelfItem... bookShelfItem);
    }
}

```

`MVVM`:主要是用到了观察者模式，通过数据的改变来通知相应的View改变的过程。M层和上面的MVP中的M层是一样的，都是网络请求+数据缓存来实现该层的，里面的双V，一个指的ViewModel实现的，另外一个AndroidDataBinding实现V层，ViewModel层获取到M层的数据后，通过观察者模式通知AndroidDataBinding在UI上的改变。缺点的话，只能吐糟下AndroidDataBinding了，在xml中写逻辑的时候，一点提示代码都没有，感觉完全是在写js似的，可读性肯定对于初级的来说还是有点难看懂的。

### Android中用到的观察者模式有哪些地方

观察者模式是由一个发送者（发送者是笔者自己的称呼，觉较之被观察者贴切得多）和一个观察者构成的、发送者在状态改变时（用户操作、程序主动改变等）主动通知所有观察者作相应的刷新。
android中最经典要说ListView的数据源发生变化了，刷新列表的事例。在setAdapter的时候，生成一个`AdapterDataSetObserver`，紧接着就是订阅上该观察者，该观察者`onChange`方法里面有`requestLayout`方法，该方法是触发UI发生变化的方法。在`BaseAdapter`里面可以看到`notifyDataSetChanged`实际上触发的是`DataSetObservable`被观察者的`notifyChanged`方法，`notifyChanged`会触发`AdapterDataSetObserver`的`onChange`方法。所以最终会走listView的`requestLayout`，最后刷新了UI。

### 说说google新出的Lifecycle框架

将类的生命周期方法移交到Lifecycle中管理，实现对类的生命周期的监听，从而在Lifecycle中处理生命周期的逻辑代码。这里涉及到几个对象:

`LifecycleObserver接口`（ Lifecycle观察者）：实现该接口的类，通过注解的方式，可以通过被LifecycleOwner类的addObserver(LifecycleObserver o)方法注册,被注册后，LifecycleObserver便可以观察到LifecycleOwner的生命周期事件。

`LifecycleOwner接口`（Lifecycle持有者）：实现该接口的类持有生命周期(Lifecycle对象)，该接口的生命周期(Lifecycle对象)的改变会被其注册的观察者LifecycleObserver观察到并触发其对应的事件。

`Lifecycle`(生命周期)：和LifecycleOwner不同的是，LifecycleOwner本身持有Lifecycle对象，LifecycleOwner通过其Lifecycle getLifecycle()的接口获取内部Lifecycle对象。

`State`(当前生命周期所处状态)：几种事件状态。

`Event`(当前生命周期改变对应的事件)：当Lifecycle发生改变，事件状态的回调event。

### okhttp原理

okhttp主要实现了异步、同步的网络操作，创建了不同的`call`对象，这里的call对象是一个个的runnable对象，由于我们的任务是很多的，因此这里有`Dispatcher`包装了线程池来处理不同的`call`，其中该类中创建了三种队列，分别用于存放正在执行的异步任务，同步队列，以及准备的队列。最后在执行每个任务的时候，采用队列的先进先出原则，处理每一个任务，都是交给了后面的各种拦截器来处理，有请求准备的拦截器、缓存拦截器、网络连接的拦截器，每一个拦截器组成了一个责任链的形式。到最后返回`response`信息。

**OkHttp的底层是通过Java的Socket发送HTTP请求与接受响应的(这也好理解，HTTP就是基于TCP协议的)，但是OkHttp实现了连接池的概念，即对于同一主机的多个请求，其实可以公用一个Socket连接，而不是每次发送完HTTP请求就关闭底层的Socket，这样就实现了连接池的概念。而OkHttp对Socket的读写操作使用的OkIo库进行了一层封装。**

### Retrofit原理

retrofit基于okHttp封装成RESTFUL网络请求框架，通过工厂模式配置各种参数，通过动态代理、注解实现网络请求。retrofit利用了工厂模式，将分为生产网络请求执行器(callFactory)、回调方法执行器(callbackExecutor)、网络请求适配器(CallAdapterFactory)、数据转换器(converterFactory)等几种工厂。
callFactory负责生产okHttp的call，大家都知道okHttp通过生成call对象完成同步和异步的http请求。

callbackExecutor通过判断不同的平台，生成对应平台的数据回调执行器。其中android端的回调执行器是通过handler回调数据。

CallAdapterFactory是数据解析工厂，一般我们配置json的数据解析适配器就行。

converterFactory是数据转换的工厂，一般我们配置Rxjava的数据转换就行。

retrofit通过动态代理模式实现接口类配置的注解、参数解析成HTTP对象，最后通过okHttp实现网络请求。

### RecyclerView源码、缓存分析

RecyclerView使用了强大的分工操作，显示、排版由LayoutManager处理，数据显示由adapter处理，item上下左右动态加入绘制由ItemDecoration处理，item的动画由ItemAnimator处理。面试主要分析recyclerView缓存，recyclerView缓存是由内部类Recycler维护，其中一级缓存有`mAttachedScrap`，里面放的都是当前屏幕正在显示的viewHolder的缓存，二级缓存是`mCachedViews`，里面放的都是移出到屏幕外的viewHolder缓存，`mRecyclerPool`是recyclerView的三级缓存，一般用在RecyclerView嵌套RecyclerView的时候用得到，比如外层的RecyclerView的item中有RecyclerView，那么里面的RecyclerView通过共用外层的RecyclerView的RecyclerPool来减少里面RecyclerView的ViewHolder创建。

### Binder机制

binder机制是android端进程间通信的基石，采用aidl的ipc通信方式，我们可以利用它来定义两个进程相互通信的接口。他是基于Service实现的一种线程间通信机制。它的本质是C/S架构的，需要一个服务器端，一个客户端。
AIDL通信方式里面有四个对象，一个是IInterface，专门用来负责接口的调度，Stub用来负责通信的响应和发送给service端的数据，Proxy负责两个进程通信的包装，算是间接调用Stub的包装类，service是服务端处理数据的关键类。用一张图来表示如下:

![](https://upload-images.jianshu.io/upload_images/2528336-4eec83304bfd817e?imageMogr2/auto-orient/strip|imageView2/2/w/732/format/webp)

### Android Jetpack

Android jetpack是google专门为开发者快速开发app的一套组件，快速搭建mvvm框架的实现，其中包括Lifecyle、LiveData、ViewModel、Room、DadaBinding、Navigation、Paging、WorkManager等一系列优秀的框架。

**Lifecycle**：实现和activity、fragment生命周期感知的框架，实现数据层和view层销毁的时候解绑。原理是Lifecycler为每个活动组件添加了一个没有界面的Fragment，利用Fragment周期会根据活动声明周期变化的特性实现的特性，从而实现生命周期的感知，然后根据注解的Event查找执行相应的方法。

**LiveData**：提供了一种数据改变的同时，主动去告诉ui，让ui层做出相应的逻辑判断。原理是内部保存了LifecycleOwner和Observer，利用LifecycleOwner感知并处理声明中期的变化，Observer在数据改变时遍历所有观察者并回调方法。

**ViewModel**：它是我们view层和model层的桥梁，是数据驱动界面的关键地方，也是我们ui层在数据丢失的情况下，viewModel还能继续保持原有的数据，原理是将数据保存到ViewModel中，然后为活动中添加一个HolderFragment，HolderFragment中保存了ViewStore的实例，ViewStore中使用Map保存了ViewModel，从而在活动重新创建时获取到原来的ViewModel。

**Room**：是model层本地数据库的框架，通过实体映射到对应的db表结构，将实体映射到db关系型数据库里面。跟greendao差不多，room数据库版本升级数据迁移比greendao迁移要麻烦，个人还是比较喜欢greendao来实现本地数据库。

**DadaBinding**：是一个可以通过在xml布局文件中实现ui逻辑的框架，并且它的ui层和数据层双向驱动还是挺不错的。

**Navigation**：是后面新出来的可视化管理fragment的组件，通过在xml中配置fragment之间跳转的关系。

### Kotlin

kotlin使用了一组新的语法糖，kotlin不让变量初始化空的类型，强大的非空设计思想比java要人性化，在代码编写阶段就有提示开发者的好处。还有它的内联函数，函数体作为返回值的各种简写方式使更多的人愿意接受kotlin。它的协成开发比较好的控制线程之间切换的多层嵌套的问题，以及它简洁的语法，比较受开发者青睐。

好了，大概Android面试的就写这么多。如果大家觉得还有那些要补上可以留言我!!!

## 文末

> 面试：如果不准备充分的面试，完全是浪费时间，更是对自己的不负责！
> 
> 金九银十面试季，赶快去为自己的面试做足准备吧！

最后不管怎么样，不论是什么样的大小面试，要想不被面试官虐的不要不要的，只有刷爆面试题题做好全面的准备，当然除了这个还需要在平时把自己的基础打扎实，这样不论面试官怎么样一个知识点里往死里凿，你也能应付如流啊~

**快来获取学习资料提升自己去挑战一下BAT面试难关吧！**

[Android 学习，面试文档，视频收集大整理](https://links.jianshu.com/go?to=https%3A%2F%2Fshimo.im%2Fdocs%2FVxOufNNdaD4WS61R)

![](https://upload-images.jianshu.io/upload_images/15233854-e9a09a508f39bd5e.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

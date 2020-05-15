![](https://upload-images.jianshu.io/upload_images/15233854-9b903b0c37393abb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##正文
Handler机制是Android中相当经典的异步消息机制，在Android发展的历史长河中扮演着很重要的角色，无论是我们直接面对的应用层还是FrameWork层，使用的场景还是相当的多。分析源码一探究竟。从一个常见的用法说起：
```
private Button mBtnTest;
private Handler mTestHandler = new Handler(){
   @Override
   public void handleMessage(Message msg) {
       switch (msg.what){
           case 1:
               mBtnTest.setText("收到消息1");
       }
   }
};
@Override
protected void onCreate(final Bundle savedInstanceState) {
   super.onCreate(savedInstanceState);
   setContentView(R.layout.activity_main);
   mBtnTest = (Button) findViewById(R.id.btn_test);
   new Thread(new Runnable() {
       @Override
       public void run() {
           try {
               Thread.sleep(3000);
               mTestHandler.sendEmptyMessage(1);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
       }
   }).start();
}
```
在对某件实物进一步了解之前，我们要先对该事物的价值意义有一个理解，即他是做什么的，再明白事物产生或发生时做了什么，结束时又会有什么样的结果。我们要讨论研究的是这个过程到底经历了什么，是发生什么因，再经历什么产生这个果。

当调用Handler发送消息相关方法时，会把这个消息发送到哪儿去？从上面的示例代码中可以看到消息最终还是会回到Handler手上，由他自己处理。我们要搞清楚的就是这个消息由发到收的过程。

####消息会发送到哪儿去？

>mTestHandler.sendEmptyMessage(1);

我们追随sendEmptyMessage()方法下去：

Handler无论以何种方式发送何种消息,都会经过到sendMessageAtTime()方法：
```
public boolean sendMessageAtTime(Message msg, long uptimeMillis) {
   MessageQueue queue = mQueue;
   if (queue == null) {
       RuntimeException e = new RuntimeException(
               this + " sendMessageAtTime() called with no mQueue");
       Log.w("Looper", e.getMessage(), e);
       return false;
   }
   return enqueueMessage(queue, msg, uptimeMillis);
}
```
而此方法会先判断当前Handler的mQueue对象是否为空，再调用enqueueMessage()方法，从字面意思不难理解是将该消息入队保存起来。再看enqueueMessage()方法：
```
private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
   msg.target = this;
   if (mAsynchronous) {
       msg.setAsynchronous(true);
   }
   return queue.enqueueMessage(msg, uptimeMillis);
}

public final class Message implements Parcelable {
   //....
   Handler target;
}
```
该方法会先将Message和当前Handler绑定起来，不难理解当需要处理Message时直接甩给绑定他的Handler就是了。再调用queue.enqueueMessage()方法正式入队，而queue对象到底是一个什么样的对象？由单向链表实现的消息队列。queue.enqueueMessage()方法就是遍历链表将消息插入表尾保存起来，而从queue取消息就是把表头的Message拿出来。

接着来搞清楚queue他是何时怎样创建的？来看Handler的构造函数。
```
public Handler(Callback callback, boolean async) {
   mLooper = Looper.myLooper();
   if (mLooper == null) {
       throw new RuntimeException(
           "Can't create handler inside thread that has not called Looper.prepare()");
   }
   mQueue = mLooper.mQueue;
   mCallback = callback;
   mAsynchronous = async;
}
```
Handler的构造方法会先调用Looper.myLooper()方法看能不能获取一个Looper对象，如果获取不到程序就直接蹦了。再从该Looper对象中获取我们需要的消息队列。

Looper到底是一个怎样的对象，有这怎样的身份，在Handler机制中扮演这怎样的角色？来看myLooper()方法：
```
public static @Nullable Looper myLooper() {
   return sThreadLocal.get();
}
```
myLooper()方法会直接就从sThreadLocal对象中获取Looper，而sThreadLocal是一个ThreadLocal类对象，而ThreadLocal类说白了就是通过他存储的对象是线程私有的。

>static final ThreadLocal sThreadLocal = new ThreadLocal();

调用get()方法直接从ThreadLocal中获取Looper，接下来就得看是何时set()将Loooper对象保存到ThreadLocal中去的。Looper.prepare()方法：
```
private static void prepare(boolean quitAllowed) {
   if (sThreadLocal.get() != null) {
       throw new RuntimeException("Only one Looper may be created per thread");
   }
   sThreadLocal.set(new Looper(quitAllowed));
}

private Looper(boolean quitAllowed) {
   mQueue = new MessageQueue(quitAllowed);
   mThread = Thread.currentThread();
}
```
从这段源码可以看出，Looper不仅是线程私有的还是唯一不可替换。Looper对象创建时会初始化MessageQueue()对象，正是我们需要的队列。
之所以最上面的示例代码中我们并没有调用prepare()方法初始化Looper，程序也没有崩溃，那是因为在ActivityThread的Main方法中就已经初始化了Looper对象。
```
public final class ActivityThread {
   //......
   public static void main(String[] args) {
       Looper.prepareMainLooper();
   }
   //......
}
```
到此我们算是明白消息会发送到哪儿去了，现在就要知道的是怎么取出消息交给Handler处理的。

首先MessageQueue封装有完整的添加(入队)和获取/删除(出队)方法，MessageQueeue.next()方法将链表当中表头第一个消息取出。
```
Message next() {
   //..........
   for (;;) {
       if (nextPollTimeoutMillis != 0) {
           Binder.flushPendingCommands();
       }

       nativePollOnce(ptr, nextPollTimeoutMillis);

       synchronized (this) {
           final long now = SystemClock.uptimeMillis();
           Message prevMsg = null;
           Message msg = mMessages;
           if (msg != null && msg.target == null) {
               do {
                   prevMsg = msg;
                   msg = msg.next;
               } while (msg != null && !msg.isAsynchronous());
           }
           if (msg != null) {
               if (now < msg.when) {
                   nextPollTimeoutMillis = (int) Math.min(msg.when - now, Integer.MAX_VALUE);
               } else {
                   mBlocked = false;
                   if (prevMsg != null) {
                       prevMsg.next = msg.next;
                   } else {
                       mMessages = msg.next;
                   }
                   msg.next = null;
                   if (DEBUG) Log.v(TAG, "Returning message: " + msg);
                   msg.markInUse();
                   return msg;
               }
           } else {
               nextPollTimeoutMillis = -1;
           }

           if (mQuitting) {
               dispose();
               return null;
           }
           //............
       }
       //..............
   }
}
```
代码虽然比较多，我们从第三行和第39行开始说起。next()方法实际是一个死循环，会一直从当前队列中去取Message，即使当前队列没有消息可取，也不会跳出循环，会一直执行，直到能够从队列中取到消息next()方法才会执行结束。其次当Looper调用quit()方法，mQuitting变量为ture时会跳出死循环，next()方法返回null方法也会执行结束。上面提到在ActivityThread中的main()方法中会初始化Looper,其实在不久之后便会开始从队列中取消息。
```
public static void main(String[] args) {

   //......
   Looper.prepareMainLooper();

   ActivityThread thread = new ActivityThread();
   thread.attach(false);

   if (sMainThreadHandler == null) {
       sMainThreadHandler = thread.getHandler();
   }

   if (false) {
       Looper.myLooper().setMessageLogging(new
               LogPrinter(Log.DEBUG, "ActivityThread"));
   }

   Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);
   Looper.loop();

   throw new RuntimeException("Main thread loop unexpectedly exited");
}

调用Looper.loop()方法就会开始遍历取消息。

public static void loop() {
for (;;) {
   Message msg = queue.next(); // might block
   if (msg == null) {
       return;
   }

   final Printer logging = me.mLogging;
   if (logging != null) {
       logging.println(">>>>> Dispatching to " + msg.target + " " +
               msg.callback + ": " + msg.what);
   }

   final long slowDispatchThresholdMs = me.mSlowDispatchThresholdMs;

   final long traceTag = me.mTraceTag;
   if (traceTag != 0 && Trace.isTagEnabled(traceTag)) {
       Trace.traceBegin(traceTag, msg.target.getTraceName(msg));
   }
   final long start = (slowDispatchThresholdMs == 0) ? 0 : SystemClock.uptimeMillis();
   final long end;
   try {
       msg.target.dispatchMessage(msg);
       end = (slowDispatchThresholdMs == 0) ? 0 : SystemClock.uptimeMillis();
   } finally {
       if (traceTag != 0) {
           Trace.traceEnd(traceTag);
       }
   }
}
```
loop()方法中也是一个死循环，调用queue.nex()方法开始阻塞式取消息，只有手动让Looper停止，next()方法才会返回null。

取到消息后，调用dispatchMessage()方法把消息交由Handler处理。

>msg.target.dispatchMessage(msg);
```
public void dispatchMessage(Message msg) {
   if (msg.callback != null) {
       handleCallback(msg);
   } else {
       if (mCallback != null) {
           if (mCallback.handleMessage(msg)) {
               return;
           }
       }
       handleMessage(msg);
   }
}
```
不仅可以给Handler设置回调接口，Message也行。默认情况下会回调handleMessage()方法。

本以为说得差不多了，其实还有一个关键的问题。我们是在主线程中执行的loop()方法，死循环为什么没有造成Activity阻塞卡死？查阅资料Android中为什么主线程不会因为Looper.loop()里的死循环卡死后得知next()方法中会执行一个重要方法。

>nativePollOnce(ptr, nextPollTimeoutMillis);

大佬分析得很好，我就不多说了。提一点，我们发送的延时消息，会通过Message字段/变量when，将时长保存下来，延时也是通过这个方法做到的。
```
Message next() {

   final long now = SystemClock.uptimeMillis();

   if (msg != null) {
       if (now < msg.when) {
           // Next message is not ready.  Set a timeout to wake up when it is ready.
           nextPollTimeoutMillis = (int) Math.min(msg.when - now, Integer.MAX_VALUE);
       } else {
           //......
       }
   }
}
```
总结，Handler发送消息会将消息保存到Looper维护的消息队列MessageQueue中去，而Looper会死循环一直从队列中取消息，取到消息后会交由Message绑定的Handler回调处理。

##文末
面试涉及的技术点可不知这么一点，我们需要学习的还多着呢。其实不管怎么样，不论是什么样的大小面试，要想不被面试官虐的不要不要的，只有刷爆面试题题做好全面的准备，当然除了这个还需要在平时把自己的基础打扎实，这样不论面试官怎么样一个知识点里往死里凿，你也能应付如流啊~

**快来获取学习资料提升自己去挑战一下BAT面试难关吧！**

[Android 学习，面试文档，视频收集大整理](https://links.jianshu.com/go?to=https%3A%2F%2Fshimo.im%2Fdocs%2FVxOufNNdaD4WS61R)

![](https://upload-images.jianshu.io/upload_images/15233854-e9a09a508f39bd5e.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

>面试：如果不准备充分的面试，完全是浪费时间，更是对自己的不负责！
>
>金九银十面试季，赶快去为自己的面试做足准备吧！

最后，祝愿大家在金九银十中都能找到一份好的工作，拿到心仪的offer！

**如果你觉得还算有用的话，不妨把它们推荐给你的朋友~**

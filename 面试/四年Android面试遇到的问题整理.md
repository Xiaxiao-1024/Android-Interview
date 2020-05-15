以下问题的答案均是个人四年来学习实践中整理的，如有不同意见，欢迎斧正。
1.自定义Handler时如何避免内存泄漏

答案:

一般非静态内部类持有外部类的引用的情况下，造成外部类在使用完成后不能被系统回收内存，从而造成内存泄漏。为了避免这个问题，我们可以自定义的Handler声明为静态内部类形式，然后通过弱引用的方式，让Handler持有外部类的引用，从而可避免内存泄漏问题。

以下是代码实现

public class MainActivity extends AppCompatActivity {
    private static final String TAG = "MainActivity";
    private TextView mTextView;
 
    private WeakReference<MainActivity> activityWeakReference;
    private MyHandler myHandler;
 
    static class MyHandler extends Handler {
        private MainActivity activity;
 
        MyHandler(WeakReference<MainActivity> ref) {
            this.activity = ref.get();
        }
 
        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
            switch (msg.what) {
                case 1:
                    //需要做判空操作
                    if (activity != null) {
                        activity.mTextView.setText("new Value");
                    }
                    break;
                default:
                    Log.i(TAG, "handleMessage: default ");
                    break;
            }
 
 
        }
    }
 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        //在onCreate中初始化
        activityWeakReference = new WeakReference<MainActivity>(this);
        myHandler = new MyHandler(activityWeakReference);
 
        myHandler.sendEmptyMessage(1);
        mTextView = (TextView) findViewById(R.id.tv_test);
    }
}
参考博文http://blog.csdn.net/ucxiii/article/details/50972747

2.onNewIntent()的调用时机

解析:

在Android应用程序开发的时候，从一个Activity启动另一个Activity并传递一些数据到新的Activity上非常简单，但是当您需要让后台运行的Activity回到前台并传递一些数据可能就会存在一点点小问题。

首先，在默认情况下，当您通过Intent启到一个Activity的时候，就算已经存在一个相同的正在运行的Activity,系统都会创建一个新的Activity实例并显示出来。为了不让Activity实例化多次，我们需要通过在AndroidManifest.xml配置activity的加载方式（launchMode）以实现单任务模式，如下所示：

<activity android:label="@string/app_name" android:launchmode="singleTask"android:name="Activity1"></activity>
launchMode为singleTask的时候，通过Intent启到一个Activity,如果系统已经存在一个实例，系统就会将请求发送到这个实例上，但这个时候，系统就不会再调用通常情况下我们处理请求数据的onCreate方法，而是调用onNewIntent方法

答案:

前提:ActivityA已经启动过,处于当前应用的Activity堆栈中; 
当ActivityA的LaunchMode为SingleTop时，如果ActivityA在栈顶,且现在要再启动ActivityA，这时会调用onNewIntent()方法 
当ActivityA的LaunchMode为SingleInstance,SingleTask时,如果已经ActivityA已经在堆栈中，那么此时会调用onNewIntent()方法 

当ActivityA的LaunchMode为Standard时，由于每次启动ActivityA都是启动新的实例，和原来启动的没关系，所以不会调用原来ActivityA的onNewIntent方法，仍然调用的是onCreate方法

以下是代码实例

1.设置MainActivity的启动模式为SingleTask(栈内复用)

 <activity
            android:name=".MainActivity"
            android:launchMode="singleTask">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
 
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
2.MainActivity中重写onNewIntent方法

package code.xzy.com.handlerdemo;
 
import android.content.Intent;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;
 
public class MainActivity extends AppCompatActivity {
    private static final String TAG = "MainActivity";
    private Button mButton;
 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mButton = (Button) findViewById(R.id.forward_btn);
        mButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                startActivity(new Intent(MainActivity.this, Main2Activity.class));
            }
        });
 
    }
 
    @Override
    protected void onNewIntent(Intent intent) {
        Toast.makeText(this, "onnewIntent", Toast.LENGTH_SHORT).show();
        Log.i(TAG, "onNewIntent: i done....");
    }
}
3.Main2Actvity执行点击跳转，MainActivity被复用，执行onNewIntent方法

package code.xzy.com.handlerdemo;
 
import android.content.Intent;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.Button;
 
public class Main2Activity extends AppCompatActivity {
    private Button mButton;
 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main2);
 
        mButton = (Button)findViewById(R.id.btn);
 
        mButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                startActivity(new Intent(Main2Activity.this,MainActivity.class));
                finish();
            }
        });
    }
}
打印截图:



3.RecyclerView相比ListView有哪些优势

解析:

首先需要解释下RecyclerView的这个名字了，从它类名上看，RecyclerView代表的意义是，我只管Recycler View，也就是说RecyclerView只管回收与复用View，其他的你可以自己去设置。可以看出其高度的解耦，给予你充分的定制自由（所以你才可以轻松的通过这个控件实现ListView,GirdView，瀑布流等效果）

其次RecyclerView提供了添加、删除item的动画 效果，而且可以自定义

RecyclerView相比ListView优势在于可以轻松实现:

ListView的功能
GridView的功能
横向ListView的功能
横向ScrollView的功能
瀑布流效果
便于添加Item增加和移除动画

不过一个挺郁闷的地方就是，系统没有提供ClickListener和LongClickListener。 
不过我们也可以自己去添加，只是会多了些代码而已。 
实现的方式比较多，你可以通过mRecyclerView.addOnItemTouchListener去监听然后去判断手势， 

当然你也可以通过adapter中自己去提供回调

参考

http://jcodecraeer.com/a/anzhuokaifa/androidkaifa/2014/1118/2004.html

http://blog.csdn.net/lmj623565791/article/details/45059587

http://www.360doc.com/content/16/0808/14/9200790_581676933.shtml

4.谈一谈Proguard混淆技术

答案:

Proguard技术有如下功能:

压缩 --检查并移除代码中无用的类
优化--对字节码的优化，移除无用的字节码
混淆--混淆定义的名称，避免反编译

预监测--在java平台对处理后的代码再次进行检测

代码混淆只在上线时才会用到，debug模式下会关闭，是一种可选的技术。

那么为什么要使用代码混淆呢?

因为Java是一种跨平台的解释性开发语言，而java的源代码会被编译成字节码文件，存储在.class文件中，由于跨平台的需要，java的字节码中包含了很多源代码信息，诸如变量名、方法名等等。并且通过这些名称来访问变量和方法，这些变量很多是无意义的，但是又很容易反编译成java源代码，为了防止这种现象，我们就需要通过proguard来对java的字节码进行混淆，混淆就是对发布的程序进行重新组织和处理，使得处理后的代码与处理前的代码有相同的功能，和不同的代码展示，即使被反编译也很难读懂代码的含义，哪些混淆过的代码仍能按照之前的逻辑执行得到一样的结果。

但是，某些java类是不能被混淆的，比如实现了序列化的java类是不能被混淆的，否则反序列化时会出问题。

下面这类代码混淆的时候要注意保留，不能混淆。
　　Android系统组件，系统组件有固定的方法被系统调用。
　　被Android Resource 文件引用到的。名字已经固定，也不能混淆，比如自定义的View 。
　　Android Parcelable ，需要使用android 序列化的。
　　其他Anroid 官方建议 不混淆的，如
　　android.app.backup.BackupAgentHelper
　　android.preference.Preference
　　com.android.vending.licensing.ILicensingService
　　Java序列化方法，系统序列化需要固定的方法。
　　枚举 ，系统需要处理枚举的固定方法。
　　本地方法，不能修改本地方法名
　　annotations 注释
　　数据库驱动
　　有些resource 文件

　　用到反射的地方

5.ANR出现的场景及解决方案

在Android中，应用的响应性被活动管理器（Activity Manager）和窗口管理器（Window Manager）这两个系统服务所监视。当用户触发了输入事件（如键盘输入，点击按钮等）,如果应用5秒内没有响应用户的输入事件，那么，Android会认为该应用无响应，便弹出ANR对话框。而弹出ANR异常，也主要是为了提升用户体验。

解决方案是对于耗时的操作，比如访问网络、访问数据库等操作，需要开辟子线程，在子线程处理耗时的操作，主线程主要实现UI的操作

6.HTTPS中SSL证书认证的过程

7.简述Android的Activity的内部机制

8.对Android Framework层的某一个模块(或者System App)做简要介绍

9.Android Handler的机制和原理

主线程使用Handler的过程

首先在主线程创建一个Handler对象 ，并重写handleMessage（）方法。然后当在子线程中需要进行更新UI的操作，我们就创建一个Message对象，并通过handler发送这条消息出去。之后这条消息被加入到MessageQueue队列中等待被处理，通过Looper对象会一直尝试从Message Queue中取出待处理的消息，最后分发会Handler的handler Message（）方法中。



参考 http://blog.csdn.net/u012827296/article/details/51236614

10.线程间通信和进程间通信有什么不同，Android开发过程中是怎么实现的

https://www.cnblogs.com/yangtao1995/p/6079067.html

11.简述项目中对于内存优化的几个细节点

答案:

1.当查询完数据库之后，及时关闭Cursor对象。
2.记得在Activity的onPause方法中调用unregisterReceiver()方法，反注册广播
3.避免Content内存泄漏，比如在4.0.1之前的版本上不要讲Drawer对象置为static。当一个Drawable绑定到了View上，实际上这个View对象就会成为这个Drawable的一个callback成员变量，上面的例子中静态的sBackground持有TextView对象lable的引用，而lable只有Activity的引用，而Activity会持有其他更多对象的引用。sBackground生命周期要长于Activity。当屏幕旋转时，Activity无法被销毁，这样就产生了内存泄露问题。
4.尽量不要在Activity中使用非静态内部类，因为非静态内部类会隐式持有外部类实例的引用，当非静态内部类的引用的声明周期长于Activity的声明周期时，会导致Activity无法被GC正常回收掉。
5.谨慎使用线程Thread！！这条是很多人会犯的错误: Java中的Thread有一个特点就是她们都是直接被GC Root所引用，也就是说Dalvik虚拟机对所有被激活状态的线程都是持有强引用，导致GC永远都无法回收掉这些线程对象，除非线程被手动停止并置为null或者用户直接kill进程操作。所以当使用线程时，一定要考虑在Activity退出时，及时将线程也停止并释放掉
6.使用Handler时，要么是放在单独的类文件中，要么就是使用静态内部类。因为静态的内部类不会持有外部类的引用，所以不会导致外部类实例的内存泄露

12.简述Android的视图层级优化，简述自定义View或者自定义ViewGroup的步骤

个人的理解是，Android视图渲染必须经过measure、layout、draw三个步骤，measure过程是在一个树形结构中不断遍历的，如果UI层级嵌套很深，必将花费大量的时间，所以应该尽量减少层级嵌套，保证树的结构扁平，并移除不需要渲染的views

自定义view步骤:

Android自定义View的一般步骤

13.选取一个常用的第三方开源库，简述其接入步骤

Volley教程 http://blog.csdn.net/jdfkldjlkjdl/article/details/79074259

14.TCP和UPD的区别以及使用场景

TCP与UDP基本区别
  1.基于连接与无连接
  2.TCP要求系统资源较多，UDP较少； 
  3.UDP程序结构较简单 
  4.流模式（TCP）与数据报模式(UDP); 
  5.TCP保证数据正确性，UDP可能丢包 
  6.TCP保证数据顺序，UDP不保证 
　　
UDP应用场景：
  1.面向数据报方式
  2.网络数据大多为短消息 
  3.拥有大量Client
  4.对数据安全性无特殊要求
  5.网络负担非常重，但对响应速度要求高

15.简述一个设计模式的概念，并简要谈谈framework层哪些地方用到了什么设计模式

单例模式:单例模式是一种对象创建模式，它用于产生一个对象的具体实例，它可以确保系统中一个类只产生一个实例。

适配器模式:将一个接口转换成客户希望的另一个接口，适配器模式使接口不兼容的那些类可以一起工作，其别名为包装器(Wrapper)

装饰模式：动态地给一个对象增加一些额外的职责，就增加对象功能来说，装饰模式比生成子类实现更为灵活。装饰模式是一种对象结构型模式。

使用场景:
1.在不影响其他对象的情况下，以动态、透明的方式给单个对象添加职责。
2.当不能采用继承的方式对系统进行扩展或者采用继承不利于系统扩展和维护时可以使用装饰模式。
优点:
1.对于扩展一个对象的功能，装饰模式比继承更加灵活，不会导致类的个数急剧增加。
2.可以通过一种动态地方式来扩展一个对象的功能。
3.可以对一个对象进行多次装饰，通过使用不同的具体装饰类以及这些装饰类的排列组合。
实际运用:
Android中Context类的实现

外观模式:主要目的在于让外部减少与子系统内部多个模块的交互，从而让外部能够更简单得使用子系统。它负责把客户端的请求转发给子系统内部的各个模块进行处理。

使用场景:
1.当你要为一个复杂的子系统提供一个简单的接口时
2.客户程序与抽象类的实现部分之前存在着很大的依赖性
3.当你需要构建一个层次结构的子系统时

组合模式:将对象以树形结构组织起来，以达成”部分--整体”的层次结构，使得客户端对单个对象和组合对象的使用具有一致性。

使用场景:
1.需要表示一个对象整体或部分 层次
2.让客户端能够忽略不同对象层次的变化
优点:
1.高层模块调用简单
2.节点自由增加

模板方法：是通过一个算法骨架，而将算法中的步骤延迟到子类，这样子类就可以复写这些步骤的实现来实现特定的算法。它的使用场景:

1.多个子类有公有的方法，并且逻辑基本相同
2.重要、复杂的算法，可以把核心算法设计为模板方法

3.重构时，模板方法模式是一个经常使用的模式

观察者模式:定义对象之间一种一对多依赖关系，使得每当一个对象状态发生改变时，其相关依赖对象皆得到通知并被自动更新。其使用场景:

1.一个抽象模型有两个方面，其中一个方面依赖于另一个方面
2.一个对象的改变将导致一个或多个其他对象也 发生改变

3.需要在 系统中创建一个 触发链

具体应用:

比如回调模式中，实现了抽象类/接口的实例实现了父类提供的抽象方法后，将该方法交还给父类来处理
Listview中的notifyDataSetChanged

RxJava中的观察者模式

责任链模式:是一个请求有多个对象来处理，这些对象是一条链，但具体由哪个对象来处理，根据条件判断来确定，如果不能处理会传递给该链条中的下一个对象，直到有对象处理它为止。

使用场景:
1.有多个对象可以处理同一个请求，具体哪个对象处理该请求待运行时再确定
2.在不明确指定接收者的情况下，向多个对象中的一个提交一个请求。

实际运用:
Try...catch语句
OrderedBroadcast
MotionEvent:actionDwon actionMove actionUp
事件分发机制三个重要方法:dispatchTouchEvent. onInterceptTouchEvent. onTouchEvent

策略模式:定义一系列的算法，把它们一个个封装起来，并且使他们可互相替换。本模式使得算法可独立于使用它的客户而变化。策略模式的使用场景:一个类定义了多种行为，并且这些行为在这个类的方法中以多个条件语句的形式出现，那么可以使用策略模式避免在类中使用大量的条件语句。

使用场景:
一个类定义了多种行为，并且这些行为在这个类的方法中以多个条件语句的形式出现，那么可以使用策略模式避免在类中使用大量的条件语句。
优点:
1.上下文Context和具体策略ConcreateStrategy是松耦合关系
2.策略模式满足 开-闭原则
具体应用:
HttpStack

Volley框架



16.字节流和字符流的区别

字节流操作的基本单元为字节；字符流操作的基本单元为Unicode码元(2个字节)。
字节流默认不使用缓冲区；字符流使用缓冲区。

字节流通常用于处理二进制数据，实际上它可以处理任意类型的数据，但它不支持直接写入或读取Unicode码元；字符流通常处理文本数据，它支持写入及读取Unicode码元。

参考 理解Java中字符流与字节流的区别

17.View的绘制流程，是先测量父View还是先测量子View

View和ViewGroup的基本绘制流程

18.OOM异常是否可以被try...catch捕获(或者可否用try-catch捕获Out Of Memory Error以避免其发生？)

只有在一种情况下，这样做是可行的：在try语句中声明了很大的对象，导致OOM，并且可以确认OOM是由try语句中的对象声明导致的，那么在catch语句中，可以释放掉这些对象，解决OOM的问题，继续执行剩余语句。

但是这通常不是合适的做法。Java中管理内存除了显式地catch OOM之外还有更多有效的方法：比如SoftReference, WeakReference, 硬盘缓存等。在JVM用光内存之前，会多次触发GC，这些GC会降低程序运行的效率。如果OOM的原因不是try语句中的对象（比如内存泄漏），那么在catch语句中会继续抛出OOM

19.WeakReference和SoftReference的区别

Java的StrongReference, SoftReference, WeakReference, PhantomReference的区别

20.请计算一张100像素*100像素的图片所占用内存

http://blog.csdn.net/u010652002/article/details/72676723

21.okHttp实现的原理

22.okHttp有哪些拦截器

23.计算1+2！+3！+4！+5！+...+20!的结果，用代码实现

24.写出单例模式，哪些是线程安全的，为什么是线程安全的

25.Retrofit实现原理

26.android图片有哪些格式

答案:http://blog.csdn.net/xmc281141947/article/details/72768175

27.sqlite可以执行多线程操作吗?如何保证多线程操作数据库的安全性

答:

每当你需要使用数据库时，你需要使用DatabaseManager的openDatabase()方法来取得数据库，这个方法里面使用了单例模式，保证了数据库对象的唯一性，也就是每次操作数据库时所使用的sqlite对象都是一致得到。其次，我们会使用一个引用计数来判断是否要创建数据库对象。如果引用计数为1，则需要创建一个数据库，如果不为1，说明我们已经创建过了。
在closeDatabase()方法中我们同样通过判断引用计数的值，如果引用计数降为0，则说明我们需要close数据库。

 大致的做法就是在多线程访问的情况下需要自己来封装一个DatabaseManager来管理Sqlite数据库的读写，需要同步的同步，需要异步的异步，不要直接操作数据库，这样很容易出现因为锁的问题导致加锁后的操作失败。

该答案参考了这篇文章http://blog.csdn.net/rockcode_li/article/details/39024497

28.有两个长度已知的链表，怎样确定两个链表的交集

解析:leetcode 两个链表的交集点 http://www.360doc.com/content/16/0926/20/10408243_593854682.shtml

有以下几种思路：

（1） 暴力破解 ，遍历链表A的所有节点，并且对于每个节点，都与链表B中的所有节点比较，退出条件是在B中找到第一个相等的节点。时间复杂度O(lengthA*lengthB)，空间复杂度O(1)。

（2） 哈希表 。遍历链表A，并且将节点存储到哈希表中。接着遍历链表B，对于B中的每个节点，查找哈希表，如果在哈希表中找到了，说明是交集开始的那个节点。时间复杂度O(lengthA+lengthB)，空间复杂度O(lengthA)或O(lengthB)。

（3） 双指针法 ，指针pa、pb分别指向链表A和B的首节点。

遍历链表A，记录其长度lengthA，遍历链表B，记录其长度lengthB。

因为两个链表的长度可能不相同，比如题目所给的case，lengthA=5，lengthB=6，则作差得到 lengthB- lengthA=1，将指针pb从链表B的首节点开始走1步，即指向了第二个节点，pa指向链表A首节点，然后它们同时走，每次都走一步，当它们相等时，就是交集的节点。
#####结尾
喜欢的朋友可以收藏点个赞哦。欢迎转发

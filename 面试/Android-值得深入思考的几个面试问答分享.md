![](https://upload-images.jianshu.io/upload_images/15233854-89629e68ed7659af.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

金九银十面试季又要到来了，这里给大家不定期更新大厂面试真题，今天来分享一下Android面试中几个值得我们深入思考的面试题还有解析，希望可以帮助到即将面试的小伙伴们，祝面试顺利~

**文末还有大厂面试专题资料包免费分享~接下来是正文：**

####1. 事件分发机制大家应该都熟记于心，默认事件分发是逆序的，有哪些方法可以修改分发顺序？

>记得曾经有位朋友做贴纸应用时，有RT 的需求。
>
>默认事件分发为逆序，遍历子 View 为 (childCount ~ 0 ]，有哪些方式可以修改这一策略，比如修改遍历方式为[0,childCount)?

修改事件分发顺序的话，在日常开发中基本遇不到，因为现在的逆序遍历，是跟View的层级显示相匹配的，随便更改反而不太合理。

如果非要修改这个顺序，很多同学首先会想到：

**重写dispatchTouchEvent方法，然后在里面一个for循环，从0开始一个个调用子View的dispatchTouchEvent。**

这个方法，不是说绝对不行，只是你要做的事情很多，就比如**触摸坐标的转换：**

 我们都知道，ViewGroup在分派事件的时候，会**检查子View是否应用过属性动画的(位移、缩放、旋转等)，如果有的话还要把坐标给映射回去**。

 接着，还会把相对于这个ViewGroup本身的触摸坐标 转换成 相对于对应子View的触摸坐标。

这样说可能有点绕，举个例子：

**比如**：当手指在屏幕中按下，ViewGroup中收到的event坐标(getX,getY)假设是【500,500】，刚好在这个位置上有个子View，那接下来肯定会把事件传给这个子View的dispatchTouchEvent，这时候如果坐标不转换直接传的话，那子View收到的event坐标(getX,getY)也是【500,500】，这明显是不对的，正确的坐标应该要分别减去它的left和top。

 这看起来好像没什么大的影响，但如果你的子View没有重写onTouchEvent方法的话（比如子View是常用的ImageView，TextView之类的），你的OnClickListener就会无效了，因为默认的onTouchEvent在处理ACTION_MOVE的时候，会检查event的坐标是否已经脱离了View的边界范围，如果在边界范围之外的话，pressed将会失效（认为没有被按下），当ACTION_UP时，如果pressed为false，就不会执行PerformClick。

**那难道没有方法可以完美地做到了吗？**

 在ViewGroup的dispatchTouchEvent方法中，虽然它是逆序的for，但是呢，它把子View拿出来的时候，却不是直接操作的mChildren数组，而是通过一个getAndVerifyPreorderedView方法来获得，这个方法会把当前索引传进去，还有一个preorderedList。

```
@Override
public boolean dispatchTouchEvent(MotionEvent ev) {
     // ...   
    final ArrayList<View> preorderedList = buildTouchDispatchChildList();
    final boolean customOrder = preorderedList == null
            && isChildrenDrawingOrderEnabled();
    final View[] children = mChildren;
    for (int i = childrenCount - 1; i >= 0; i--) {
        final int childIndex = getAndVerifyPreorderedIndex(
                childrenCount, i, customOrder);
        final View child = getAndVerifyPreorderedView(
                preorderedList, children, childIndex);
    ...
}
```

如果传进去的preorderedList不为空，那么就会直接从它里面去取。

####**preorderedList怎么来？**

 通过调用buildOrderedChildList方法获取的。

#### **buildOrderedChildList方法是怎么样的？**

```
ArrayList<View> buildOrderedChildList() {
        final int childrenCount = mChildrenCount;
        if (childrenCount <= 1 || !hasChildWithZ()) return null;

        if (mPreSortedChildren == null) {
            mPreSortedChildren = new ArrayList<>(childrenCount);
        } else {
            // callers should clear, so clear shouldn't be necessary, but for safety...
            mPreSortedChildren.clear();
            mPreSortedChildren.ensureCapacity(childrenCount);
        }

        final boolean customOrder = isChildrenDrawingOrderEnabled();
        for (int i = 0; i < childrenCount; i++) {
            // add next child (in child order) to end of list
            final int childIndex = getAndVerifyPreorderedIndex(childrenCount, i, customOrder);
            final View nextChild = mChildren[childIndex];
            final float currentZ = nextChild.getZ();

            // insert ahead of any Views with greater Z
            int insertIndex = i;
            while (insertIndex > 0 && mPreSortedChildren.get(insertIndex - 1).getZ() > currentZ) {
                insertIndex--;
            }
            mPreSortedChildren.add(insertIndex, nextChild);
        }
        return mPreSortedChildren;
    }
```

它里面是通过一个getAndVerifyPreorderedIndex方法来获取对应的子VIew索引，这个方法要传进去一个叫customOrder的boolean。

 这个customOrder，看名字可以知道，是自定义顺序的意思，如果它为true的话，接着会通过getChildDrawingOrder(int childCount, int i)方法来获取对应的索引，而且，这个方法是protected的，所以我们可以通过重写这个方法并根据参数"i"来决定返回哪一个View所对应的索引，从而改变分发的顺序。

```
protected int getChildDrawingOrder(int childCount, int i) {
        return i;
    }
```

**那这个customOrder，什么时候为true呢？**

 在buildOrderedChildList方法里可以看到这么一句：

```
final boolean customOrder = isChildrenDrawingOrderEnabled();
```

emmmm，也就是说，如果要自定义这个顺序的话，还需要调用setChildrenDrawingOrderEnabled(true)来开启。

 重新捋一捋流程：

**1\. setChildrenDrawingOrderEnabled(true)来开启自定义顺序；**
**2\. 重写getChildDrawingOrder方法来决定什么时候要返回哪个子View；**

###**2. AppCompatTextView 与 TextView 有什么区别？**

1\. compat库是如何将TextView替换为AppCompatTextVew的？
2\. 为什么要进行替换？
3\. 根据替换相关原理，我们可以做哪些事情？

####**先从第二问开始吧:**

AppCompatTextView继承自TextView，是对TextView的一种扩展，因为在5.0中首次推出了MaterialDesign这种设计风格。

但是众所周知的，5.0推出不可能所有的设备全都一下子更新到最新版本，为了在早期版本上实现新的功能（这些新功能比如从源码注释中解读到比如backgroundTint属性，根据文本内容自适应大小等）.

**即为了新特性同样可以兼容老版本，framework在创建TextView实例的时候，自动帮我们进行了替换。**

其它的AppCompatXXX与XXX的关系也是如此。

####**第一问：**

然后第一问，如何完成替换的，我们这里只拿最直观的流程举例，且尽可能的简化源码过程，在讨论这个问题之前，先了解几个预备知识：

View是怎么被解析创建出来的：

**1.LayoutInflater：**将布局XML文件实例化为其对应的View对象，我们在Activity中通过setContentView传入一个Layout的资源文件id，最终该方法最终会调用到PhoneWindow的setContentView方法，这个方法里面有调用到

```
mLayoutInflater.inflate(layoutResID, mContentParent);
```

**2.inflate方法**，该方法的作用是将指定的XML文件填充到View的层次结构中去，最终无论通过什么途径调用到inflate方法，都会走到三个参数的重载方法这里：

```
return inflate(parser, root, attachToRoot);
```

parser你可以认为持有将Layout.XML解析后的数据。**后两个参数的意义如下：**

1\. root为null，attchToRoot无意义，inflate返回的是当前XML对应的根布局。
2\. root不为null且attachToRoot为true，则整个XML对应的布局就设置了根布局是root。
3\. root不为null且attachToRoot为false，则会将root的layoutParames设置给当前XML的布局。

知道了LayoutInflate.inflate做了什么，再往下，inflate中会调用createViewFromTag，从方法名就能知道，继续往下走，我们离答案越来越近了。

createViewFromTag做的事情非常有意思：

![](https://upload-images.jianshu.io/upload_images/15233854-f72c28e7ff3872ab?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

先看到787行这个if-else，条件是name中有没有"."字符，如果有我们会执行onCreateView，如果没有会执行createView。

####**name啥时候有点？**

自定义控件的时候。

当是系统控件的时候，createView会有一个填充了第二个参数的调用：

createView(name, "android.view.", attrs);补上了View控件的全路径名，而自定义控件则不需要，因为传入的name就是一个全路径名。

####**为什么要全路径名？**

因为View控件对象的创建是通过反射来实现的：

```
clazz = mContext.getClassLoader().loadClass(
    prefix != null ? (prefix + name) : name).asSubclass(View.class);
...
constructor = clazz.getConstructor(mConstructorSignature);
constructor.setAccessible(true);
sConstructorMap.put(name, constructor);
// ...
args[1] = attrs;
final View view = constructor.newInstance(args);
```

下面对这几步做一个总结：

XML中保存了ViewTree的结构和View的相关标签信息（包括View的类型和一些属性值），然后这些信息会在后面通过反射的方式（如果没有Factory2和Factory的话）创建实例对象，如果创建的是ViewGroup，则会对它的子View遍历重复创建步骤，创建完View对象后，会add到对应的ViewGroup中。

其中相关方法的调用流程是：

**inflate->rInflate->createViewFromTag->createView。**

**好像还是没有看到替换？**

还是上一张图，我们只解释了后半部分，没有解释前半部分，**那么什么是Factory?**

继续往下看：

createViewFromTag中会先判断有没有**Factory**或者**Factory2**的对象，如果有，则调用Factory的onCreateView方法。

这两个类都是接口，其中Factory2是Factory的子接口，都只有唯一一个onCreateView方法。

不同之处在于Factory2的onCreateView方法传入了parentView。

 该方法的作用就是你可以借助它来改造XML中已经存在了的Tag的值。所以Factory2可以达到改造parentView的目的。

**但是我们在日常中根本就没有任何地方接触到了Factory（2）呀，那么它是不是就直接是null呢？**

到这里又是一番源码调来调去，为了便于理解，只需要知道，这个东西(Factory2)，在最开始AppCompatActivity（为了兼容低版本，我们现在Activity默认都是继承自它）中的onCreate方法中就已经通过层层调用被设置好了。

既然现在Factory2不为空，那么就应该去走它的onCreateView方法了，这里又是层层调用，最终来到了**AppCompatViewInflater**** 的 createView 方法：**

答案就在这里：

![](https://upload-images.jianshu.io/upload_images/15233854-1dcaff9d3d55dd85?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果创建的是非兼容控件（系统控件那么多，实现兼容的只是常用的一些控件），那么就会是143行，在146中通过反射创建View对象。

啰里啰唆扯了一大堆，还是没回答第一个问题：

####compat库是如何将TextView替换为AppCompatTextVew的？

> 个人对这个的理解：在将XML文件解析成包含ViewTree信息之后，开始利用这些信息去创建每一个View节点，在创建View对象的时候，如果发现这个节点是属于支持兼容的控件比如TextView，那么就会去调用到new AppCompatTextView()来创建一个兼容的View对象，也就是在创建的时候，及已经实现了替换。

#### **第三问：**

根据替换相关原理，我们可以做哪些事情？

整个替换从图一所示的源码中可以看到，能够被替换的关键是Factory（2）存在，那么我觉得，其实问题问的是Factory（2）可以用来做什么吧？

那么这个时候，就适合去问站长大人了：

*   [探究 LayoutInflater setFactory](http://mp.weixin.qq.com/s?__biz=MzAxMTI4MTkwNQ==&mid=2650820263&idx=1&sn=f7cbcf70570decc064fa5f05531e2c5d&chksm=80b79a39b7c0132f80e0d41c49c593ef464f4d3309c8a4084a229b92e00c4e004caf3db21f0b&scene=21#wechat_redirect)

###**3. getWidth, getMeasuredWidth 有什么区别?**

**getWidth和getMeasuredWidth的区别：**

getMeasuredWidth方法返回的是测量后的宽度，这个宽度是当setMeasuredDimension方法(**measure方法最终会调用setMeasuredDimension)被调用后刷新的**。

 而getWidth返回的是最终layout出来的宽度，在View代码中返回的是【mRight - mLeft】，这个mRight和mLeft，是在setFrame方法被调用后赋值的**(layout方法最终会调用setFrame**)。

也就是说，**getMeasuredWidth返回值的大小，取决于setMeasuredDimension，而getWidth，则取决于layout。**

**传说中一个是 View 宽度，一个是 View 中的内容宽度，这个解答对吗？**

在常规的View中，比如TextView，ImageView这些，如果没有明确指定宽度的话，那么他们的getMeasuredWidth返回的宽度，确实就是实际内容的宽度。

 但如果在xml布局里或自定义View中故意把宽度设置的很大，或者很小，比如设置宽度为9999999，这种情况就不算了。

 所以我的回答是：如果这个View和它所在的ViewGroup(在ViewGroup中的onMeasure也可做手脚)，都遵守规矩的话，那么这句话就是对的。

###**4.butterknife 中的黑科技**

很多时候大家在剖析butterknife源码的时候，更多的是讲解其中的apt等，在library中使用buttterknife的时候，会使用R2.id.xxx

```
class ExampleActivity extends Activity {
  @BindView(R2.id.user) EditText username;
  @BindView(R2.id.pass) EditText password;
...
}
```

而非R.id.xxx.

### 文末

好了，今天的分享就到这里，如果你对在面试中遇到的问题，或者刚毕业及工作几年迷茫不知道该如何准备面试并突破现状提升自己，对于自己的未来还不够了解不知道给如何规划，可以加一下合作的技术群：887084983。来看看同行们都是如何突破现状，怎么学习的，来吸收他们的面试以及工作经验完善自己的之后的面试计划及职业规划。

> 这里放上一部分我工作以来以及参与过的大大小小的面试收集总结出来的一套**进阶学习的视频及面试专题资料包**，在这里[免费分享](https://links.jianshu.com/go?to=https%3A%2F%2Fjq.qq.com%2F%3F_wv%3D1027%26k%3D55Swnyf)给大家，主要还是希望大家在如今大环境不好的情况下面试能够顺利一点，希望可以帮助到大家~
> 加群：887084983 找管理员免费领取

![](https://upload-images.jianshu.io/upload_images/15233854-1c6a87a9a4da0d57.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/517/format/webp)

##### 未完待续。。。

> 这里只是整理出来的部分面试题，后续会持续更新，希望通过这些高级面试题能够降低面试Android岗位的门槛，让更多的Android工程师理解Android系统，掌握Android系统。喜欢的话麻烦点击一个喜欢在关注一下~

#### 以下墙裂推荐阅读！！！

*   [Android学习笔记参考（敲黑板！！）](https://links.jianshu.com/go?to=https%3A%2F%2Fshimo.im%2Fdocs%2FVxOufNNdaD4WS61R)
*   [“寒冬未过”，阿里P9架构分享Android必备技术点，让你offer拿到手软！](https://www.jianshu.com/p/534cb0eb8d4e)
*   [毕业3年，我是如何从年薪10W的拖拽工程师成为30W资深Android开发者！](https://www.jianshu.com/p/0eda7b13e9fe)
*   [腾讯T3大牛带你了解 2019 Android开发趋势及必备技术点！](https://www.jianshu.com/p/da8dc5b9d3f3)
*   [八年Android开发，从码农到架构师分享我的技术成长之路，共勉！](https://www.jianshu.com/p/ea587bd3cfe5)

**最后祝大家生活愉快~**

![](https://upload-images.jianshu.io/upload_images/15233854-e90328d2bcf537a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/55/format/webp)

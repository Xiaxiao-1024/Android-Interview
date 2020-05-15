![](https://upload-images.jianshu.io/upload_images/15233854-5591f5bd439b48b2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

今天我们来了解一下内存泄漏的知识。

###什么是事件分发？
大家知道Android中的视图是由一个个View嵌套构成的层级视图，即一个View里包含有子View，而这个子View里面又可以再添加View。当用户触摸屏幕产生一系列事件时，事件会由高到低，由外向内依次传递，最终把事件交到一个具体的View手上处理，这个传递的过程就叫做事件分发。

>从手触摸屏幕开始所产生一系列MotionEvent事件，将事件传递到具体的某一个View的过程就叫做事件分发。

Android将触摸事件统一封装成MontionEvent类，以Down事件开始，Up事件结束，中间可能会产生其实事件(例如Move)，组成一个完整的事件系列。

同时事件分发又有三个重要的方法：

#####boolean dispatchTouchEvent(MotionEvent e)

如果事件能够传递给当前View，此方法一定会被调用，意味着当前View接受到事件。

#####boolean onInterceptTouchEvent()

在dispatchTouchEvent方法中调用，用来判断是否要拦截当前事件。基本上不拦截事件。

#####boolean onTouchEvent()

在dispatchTouchEvent方法中调用，用来处理点击事件。返回ture则意味事件被消费。

三大方法有着千丝万缕的关系。调用dispatchTouchEvent()方法则说明事件已经交到该View手上了，接来下要么View自己拦截(onInterceptTouchEvent())处理事件，要么传递给子View。
```
public boolean dispatchTouchEvent(MotionEvent ev){
   boolean consume;
   if(onInterceptTouchEvent(ev)){
       consume = onTouchEvent(ev);
   }else {
       consume = child.dispatchTouchEvent(ev);
   }
   return consume;
}
```
#####举个例子:
小明的父亲准备将传家宝传给小明(dispatchTouchEvent)。小明拿到传家宝后，立马对自己的儿子进行了重重考核(onInterceptTouchEvent)，最终小明觉得自己的儿子太不争气了。就决定不把传家宝交给儿子，把宝贝拿去卖了换钱养老(onTouchEvent)。所以到这，该宝贝传到小明手上就断了传承，同理事件也就意味着结束了。

当然如果小明儿子挺争气的，小明就会把传家宝传给儿子。到了小明的儿子手上又如此向下反复传承。直至有后代自己把宝贝处理了，断了传承，事件也才被消费结束。

小明有后代的情况下才有义务把传家宝传下去，所以他会把传家宝传给符合自己要求的儿子，如果儿子不符合要求，则自己拿来养老。所以ViewGroup的dispatchTouchEvent()一般都不拦截事件，只有在很苛刻的条件下才会主动拦截。接下来再找到符合条件的子View，把事件传递给他。

如果小明终无后人，则传家宝到小明手上后就断了传承，自己连传和不传的选择都没有，只能自己想办法给处理了。所以View没有onInterceptTouchEvent()方法，交到View手上也就意味着事件即将处理。(此View非彼View，例如TextView这种单独的View，不能再添加子View)

![](https://upload-images.jianshu.io/upload_images/15233854-a21505fe16adc8b2.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###从那儿开始发事件？
事件最终会交给一个View处理，那事件最开始是从那儿传递的呢？Activity。
产生事件最先会交给Activity，再依次向下传递。
```
Activity
public boolean dispatchTouchEvent(MotionEvent ev) {
   if (ev.getAction() == MotionEvent.ACTION_DOWN) {
       //空实现
       onUserInteraction();
   }
   if (getWindow().superDispatchTouchEvent(ev)) {
       return true;
   }
   return onTouchEvent(ev);
}
DecorView

public boolean superDispatchTouchEvent(MotionEvent event) {

   return super.dispatchTouchEvent(event);
}

PhoneWindow

@Override
public boolean superDispatchTouchEvent(MotionEvent event) {
   return mDecor.superDispatchTouchEvent(event);
}
```
Activity会将事件传递给Window，window又会向下继续传递。如果最终都没有View消费事件(superDispatchTouchEvent返回false)，则Activity会自己调用onTouchEvent()方法处理事件。
```
if (getWindow().superDispatchTouchEvent(ev)) {
   return true;
}
return onTouchEvent(ev);
```
而PhoneWindow又会把事件传递给DecorView(FrameLayout)。这篇文章对三者之间的关系有一个简单的说明。而FrameLayout(包括其他4大布局)都没有重写dispatchTouchEvent()方法，所以系统的事件传递规则都会按照ViewGroup的dispatchTouchEvent()方法处理。
```
graph TB
   A{Activity}-->B{Window}
   B{Window}-->C{DecorView}
```
从小明有儿子的情况说起
```
// Check for interception.
final boolean intercepted;
if (actionMasked == MotionEvent.ACTION_DOWN || mFirstTouchTarget != null) {
   final boolean disallowIntercept = (mGroupFlags & FLAG_DISALLOW_INTERCEPT) != 0;
   if (!disallowIntercept) {
       intercepted = onInterceptTouchEvent(ev);
       ev.setAction(action); 
   } else {
       intercepted = false;
   }
} else {
   intercepted = true;
}
```
ViewGroup的dispatchTouchEvent()方法会先判断自己是否要拦截当前事件，是否拦截的作用在于，是自己处理事件，还是要将事件传递下去。即intercepted为true自己处理，为false则寻找子View向下传递。当然如果没有符合传递要求的子View，事件还是会由当前View自己处理。

>if (actionMasked == MotionEvent.ACTION_DOWN || mFirstTouchTarget != null)

actionMasked == MotionEvent.ACTION_DOWN好理解。其二，当子View消费事件时，父View会把消费事件的子View用链表记录下来,方便后续事件传递，而mFirstTouchTarget就是链表表头。没有子View能够接受事件,或者子View接受到事件但是不消费,mFirstTouchTargets为null。就会造成下次产生其它事件，走到这里该表达式没有一个为真，则当前View直接拦截事件处理。以后的事件子View想都不要想了。

>如果View开始处理事件，但是不消耗最开头的ACTION_DONW事件(例如：执行onTouchEvent()方法却返回false),以后同系列的事件都不会再交给他。
>
>同时父View以后也不会再有机会执行onInterceptTouchEvent()方法。
>
>final boolean disallowIntercept = (mGroupFlags & FLAG_DISALLOW_INTERCEPT) != 0;

该表达式默认情况下为false，所以会调用onInterceptTouchEvent()方法，但onInterceptTouchEvent()还是不拦截返回false。我们可以调用requestDisallowInterceptTouchEvent(boolean disallowIntercept)方法修改标志位mGroupFlags，即(mGroupFlags & FLAG_DISALLOW_INTERCEPT) != 0表达式成立为ture。

但intercepted还是为false不拦截？那这个表达式和requestDisallowInterceptTouchEvent()方法的价值和意义在哪儿？？

如我们表面所看到的，目的就是为了执不执行onInterceptTouchEvent()方法。例如：当我们自定义View时，在InterceptTouchEvent()方法中处理事件拦不拦截的逻辑。子View可以调用parent.requestDisallowInterceptTouchEvent(true)方法可以让父View没机会执行处理拦截的逻辑，直接让父View开始传递事件。就像小明儿子不管父亲对自己有什么样的看法，直接把传家宝抢到自己手上一个道理。

事件为Down时会重置mGroupFlags标志位状态，即无论怎样还是要走一遭onInterceptTouchEvent()方法。
```
// Handle an initial down.
if (actionMasked == MotionEvent.ACTION_DOWN) {
   // Throw away all previous state when starting a new touch gesture.
   // The framework may have dropped the up or cancel event for the previous gesture
   // due to an app switch, ANR, or some other state change.
   cancelAndClearTouchTargets(ev);
   //重置mGroupFlags
   resetTouchState();
}
```
走到这后，如果intercept为false，则小明心里面已经没有自己卖掉传家宝的想法了。此时是想把传家宝传下去的，接下来就是挑选合格的继承人了。

遍历子View，将事件传递给符合条件的View。
```
final View[] children = mChildren;
for (int i = childrenCount - 1; i >= 0; i--) {
   //i
   final int childIndex = getAndVerifyPreorderedIndex(
           childrenCount, i, customOrder);
   //children[i]
   final View child = getAndVerifyPreorderedView(
           preorderedList, children, childIndex);

   // If there is a view that has accessibility focus we want it
   // to get the event first and if not handled we will perform a
   // normal dispatch. We may do a double iteration but this is
   // safer given the timeframe.
   //事件针对特殊情况，对象才会不为空
   if (childWithAccessibilityFocus != null) {
       if (childWithAccessibilityFocus != child) {
           continue;
       }
       childWithAccessibilityFocus = null;
       i = childrenCount - 1;
   }
   /*
   *canViewReceivePointerEvents()确保子View要可见。执行补间动画时View会变成可见,即使View的Visibility属性为INVISIBLE。
   *isTransformedTouchPointInView()判断事件的坐标是否落在当前子View的区域内。
   */
   if (!canViewReceivePointerEvents(child)
           || !isTransformedTouchPointInView(x, y, child, null)) {
       ev.setTargetAccessibilityFocus(false);
       continue;
   }
   //如果之前已有事件交由子View处理消费，则直接跳出循环，将事件传递下去
   newTouchTarget = getTouchTarget(child);
   if (newTouchTarget != null) {
       // Child is already receiving touch within its bounds.
       // Give it the new pointer in addition to the ones it is handling.
       newTouchTarget.pointerIdBits |= idBitsToAssign;
       break;
   }

   resetCancelNextUpFlag(child);
   //将事件传递给子View
   if (dispatchTransformedTouchEvent(ev, false, child, idBitsToAssign)) {
       // Child wants to receive touch within its bounds.
       mLastTouchDownTime = ev.getDownTime();
       //反射测试preorderedList == null，该集合按照子View绘制顺序和Z轴排序子View。
       if (preorderedList != null) {
           // childIndex points into presorted list, find original index
           for (int j = 0; j < childrenCount; j++) {
               if (children[childIndex] == mChildren[j]) {
                   mLastTouchDownIndex = j;
                   break;
               }
           }
       } else {
           mLastTouchDownIndex = childIndex;
       }
       mLastTouchDownX = ev.getX();
       mLastTouchDownY = ev.getY();
       //链表存储消费事件的子View
       newTouchTarget = addTouchTarget(child, idBitsToAssign);
       alreadyDispatchedToNewTouchTarget = true;
       break;
   }

   // The accessibility focus didn't handle the event, so clear
   // the flag and do a normal dispatch to all children.
   ev.setTargetAccessibilityFocus(false);
}
```
**ViewGroup，挑选传递事件的子View要符合两个条件:**

>可见状态
事件的坐标在子View范围

符合这两个条件,则调用dispatchTransformedTouchEvent()方法把事件传递给子View。dispatchTransformedTouchEvent()方法会根据child参数来做不同的处理，当子View为null时调用View的dispatchTouchEvent()传递事件，意味当前View自己处理事件。child不为null的情况下，则调用child的dispatchTouchEvent()把事件交给子View。
```
if (child == null) {
   handled = super.dispatchTouchEvent(transformedEvent);
} else {
   final float offsetX = mScrollX - child.mLeft;
   final float offsetY = mScrollY - child.mTop;
   transformedEvent.offsetLocation(offsetX, offsetY);
   if (! child.hasIdentityMatrix()) {
       transformedEvent.transform(child.getInverseMatrix());
   }

   handled = child.dispatchTouchEvent(transformedEvent);
}
```
我们以上面布局为例，当我们点击TextView产生Down事件，交到DecorView手上。事件的坐标在ContentView(FrameLayout)区域内，DecorView调用dispatchTransformedTouchEvent()方法，把事件交给ContentView。ContentView又把事件交给RelativeLayout->LinearLayout->TextView。DecorView执行的dispatchTransformedTouchEvent()要等待ContentView的dispatchTouchEvent()方法执行结束才有结果，而ContentView又需要等RelativeLayout执行结束。

如果最终TextView消费了事件，dispatchTouchEvent()返回ture。随之LinearLayout的dispatchTransformedTouchEvent()执行结束为ture。随后调用

>newTouchTarget = addTouchTarget(child, idBitsToAssign);

将TextView记录下来插入表头

>LinearLayout.mFirstTouchTarget.child = TextView;mFirstTouchTarget.next = null;

LinearLayout的dispatchTouchEvent()执行结束返回true，随后RelativeLayout的dispatchTransformedTouchEvent()执行结束为ture;

>RelativeLayout.mFirstTouchTarget.child = LinearLayout;mFirstTouchTarget.next = null;

如此反复向上，Down事件分发结束。

![](https://upload-images.jianshu.io/upload_images/15233854-5efd50b99f8a6c66.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果Dwon事件交到TextView手上，但是TextView并未消费事件，TextView的dispatchTouchEvent()返回false，随即LinearLayout的dispatchTransformedTouchEvent()方法结果为false，造成mFirstTouchTarget不能初始化还是为null。随后LinearLayout会自己处理事件。
```
if (mFirstTouchTarget == null) {
   // No touch targets so treat this as an ordinary view.
   handled = dispatchTransformedTouchEvent(ev, canceled, null,
           TouchTarget.ALL_POINTER_IDS);
}
```
如果LinearLayout依旧不消费事件，则RelativeLayout的mFirstTouchTarget为null,RelativeLayout自己处理事件。如此向上反复，最终Activity会处理事件。接下来如果又触摸屏幕产生了其它后续事件(Move)

>if (actionMasked == MotionEvent.ACTION_DOWN || mFirstTouchTarget != null)
没有一个为真，当事件交给DecorView时就不向下走了，事件会在Activity，window，DecorView三者之间来回传递，最后还是Activity处理。

Case:TextView之前消费了Donw事件,此时手指还未离开屏幕，来回移动产生Move事件。Move事件经Activity交到DecorView手上，DecorView还是一样需要先判断事件是否拦截。
```
if (actionMasked == MotionEvent.ACTION_DOWN || mFirstTouchTarget != null) {
 final boolean disallowIntercept = (mGroupFlags & FLAG_DISALLOW_INTERCEPT) != 0;
   if (!disallowIntercept) {
       intercepted = onInterceptTouchEvent(ev);
       ev.setAction(action); 
   } else {
       intercepted = false;
   }
}
```
依旧会执行onInterceptTouchEvent()方法，虽然结果还是不拦截。随后直接从mFirstTouchTarget表头找到之前消费Down事件的子Viwe，将事件传递给它。不需要再遍历寻找子View了：
```
if (actionMasked == MotionEvent.ACTION_DOWN || (split && actionMasked == MotionEvent.ACTION_POINTER_DOWN) || actionMasked == MotionEvent.ACTION_HOVER_MOVE) {
   //...........
   final View[] children = mChildren;
   for (int i = childrenCount - 1; i >= 0; i--) {
   }
```
只有当事件为Down/ACTION_POINTER_DOWN/ACTION_HOVER_MOVE这三种情况时才会遍历查找符合条件的子View，所以当TextView消费Down事件后，LinearLayout就认准他了，以后的事件都会交给他处理。即使我们的移动范围已经超出了TextView，TextView不可见。事件还是会交给他。

>所以，同一系列事件只能由一个View消费。//除开自己的骚操作
```
if (mFirstTouchTarget == null) {
   handled = dispatchTransformedTouchEvent(ev, canceled, null,
           TouchTarget.ALL_POINTER_IDS);
} else {
   TouchTarget predecessor = null;
   TouchTarget target = mFirstTouchTarget;
   while (target != null) {
       final TouchTarget next = target.next;
       //事件为Down/ACTION_POINTER_DOWN/ACTION_HOVER_MOVE并且被消费
       if (alreadyDispatchedToNewTouchTarget && target == newTouchTarget) {
           handled = true;
       } else {
           final boolean cancelChild = resetCancelNextUpFlag(target.child)
                   || intercepted;
           //第二次Move事件
           if (dispatchTransformedTouchEvent(ev, cancelChild,
                   target.child, target.pointerIdBits)) {
               handled = true;
           }
       }
}
```
可以看到，下回产生的Move事件，确定不拦截后就会走到while中的else中去，如果TextView这回没有消费Move事件，这些事件最终还是会交给Activity处理，以后TextView还是接受到后续的事件。

###View是怎么开始事件？
View和ViewGroup不同，View的dispatchTouchEvent()方法，意味将准备开始处理事件了。
```
public boolean dispatchTouchEvent(MontionEvent event){
   //.....
   ListenerInfo li = mListenerInfo;
   if (li != null && li.mOnTouchListener != null
           && (mViewFlags & ENABLED_MASK) == ENABLED
           && li.mOnTouchListener.onTouch(this, event)) {
       result = true;
   }

   if (!result && onTouchEvent(event)) {
       result = true;
   }
}
```
如果我们给View设置了onTouchListener监听器，则优先会回调Listener的onTouch()方法。如果onTouch()方法返回了false,则还是会执行onTouchEvent()方法。通常我们给View设置的onClickListener，就是在onTouchEvent()方法中的Up事件处理的。所以onTouchListener优先级大于onClickListener。
```
switch (action) {
   case MotionEvent.ACTION_UP:

   if (!post(mPerformClick)) {
   //该方法里会回调onClick()
   performClick();

   }
}
public boolean onTouchEvent(MotionEvent event) {

   final boolean clickable = ((viewFlags & CLICKABLE) == CLICKABLE
           || (viewFlags & LONG_CLICKABLE) == LONG_CLICKABLE)
           || (viewFlags & CONTEXT_CLICKABLE) == CONTEXT_CLICKABLE;
   //View,setEnable()后还是能处理事件。如果我们有给View设置监听器，该事件被消费。
   if ((viewFlags & ENABLED_MASK) == DISABLED) {
       if (action == MotionEvent.ACTION_UP && (mPrivateFlags & PFLAG_PRESSED) != 0) {
           setPressed(false);
       }
       mPrivateFlags3 &= ~PFLAG3_FINGER_DOWN;
       return clickable;
   }
   if (clickable || (viewFlags & TOOLTIP) == TOOLTIP) {

       switch (action) {
           case MotionEvent.ACTION_UP:

           case MotionEvent.ACTION_DOWN:

           case MotionEvent.ACTION_MOVE:
       }
       return true;
   }
   return false;
}
```
View的CLICKABLE属性要为ture，View才能消费上事件。不然onTouchEvent()会执行结束返回false，没有机会消费事件。当我们给View设置监听器后，就会将CLICKABLE属性设为true。(Button默认为ture)
```
public void setOnClickListener(@Nullable OnClickListener l) {
   if (!isClickable()) {
       setClickable(true);
   }
   getListenerInfo().mOnClickListener = l;
}
```
以上面的示例布局为例:如果没有给ReativeLayout，LinearLayout，TextView设置监听器。点击TextView产生Down事件，交到TextView手上执行onTouchEvent()方法时，clickable结果为false。TextView未能消费事件，事件由LinearLayout处理时也未能消费，RelativeLayout结果一样。最后由Activity处理，后续事件都不会再向下传递，会在Activity，window，DecorView之间来回打滚传递。

ViewGroup有传递事件的使命，子View符合可见和在事件坐标上，则传。

没有符合条件的子View和传递给子View不消费，将自己处理。以后事件子View想都不要想。

当父View认准子View(mFirstTouchTarget!=null)，以后的事件都会交给他处理，即使子View不可见和事件坐标超出子View范围。

事件交给单独的View意味着即将处理消费事件，记着要给View设置监听器CLICKABLE为ture，View才能消费事件上。不然onTouch()方法直接返回false，以后都接受不到事件。

onTouchListener优先级大于onClickListener。

>自己处理事件传递时，只要能让ContentView的mFirstTarget!=null，其余都可我们自己控制。
参考:Android开发艺术探索

### 最后

如果你看到了这里，觉得文章写得不错就给个赞呗？如果你觉得那里值得改进的，请给我留言。一定会认真查询，修正不足。谢谢。

![](https://upload-images.jianshu.io/upload_images/15233854-a1188e1f691e98ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/259/format/webp)

**最后文末放上一个技术交流群：[Android架构设计](https://links.jianshu.com/go?to=https%3A%2F%2Fjq.qq.com%2F%3F_wv%3D1027%26k%3D5gyv0JM)（185873940）**

**PS：群内有许多技术大牛，有任何问题，欢迎广大网友一起来交流，群内还不定期免费分享高阶Android学习视频资料和面试资料包~**

**再推荐一篇文章，具体的架构视频，面试专题，学习笔记都在这篇文章中：[“寒冬未过”，阿里P9架构分享Android必备技术点，让你offer拿到手软！](https://www.jianshu.com/p/534cb0eb8d4e)**

偷偷说一句：群里高手如云，欢迎大家加群和大佬们一起交流讨论啊~

 原文链接：https://juejin.im/post/5d6382cd6fb9a06b2262c0f8

![](https://upload-images.jianshu.io/upload_images/15233854-0845dcf5d9a7e861?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "whatsapp-interface-1660652_1280")

## 前言

RecyclerView大概是Android开发者接触最多的一个控件了，官方对其做了很好的封装抽象，使得它既灵活又好用，但是你真的了解它么？在它简单的使用方式之下着实是不简单，首先我们看一下官方对它的介绍：

> A flexible view for providing a limited window into a large data set.

很简单，就一句话「为大量数据集提供一个有限的展示窗口的灵活视图」怎么展示大量的数据是个技术活，这些数据伴随着滚动逐渐展示在我们眼前，但是展示过的滚走的视图呢？它们是否还存在？我想大家肯定知道它们是要被回收的，否者来个几百上千条数据那还不OOM了。那么我们今天就围绕RecyclerView的视图回收机制来谈一谈，到底RecyclerView的回收机制是怎样的。

## 缓存层级

我们先了解下Recycler的缓存结构是怎样的，先了解两个专业词汇：

*   **Scrap (view)**：在布局期间进入临时分离状态的子视图。废弃视图可以重复使用，而不会与父级RecyclerView完全分离，如果不需要重新绑定，则不进行修改，如果视图被视为脏，则由适配器修改。（这里的脏怎么理解呢？就是指那些在展示之前必须重新绑定的视图，比如一个视图原来展示的是“张三”，之后需要展示“李四”了，那么这个视图就是脏视图，需要重新绑定数据后再展示的。）

*   **Recycle (view)**：先前用于显示适配器特定位置的数据的视图可以放置在高速缓存中以供稍后重用再次显示相同类型的数据。这可以通过跳过初始布局或构造来显着提高性能。

RecyclerView的缓存类型呢基本也就是上面的两种，这时可能有同学要站出来说我不对了，胡说，RecyclerView明明有四级缓存，怎么就两种了，骚年稍安勿躁，且听我来慢慢分解。首先我们先看一个RV（RecyclerView在后文简称RV）的内部类Recycler。

```
    public final class Recycler {
        final ArrayList<ViewHolder> mAttachedScrap = new ArrayList<>();
        ArrayList<ViewHolder> mChangedScrap = null;

        final ArrayList<ViewHolder> mCachedViews = new ArrayList<ViewHolder>();
        RecycledViewPool mRecyclerPool;
        private ViewCacheExtension mViewCacheExtension;
        …… 省略 ……
    }
```

就是介个类掌握着RV的缓存大权，从上面的代码片段我们可以看到这个类声明了五个成员变量。我们一个个的来说一下：

1.  mAttachedScrap:我们可以看到这个变量是个存放ViewHolder对象的ArrayList,这一级缓存是没有容量限制的，只要符合条件的我来者不拒，全收了。前面讲两个专业术语的时候提到了Scrap,这个就属于Scrap中的一种，这里的数据是不做修改的，不会重新走Adapter的绑定方法。

2.  mChangedScrap:这个变量和上边的mAttachedScrap是一样的，唯一不同的从名字也可以看出来，它存放的是发生了变化的ViewHolder,如果使用到了这里的缓存的ViewHolder是要重新走Adapter的绑定方法的。

3.  mCachedViews:这个变量同样是一个存放ViewHolder对象的ArrayList,但是这个不同于上面的两个里面存放的是dettach掉的视图，它里面存放的是已经remove掉的视图，已经和RV分离的关系的视图，但是它里面的ViewHolder依然保存着之前的信息，比如position、和绑定的数据等等。这一级缓存是有容量限制的，默认是2（不同版本API可能会有差异，本文基于API26.1.0）。

4.  mRecyclerPool:这个变量呢本身是一个类，跟上面三个都不一样。这里面保存的ViewHolder不仅仅是removed掉的视图，而且是恢复了出厂设置的视图，任何绑定过的痕迹都没有了，想用这里缓存的ViewHolder那是铁定要重新走Adapter的绑定方法了。而且我们知道RV支持多布局，所以这里的缓存是按照itemType来分开存储的，我们来大致的看一下它的结构：

```
    public static class RecycledViewPool {
        private static final int DEFAULT_MAX_SCRAP = 5;
        static class ScrapData {
            ArrayList<ViewHolder> mScrapHeap = new ArrayList<>();
            int mMaxScrap = DEFAULT_MAX_SCRAP;
            …… 省略 ……
        }
        SparseArray<ScrapData> mScrap = new SparseArray<>();
        …… 省略后面代码 ……
    }
```

*   首先我们看到一个常量‘DEFAULT_MAX_SCRAP’，这个就是缓存池定义的一个默认的缓存数，当然这个缓存数我们是可以自己设置的。而且这个缓存数量不是指整个缓存池只能缓存这么多，而是每个不同itemType的ViewHolder的缓存数量。

*   接着往下看，我们看到一个静态内部类ScrapData,这里我们只看跟缓存相关的两个变量，先说mMaxScrap，前面的常量赋值给了它，这也就印证了我们前面说的这个缓存数量是对应每一种类型的ViewHolder的。再来看这个mScrapHeap变量，熟悉的一幕又来了，同样是一个缓存ViewHolder对象的ArrayList，它的容量默认是5.

*   最后我们看到mScrap这个变量，它是一个存储我们上面提到的ScrapData类的对象的SparseArray，这样我们这个RecyclerPool就把不同itemType的ViewHolder按类型分类缓存了起来。

mViewCacheExtension：这一级缓存是留给开发者自由发挥的，官方并没有默认实现，它本身是null。

![](https://upload-images.jianshu.io/upload_images/15233854-ec835eee14a2a49b?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "waste-separation-502952_1280")

垃圾桶讲完了，哦不，是缓存层级讲完了。这里提一句，其实还有一层没有提到，因为它不在Recycler这个类中，它在ChildHelper类中，其中有个mHiddenViews,是个缓存被隐藏的ViewHolder的ArrayList。到这里我想大家对这几层缓存心里已经有个数了，但是还远远不够，这么多层缓存是怎么工作的？什么时候用什么缓存？各个缓存之间有没有什么PY交易？如果让你自己写一个LayoutManager你能处理好缓存问题么？就好比垃圾分类后，我们知道每种垃圾桶的定义和功能，但是面对大妈的灵魂拷问我依然分不清自己是什么垃圾，我太难了～相比之下，RV的几个垃圾桶简单多了，下面我们一起来看看，这些个缓存都咋用。

## 各缓存的使用

上面我们介绍了RV的各缓存层级，但是它们是怎么工作的呢？为什么要设计这些层级呢？别急，我们去源码中找找答案。一叶落而知天下秋，我们就从官方自带的最简单的布局管理者LinearLayoutManager入手,来看看到底如何使用这几级缓存写出一个合格的布局管理者。

### RV从无到有的加载过程

首先我们看一下RV从无到有是怎么显示出数据来的。大家因该知道一个视图的显示要经过onMeasure、onLayout、onDraw三个方法，那么我们就先从第一个方法onMeasure入手，来看看里面做了什么。
```
    @Override
    protected void onMeasure(int widthSpec, int heightSpec) {
        if (mLayout == null) {
            defaultOnMeasure(widthSpec, heightSpec);
            return;
        }
        if (mLayout.mAutoMeasure) {
            if (mState.mLayoutStep == State.STEP_START) {
                dispatchLayoutStep1();
            }
            dispatchLayoutStep2();
        }
    }
```
上面代码省略了一些无关代码，我们只看我们关心的，dispatchLayoutStep1和2方法，1方法中如果mState.mRunPredictiveAnimations为true会调用mLayout.onLayoutChildren(mRecycler, mState)这个方法，但是一般RV的预测动画都为false，所以我们看一下2方法，方法中同样调用了mLayout.onLayoutChildren(mRecycler, mState)方法，来看一下：
```
    //已省略无关代码
    private void dispatchLayoutStep2() {
        eatRequestLayout();
        onEnterLayoutOrScroll();

        // Step 2: Run layout
        mState.mInPreLayout = false;
        mLayout.onLayoutChildren(mRecycler, mState);

        mState.mLayoutStep = State.STEP_ANIMATIONS;
        onExitLayoutOrScroll();
        resumeRequestLayout(false);
    }
```
这里onLayoutChildren方法是必走的，而mLayout是RV的成员变量，也就是LayoutManager,接下来我们去LinearLayoutManager里看看onLayoutChildren方法做了什么。
```
    //已省略无关代码
    @Override
    public void onLayoutChildren(RecyclerView.Recycler recycler, RecyclerView.State state) {

        detachAndScrapAttachedViews(recycler);

        if (mAnchorInfo.mLayoutFromEnd) {
            // fill towards start
            fill(recycler, mLayoutState, state, false);

            // fill towards end
            fill(recycler, mLayoutState, state, false);
            endOffset = mLayoutState.mOffset;
        } else {
            // fill towards end
            fill(recycler, mLayoutState, state, false);

            // fill towards start
            fill(recycler, mLayoutState, state, false);
            startOffset = mLayoutState.mOffset;
        }
    }
```
这个方法挺长的，我们只看最关心的，来看下detachAndScrapAttachedViews(recycler)方法中做了什么。
```
    public void detachAndScrapAttachedViews(Recycler recycler) {
            final int childCount = getChildCount();
            for (int i = childCount - 1; i >= 0; i--) {
                final View v = getChildAt(i);
                scrapOrRecycleView(recycler, i, v);
            }
    }
 ```
如果有子view调用了scrapOrRecycleView(recycler, i, v)方法，继续追踪。
 ```
    private void scrapOrRecycleView(Recycler recycler, int index, View view) {
        final ViewHolder viewHolder = getChildViewHolderInt(view);
        if (viewHolder.isInvalid() && !viewHolder.isRemoved()
                && !mRecyclerView.mAdapter.hasStableIds()) {
            removeViewAt(index);
            recycler.recycleViewHolderInternal(viewHolder);
        } else {
            detachViewAt(index);
            recycler.scrapView(view);
            mRecyclerView.mViewInfoStore.onViewDetached(viewHolder);
        }
    }
```
正常开始布局的时候会进入else分支，首先是调用detachViewAt(index)来分离视图，然后调用了recycler.scrapView(view)方法。前面我们说过Recycler是RV的内部类，是管理RV缓存的核心类，然后我们继续追踪这个srapView方法，看看里面做了什么。
```
    void scrapView(View view) {
        final ViewHolder holder = getChildViewHolderInt(view);
        if (holder.hasAnyOfTheFlags(ViewHolder.FLAG_REMOVED | ViewHolder.FLAG_INVALID)
                || !holder.isUpdated() || canReuseUpdatedViewHolder(holder)) {
            if (holder.isInvalid() && !holder.isRemoved() && !mAdapter.hasStableIds()) {
                throw new IllegalArgumentException("……");
            }
            holder.setScrapContainer(this, false);
            mAttachedScrap.add(holder);
        }
    }
```
这里我们看到了熟悉的身影，「mAttachedScrap」,到此为止我们知道了，onLayoutChildren方法中调用detachAndScrapAttachedViews方法把存在的子view先分离然后缓存到了AttachedScrap中。我们回到onLayoutChildren方法中看看接下来做了什么，我们发现它先判断了方向，因为LinearLayoutManager有横纵两个方向，无论哪个方向最后都是调用fill方法，见名知意，这是个填充布局的方法，fill方法中又调用了layoutChunk这个方法，我们看一眼这个方法。
```
    void layoutChunk(RecyclerView.Recycler recycler, RecyclerView.State state,
            LayoutState layoutState, LayoutChunkResult result) {
        View view = layoutState.next(recycler);
        if (view == null) {
            return;
        }
        if (layoutState.mScrapList == null) {
            if (mShouldReverseLayout == (layoutState.mLayoutDirection
                    == LayoutState.LAYOUT_START)) {
                addView(view);
            } else {
                addView(view, 0);
            }
        }
    }
```
该方法中我们看到通过layoutState.next(recycler)方法来拿到视图，如果这个视图为null那么方法终止，否则就会调用addView方法将视图添加或者重新attach回来，这个我们不关心，我们看看是怎么拿到视图的。
```
    View next(RecyclerView.Recycler recycler) {
        if (mScrapList != null) {
            return nextViewFromScrapList();
        }
        final View view = recycler.getViewForPosition(mCurrentPosition);
        mCurrentPosition += mItemDirection;
        return view;
    }
```
首先我们看到如果mScrapList不为空会去其中取视图，mScrapList是什么呢？实际上它就是mAttachedScrap,但是它是只读的，而且只有在开启预测动画时才会被赋值，所以我们忽略它即可。重点关注下recycler.getViewForPosition(mCurrentPosition)方法,这个方法经过层层调用，最终是调用的Recycler类中的「tryGetViewHolderForPositionByDeadline(int position,boolean dryRun,long deadlineNs)」方法，接下来看一下这个方法做了哪些事。
```
    @Nullable
    ViewHolder tryGetViewHolderForPositionByDeadline(int position,
            boolean dryRun, long deadlineNs) {
        boolean fromScrapOrHiddenOrCache = false;
        ViewHolder holder = null;
        // 0) If there is a changed scrap, try to find from there
        if (mState.isPreLayout()) {
            holder = getChangedScrapViewForPosition(position);
            fromScrapOrHiddenOrCache = holder != null;
        }
        // 1) Find by position from scrap/hidden list/cache
        if (holder == null) {
            holder = getScrapOrHiddenOrCachedHolderForPosition(position, dryRun);
        }
        if (holder == null) {
            // 2) Find from scrap/cache via stable ids, if exists
            if (holder == null && mViewCacheExtension != null) {
                final View view = mViewCacheExtension
                        .getViewForPositionAndType(this, position, type);
            }
            if (holder == null) {
                holder = getRecycledViewPool().getRecycledView(type);
            }
            if (holder == null) {
                holder = mAdapter.createViewHolder(RecyclerView.this, type);
            }
        }
        return holder;
    }
```
这段代码着实做了不少事情，获取View和绑定View都是在这个方法中完成的，当然关于绑定和其它的无关代码这里就不贴了。我们一步步的看一下：

**1\. 第一步**先从 getChangedScrapViewForPosition(position)方法中找需要的视图，但是有个条件mState.isPreLayout()要为true，这个一般在我们调用adapter的notifyItemChanged等方法时为true，其实也很好理解，数据发生了变化，viewholder被detach掉后缓存在mChangedScrap之中，在这里拿到的viewHolder后续需要重新绑定。

**2\. 第二步**，如果没有找到视图则从getScrapOrHiddenOrCachedHolderForPosition这个方法中继续找。这个方法的代码就不贴了，简单说下这里的查找顺序：

*   首先从mAttachedScrap中查找
*   再次从前面略过的ChildHelper类中的mHiddenViews中查找
*   最后是从mCachedViews中查找的

**3\. 第三步**， mViewCacheExtension中查找，我们说过这个对象默认是null的，是由我们开发者自定义缓存策略的一层，所以如果你没有定义过，这里是找不到View的。

**4\. 第四步**，从RecyclerPool中查找，前面我们介绍过RecyclerPool,先通过itemType从SparseArray类型的mscrap中拿到ScrapData，不为空继续拿到scrapHeap这个ArrayList，然后取到视图，这里拿到的视图需要重新绑定。

**5\. 第五步**，如果前面几步都没有拿到视图，那么调用了mAdapter.createViewHolder(RecyclerView.this, type)方法，这个方法内部调用了一个抽象方法onCreateViewHolder,是不是很熟悉，没错，就是我们自己写一个Adapter要实现的方法之一。

到此为止我们获取一个视图的流程就讲完了，获取到视图之后就是怎么摆放视图并添加到RV之中，然后最终展示到我们面前。细心的小伙伴可能发现这个流程貌似有点问题啊？第一次进入onLayoutChildren时还没有任何子view，在fill方法前等于没有缓存子view，所有的子View都是第五步onCreateViewHolder创建而来的。实际上这里的设计是有道理的，除了一些特殊情况onLayoutChildren方法会被多次调用外，一个View从无到有展示在我们面前要至少经过两次onMeasure,一次onLayout,一次onDraw方法（为什么是这样的呢，感兴趣的小伙伴可以去ViewRootImpl中找找答案）。所以这里需要做个缓存，而不至于每次都重新创建新的视图。整个过程大致如图：

![](https://upload-images.jianshu.io/upload_images/15233854-e52335d6358338e4?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "1566893166006")

这里提一下，在RV展示成功后，Scrap这层的缓存就为空了，在从Scrap中取视图的同时就被移出了缓存。在onLayout这里最终会调用到dispatchLayoutStep3方法，没错，除了1和2还有3，在3中，如果Scrap还有缓存，那么缓存会被清空，清空的缓存会被添加到mCachedViews或者RecyclerPool中。

### RV滑动时的缓存过程

RV是可以通过滚动来展示大量数据的控件，那么由当前屏幕滚动而出的View去哪了？滚动而入的View哪来的？同样的，我们去源码中找找答案。

> scrollHorizontallyBy,scrollVerticallyBy

*   一个LayoutManager如果可以滑动，那么上面的两个方法要返回非0值，分别代表可以横向滚动和纵向滚动。最终两个方法都会调用scrollBy方法，然后scrollby方法调用了fill方法，这个fill我们已经见过了，现在再看一下。

```
    int fill(RecyclerView.Recycler recycler, LayoutState layoutState,
            RecyclerView.State state, boolean stopOnFocusable) {
        final int start = layoutState.mAvailable;
        if (layoutState.mScrollingOffset != LayoutState.SCROLLING_OFFSET_NaN) {
            // TODO ugly bug fix. should not happen
            if (layoutState.mAvailable < 0) {
                layoutState.mScrollingOffset += layoutState.mAvailable;
            }
            recycleByLayoutState(recycler, layoutState);
        }
    }
```

这这段代码中判断了当前是否是滚动触发的fill方法，如果是调用recycleByLayoutState(recycler, layoutState)方法。这个方法几经周转会调用到removeAndRecycleViewAt方法：

```
    public void removeAndRecycleViewAt(int index, Recycler recycler) {
        final View view = getChildAt(index);
        removeViewAt(index);
        recycler.recycleView(view);
    }
```

这里注意先把视图remove掉了，而不是detach掉。然后调用Recycler中的recycleView方法，这个方法最后会调用recycleViewHolderInternal方法，方法如下：

```
    void recycleViewHolderInternal(ViewHolder holder) {

            if (forceRecycle || holder.isRecyclable()) {
                if (省略) {
                    int cachedViewSize = mCachedViews.size();
                    if (cachedViewSize >= mViewCacheMax && cachedViewSize > 0) {
                        recycleCachedViewAt(0);
                        cachedViewSize--;
                    }
                    mCachedViews.add(targetCacheIndex, holder);
                    cached = true;
                }
                if (!cached) {
                    addViewHolderToRecycledViewPool(holder, true);
                    recycled = true;
                }
            }
    }
```

删除不相关代码后逻辑很清晰。前面我们说过mCachedViews是有容量限制的，默认为2。那么如果符合放到mCachedViews中的条件，首先会判断mCachedViews是否已经满了，如果满了会通过recycleCachedViewAt(0)方法把最老得那个缓存放进RecyclerPool,然后在把新的视图放进mCachedViews中。如果这个视图不符合条件会直接被放进RecyclerPool中。我们注意到，在缓存进mCachedViews之前，我们的视图只是被remove掉了，绑定的数据等信息都还在，这意味着从mCachedViews取出的视图如果符合需要的目标视图是可以直接展示的，而不需要重新绑定。而放进RecyclerPool最终是要调用putRecycledView方法的。

```
    public void putRecycledView(ViewHolder scrap) {
        final int viewType = scrap.getItemViewType();
        final ArrayList<ViewHolder> scrapHeap = getScrapDataForType(viewType).mScrapHeap;
        if (mScrap.get(viewType).mMaxScrap <= scrapHeap.size()) {
            return;
        }
        scrap.resetInternal();
        scrapHeap.add(scrap);
    }
```

这个方法中同样对容量做了判断，跟mCachedViews不一样，如果容量满了，就不再继续缓存了。在缓存之前先调用了scrap.resetInternal()方法，这个方法顾名思义是个重置的方法，缓存之前把视图的信息都清除掉了，这也是为什么这里缓存满了之后就不再继续缓存了，而不是把老的缓存替换掉，因为它们重置后都一样了（这里指具有同种itemType的是一样的）。这就是滑动缓存的全过程，至此我们知道了滚动出去的视图去哪了，那么滚动进来的视图哪来的呢？

*   和从无到有的过程一样，最后滚动也调用了fill方法，那最后必然是要走到前面分析的获取视图的5个流程。前面说过在布局完成之后，Scrap层的缓存就是空的了，那就只能从mCachedViews或者RecyclerPool中取了，都取不到最后就会走onCreateViewHolder创建视图。到这里滑动时的缓存以及取缓存就讲完了。

### 数据更新时的缓存过程

这块我就简单说一下结论，感兴趣的同学可以自行查看源码。为什么我们在有数据刷新的时候推荐大家使用notifyItemChanged等方法而不使用notifyDataSetChanged方法呢？

*   在调用notifyDataSetChanged方法后，所有的子view会被标记，这个标记导致它们最后都被缓存到RecyclerPool中，然后重新绑定数据。并且由于RecyclerPool有容量限制，如果不够最后就要重新创建新的视图了。

*   但是使用notifyItemChanged等方法会将视图缓存到mChangedScrap和mAttachedScrap中，这两个缓存是没有容量限制的，所以基本不会重新创建新的视图，只是mChangedScrap中的视图需要重新绑定一下。

## 总结

我们从缓存的几个类型以及布局、滚动、刷新几个方面全方位的剖析了RV的缓存机制。

> 这么多层缓存是怎么工作的？什么时候用什么缓存？各个缓存之间有没有什么PY交易？如果让你自己写一个LayoutManager你能处理好缓存问题么？

我相信你已经有了自己的答案，后续会推出一篇关于自定义LayoutManager的文章，敬请期待。

好啦，如此文章到这里就结束了，如果你觉得不错就给个赞呗？如果你觉得那里值得改进的，请给我留言。一定会认真查询，修正不足。谢谢。

希望读到这的您能转发分享和关注一下我，以后还会更新技术干货，谢谢您的支持！

**转发+点赞+关注**，第一时间获取最新知识点

Android架构师之路很漫长，一起共勉吧！

#### 以下墙裂推荐阅读！！！

*   [Android学习笔记参考（敲黑板！！）](https://links.jianshu.com/go?to=https%3A%2F%2Fshimo.im%2Fdocs%2FVxOufNNdaD4WS61R)
*   [“寒冬未过”，阿里P9架构分享Android必备技术点，让你offer拿到手软！](https://www.jianshu.com/p/534cb0eb8d4e)
*   [毕业3年，我是如何从年薪10W的拖拽工程师成为30W资深Android开发者！](https://www.jianshu.com/p/0eda7b13e9fe)
*   [腾讯T3大牛带你了解 2019 Android开发趋势及必备技术点！](https://www.jianshu.com/p/da8dc5b9d3f3)
*   [八年Android开发，从码农到架构师分享我的技术成长之路，共勉！](https://www.jianshu.com/p/ea587bd3cfe5)

**最后祝大家生活愉快~**

![](https://upload-images.jianshu.io/upload_images/15233854-e90328d2bcf537a7.png?imageMogr2/auto-orient/strip|imageView2/2/w/55/format/webp)

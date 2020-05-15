![](https://upload-images.jianshu.io/upload_images/15233854-2391cf18c7e11877.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

面试的时候，面试官经常同你随便侃侃Activity的启动模式，但Activity启动牵扯的知识点其实很多，并非能单单用四个启动模式就能概括的，默认的启动模式的表现会随着Intent Flag的设置而改变，因此侃Activity启动模式大多走流程装逼，最多结合项目遇到的问题，随便刁难一下面试者，并不太容易把控，也许最后，面试官跟面试者的答案都是错了，比如在Service中必须通过设置FLAG_ACTIVITY_NEW_TASK才能启动Activity，这个时候启动Activit会有什么样的表现呢？就这一个问题，答案就要分好几个场景：

*   Activity的taskAffinity属性的Task栈是否存在
*   如果存在，要看Activity是否存已经存在于该Task
*   如果已经存在于该taskAffinity的Task，要看其是不是其rootActivity
*   如果是其rootActivity，还要看启动该Activity的Intent是否跟当前intent相等

不同场景，所表现的行为都会有所不同，再比如singleInstance属性，如果设置了，大家都知道只有一个实例，将来再启动会复用，但是如果使用Intent.FLAG_ACTIVITY_CLEAR_TASK来启动，仍然会重建，并非完全遵守singleInstance的说明，还有不同Flag在叠加使用时候也会有不同的表现，单一而论Activity启动模式其实是很难的。本文也仅仅是涉及部分启动模式及Flag，更多组合跟场景要自己看源码或者实验来解决了。

## 简单基本launchmode

本文假定大家对于Activity的Task栈已经有初步了解，首先，看一下Activity常见的四种启动模式及大众理解，这也是面试时最长问的：

*   **standard**：标准启动模式（默认启动模式），每次都会启动一个新的activity实例。
*   **singleTop**：单独使用使用这种模式时，如果**Activity实例位于当前任务栈顶**，就重用栈顶实例，而不新建，并回调该实例onNewIntent()方法，否则走新建流程。
*   **singleTask**：这种模式启动的Activity**只会存在相应的Activity的taskAffinit任务栈中**，同一时刻系统中只会存在一个实例，已存在的实例被再次启动时，会重新唤起该实例，并清理当前Task任务栈该实例之上的所有Activity，同时回调onNewIntent()方法。
*   **singleInstance**：这种模式启动的Activity独自占用一个Task任务栈，同一时刻系统中只会存在一个实例，已存在的实例被再次启动时，只会唤起原实例，并回调onNewIntent()方法。

需要说明的是：**上面的场景仅仅适用于Activity启动Activity，并且采用的都是默认Intent，没有额外添加任何Flag**，否则表现就可能跟上面的完全不一致，尤其要注意的是FLAG_ACTIVITY_NEW_TASK的使用，后面从源码中看，依靠FLAG_ACTIVITY_NEW_TASK其实可以分为两派。

## Intent.FLAG_ACTIVITY_NEW_TASK分析

从源码来看，Intent.FLAG_ACTIVITY_NEW_TASK是启动模式中最关键的一个Flag，依据该Flag启动模式可以分成两类，设置了该属性的与未设置该属性的，对于非Activity启动的Activity（比如Service或者通知中启动的Activity）需要显示的设置Intent.FLAG_ACTIVITY_NEW_TASK，而singleTask及singleInstance在AMS中被预处理后，隐形的设置了Intent.FLAG_ACTIVITY_NEW_TASK，而启动模式是standard及singletTop的Activity不会被设置Intent.FLAG_ACTIVITY_NEW_TASK，除非通过显示的intent setFlag进行设置。

**FLAG_ACTIVITY_NEW_TASK这个属性更多的关注点是在Task，可以认为没有设置FLAG_ACTIVITY_NEW_TASK的情况下，taskAffinity可以不考虑**，大多数情况下，需要将Activity引入到自己taskAffinity的Task中，**Intent.FLAG_ACTIVITY_NEW_TASK的初衷是在Activity目标taskAffinity的Task中启动**，非Activity启动Activity都必须添加Intent.FLAG_ACTIVITY_NEW_TASK才行，以Service启动的Activity为例：

```
  Intent intent = new Intent(BackGroundService.this, A.class);
  intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
  startActivity(intent);    
```

这种情况很有意思，如果目标Activity实例或者Task不存在，则一定会新建Activity，并将目标Task移动到前台，但是如果Activity存在，却并不一定复用，也不一定可见。这里假定A是standard的Activity，如果已经有一个A实例，并且所在的堆栈的taskAffinity跟A的taskAffinity一致，这个时候要看这个task的根Activity是不是A，如果是A，还要看A的intent是不是跟当前的启动的intent相等，如果都满足，只要将task可见即可。否则，就需要新建A，并根据A的task栈的存在情况而选择直接入栈还是新建栈。但是，如果Intent想要的启动的Activity的目标堆栈存在，那就将整个堆栈往前迁移，如果位于顶部的Task栈正好是目标Activity的Task栈，那就不做任何处理，连onNewIntent都不会回调，怎么判断目标的Activity的Task栈同找到的栈一致呢？**如果找不到目标Task自然会启动Task，如果目标task栈根Activit的intent同新将要启动的Activit相同，就不启动新Activity，否则启动Activity**。

![](//upload-images.jianshu.io/upload_images/1460468-b8dcbf77283c67b2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

### Intent.FLAG_ACTIVITY_CLEAR_TASK：必须配合FLAG_ACTIVITY_NEW_TASK使用

>If set in an Intent passed to Context.startActivity(), this flag will cause any existing task that would be associated with the activity to be cleared before the activity is started. That is, the activity becomes the new root of an otherwise empty task, and any old activities are finished. This can only be used in conjunction with FLAG_ACTIVITY_NEW_TASK.

这个属性必须同FLAG_ACTIVITY_NEW_TASK配合使用，如果设置了FLAG_ACTIVITY_NEW_TASK|Intent.FLAG_ACTIVITY_CLEAR_TASK，如果目标task已经存在，将清空已存在的目标Task，否则，新建一个Task栈，之后，新建一个Activity作为根Activity。Intent.FLAG_ACTIVITY_CLEAR_TASK的优先级最高，基本可以无视所有的配置，包括启动模式及Intent Flag，哪怕是singleInstance也会被finish，并重建。

![](//upload-images.jianshu.io/upload_images/1460468-7019ca40bf396b92.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

### Intent.FLAG_ACTIVITY_CLEAR_TOP

如果没有使用FLAG_ACTIVITY_NEW_TASK，目标是当前Task栈，根据不同的组合会产生不同的效果，如果单独使用Intent.FLAG_ACTIVITY_CLEAR_TOP，并且没有设置特殊的launchmode，那么，Google官方的示例是：如果ABCD Task中的D采用Intent.FLAG_ACTIVITY_CLEAR_TOP唤起B，这个时候首先会将CD出栈，但是至于B是否会重建，要视情况而定，如果没有设置FLAG_ACTIVITY_SINGLE_TOP，则会将B finish掉，之后创建新的入栈。如果同一个栈中原来有

![](//upload-images.jianshu.io/upload_images/1460468-ca92ed744113327a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

如果没有则新建，不会去另一个栈中寻找。

![](//upload-images.jianshu.io/upload_images/1460468-cdc2ce8ee52408d1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

如果同时设置了FLAG_ACTIVITY_SINGLE_TOP，在当前栈已有的情况下就不会重建，而是直接回调B的onNewIntent(),

![](//upload-images.jianshu.io/upload_images/1460468-d02ee010dd2af71d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

官方解释如下：

>For example, consider a task consisting of the activities: A, B, C, D. If D calls startActivity() with an Intent that resolves to the component of activity B, then C and D will be finished and B receive the given Intent, resulting in the stack now being: A, B。
>
>The currently running instance of activity B in the above example will either receive the new intent you are starting here in its onNewIntent() method, or be itself finished and restarted with the new intent. If it has declared its launch mode to be "multiple" (the default) and you have not set FLAG_ACTIVITY_SINGLE_TOP in the same intent, then it will be finished and re-created; for all other launch modes or if FLAG_ACTIVITY_SINGLE_TOP is set then this Intent will be delivered to the current instance's onNewIntent().

如果同时使用了FLAG_ACTIVITY_NEW_TASK ，这个时候，目标是Activity自己所属的Task栈，如果在自己的Task中能找到一个Activity实例，则将其上面的及自身清理掉，之后重建。

![](//upload-images.jianshu.io/upload_images/1460468-b26b3f209471b11d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

如果同时在加上FLAG_ACTIVITY_SINGLE_TOP，会更特殊一些，如果topActivity不是目标Activity，就会去目标Task中去找，并唤起

![](//upload-images.jianshu.io/upload_images/1460468-7b7716086e051879.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

**如果topActivity是目标Activity，就直接回调topActivity的onNewIntent，无论topActivity是不是在目标Task中**

![](//upload-images.jianshu.io/upload_images/1460468-8520b6969eeaad12.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

### Intent.FLAG_ACTIVITY_SINGLE_TOP

Intent.FLAG_ACTIVITY_SINGLE_TOP多用来做辅助作用，跟launchmode中的singleTop作用一样，在Task栈顶有的话，就不新建，栈顶没有的话，就新建，这里的Task可能是目标栈，也可能是当前Task栈，配合FLAG_ACTIVITY_NEW_TASK及FLAG_ACTIVITY_CLEAR_TOP都会有很有意思的效果。

## 源码分析

现在我们看一下源码，来分析下原理：从源码更能看出FLAG_ACTIVITY_NEW_TASK重要性，这里只分析部分场景，不然太多了： 下面的是4.3的代码，比较简单些：

```
final int startActivityUncheckedLocked(ActivityRecord r,
        ActivityRecord sourceRecord, int startFlags, boolean doResume,
        Bundle options) {
    final Intent intent = r.intent;
    final int callingUid = r.launchedFromUid;
    ...
    <!--关键点1：直接获取launchFlags--> 
    int launchFlags = intent.getFlags();
    ...
     <!--关键点2：预处理一些特殊的 launchmode，主要是设置Intent.FLAG_ACTIVITY_NEW_TASK-->
    // 如果sourceRecord ==null 说明不是从activity启动的，从服务开启就一定要start a new task
    if (sourceRecord == null) {
        if ((launchFlags&Intent.FLAG_ACTIVITY_NEW_TASK) == 0) {
            launchFlags |= Intent.FLAG_ACTIVITY_NEW_TASK;
        }
    } else if (sourceRecord.launchMode == ActivityInfo.LAUNCH_SINGLE_INSTANCE) {
        launchFlags |= Intent.FLAG_ACTIVITY_NEW_TASK;
    } else if (r.launchMode == ActivityInfo.LAUNCH_SINGLE_INSTANCE
            || r.launchMode == ActivityInfo.LAUNCH_SINGLE_TASK) {
        launchFlags |= Intent.FLAG_ACTIVITY_NEW_TASK;
    }
    ...
    <!--关键点3 对于Intent.FLAG_ACTIVITY_NEW_TASK 对sourceActivity直接返回cancel-->
    if (r.resultTo != null && (launchFlags&Intent.FLAG_ACTIVITY_NEW_TASK) != 0) {
        sendActivityResultLocked(-1,
                r.resultTo, r.resultWho, r.requestCode,
            Activity.RESULT_CANCELED, null);
        r.resultTo = null;
    }
    boolean addingToTask = false;
    boolean movedHome = false;
    TaskRecord reuseTask = null;
     <!--关键点4 对于Intent.FLAG_ACTIVITY_NEW_TASK分支的处理-->
    if (((launchFlags&Intent.FLAG_ACTIVITY_NEW_TASK) != 0 &&
            (launchFlags&Intent.FLAG_ACTIVITY_MULTIPLE_TASK) == 0)
            || r.launchMode == ActivityInfo.LAUNCH_SINGLE_TASK
            || r.launchMode == ActivityInfo.LAUNCH_SINGLE_INSTANCE) {
        if (r.resultTo == null) {
            <!--关键点5找到目标Task栈的栈顶元素 ，但是taskTop不一定是目标Activity-->   
            ActivityRecord taskTop = r.launchMode != ActivityInfo.LAUNCH_SINGLE_INSTANCE
                    ?findTaskLocked(intent, r.info)
                    : findActivityLocked(intent, r.info);
            <!--如果目标栈存在-->
            if (taskTop != null) {
                ActivityRecord curTop = topRunningNonDelayedActivityLocked(notTop);
                <!--关键点6 看找到的Task是不是位于栈顶，如果不是则移动-->
                if (curTop != null && curTop.task != taskTop.task) {
                    r.intent.addFlags(Intent.FLAG_ACTIVITY_BROUGHT_TO_FRONT);
                    boolean callerAtFront = sourceRecord == null
                            || curTop.task == sourceRecord.task;
                    if (callerAtFront) {
                        movedHome = true;
                        moveHomeToFrontFromLaunchLocked(launchFlags);
                        moveTaskToFrontLocked(taskTop.task, r, options);
                        options = null;
                    }
                }
                    ...
                    <!--关键点6 如果设置了Intent.FLAG_ACTIVITY_CLEAR_TASK，则重置Task，并为reuseTask赋值，这个属性只能配合Intent.FLAG_ACTIVITY_NEW_TASK使用-->
                if ((launchFlags &
                        (Intent.FLAG_ACTIVITY_NEW_TASK|Intent.FLAG_ACTIVITY_CLEAR_TASK))
                        == (Intent.FLAG_ACTIVITY_NEW_TASK|Intent.FLAG_ACTIVITY_CLEAR_TASK)) {
                    reuseTask = taskTop.task;
                    performClearTaskLocked(taskTop.task.taskId);
                    reuseTask.setIntent(r.intent, r.info);
                 <!--关键点7 如果设置了Intent.FLAG_ACTIVITY_CLEAR_TOP，则将Task中目标Activity之上的清空，至于自己是否要清空，还要看是不是设置了singltTop-->
                } else if ((launchFlags&Intent.FLAG_ACTIVITY_CLEAR_TOP) != 0
                        || r.launchMode == ActivityInfo.LAUNCH_SINGLE_TASK
                        || r.launchMode == ActivityInfo.LAUNCH_SINGLE_INSTANCE) {
                    <!--如果能找到一个SingleTop，则不用重建-->
                    ActivityRecord top = performClearTaskLocked(
                            taskTop.task.taskId, r, launchFlags);
                    if (top != null) {
                    <!--如果自己是rootActivity，则重置intent-->
                        if (top.frontOfTask) {
                            top.task.setIntent(r.intent, r.info);
                        }
                        top.deliverNewIntentLocked(callingUid, r.intent);
                    } else {
                     <!--找不到的话，就新建 ，并且为sourceRecord赋值-->
                        addingToTask = true;
                        sourceRecord = taskTop;
                    }
                } else if (r.realActivity.equals(taskTop.task.realActivity)) {
                        <!--如果taskTop的Task rootActivity是目标Activity，则额外，如果是Intent.FLAG_ACTIVITY_SINGLE_TOP，并自己位于栈顶 不新建-->
                    if (((launchFlags&Intent.FLAG_ACTIVITY_SINGLE_TOP) != 0
                            || r.launchMode == ActivityInfo.LAUNCH_SINGLE_TOP)
                            && taskTop.realActivity.equals(r.realActivity)) {
                        if (taskTop.frontOfTask) {
                            taskTop.task.setIntent(r.intent, r.info);
                        }
                        taskTop.deliverNewIntentLocked(callingUid, r.intent);
                        <!--如果root intent不相等，则新建Activity，并加入目标栈-->
                    } else if (!r.intent.filterEquals(taskTop.task.intent)) {
                        addingToTask = true;
                        sourceRecord = taskTop;
                    }
                }
                 ...
                 <!--以上部分主要是对Intent.FLAG_ACTIVITY_NEW_TASK的处理，有些需要直接返回的场景-->
                if (!addingToTask && reuseTask == null) {
                    if (doResume) {
                        resumeTopActivityLocked(null, options);
                    } else {
                        ActivityOptions.abort(options);
                    }
                    return ActivityManager.START_TASK_TO_FRONT;
                }
            }
        }
    }

    if (r.packageName != null) {
        ActivityRecord top = topRunningNonDelayedActivityLocked(notTop);
        if (top != null && r.resultTo == null) {
            if (top.realActivity.equals(r.realActivity) && top.userId == r.userId) {
                if (top.app != null && top.app.thread != null) {
               <!--可能不是目标task，但是是Intent.FLAG_ACTIVITY_SINGLE_TOP-->
                    if ((launchFlags&Intent.FLAG_ACTIVITY_SINGLE_TOP) != 0
                        || r.launchMode == ActivityInfo.LAUNCH_SINGLE_TOP
                        || r.launchMode == ActivityInfo.LAUNCH_SINGLE_TASK) {
                        logStartActivity(EventLogTags.AM_NEW_INTENT, top, top.task);
                        if (doResume) {
                            resumeTopActivityLocked(null);
                        }
                        ActivityOptions.abort(options);
                        if ((startFlags&ActivityManager.START_FLAG_ONLY_IF_NEEDED) != 0) {
                            return ActivityManager.START_RETURN_INTENT_TO_CALLER;
                        }
                        top.deliverNewIntentLocked(callingUid, r.intent);
                        return ActivityManager.START_DELIVERED_TO_TOP;
                    }
                }
            }
        }

    }
    ...

    boolean newTask = false;
    boolean keepCurTransition = false;

    <!--最终判断是不是要启动新Task栈-->
    if (r.resultTo == null && !addingToTask
            && (launchFlags&Intent.FLAG_ACTIVITY_NEW_TASK) != 0) {
        <!--新建Task 没找到reuseTask task 并且是launchFlags&Intent.FLAG_ACTIVITY_NEW_TASK-->
        if (reuseTask == null) {
            mService.mCurTask++;
            if (mService.mCurTask <= 0) {
                mService.mCurTask = 1;
            }
            r.setTask(new TaskRecord(mService.mCurTask, r.info, intent), null, true);
        } else {
        <!--找到Task，但是rootActivity不是目标Activiyt，则需要新建activity-->
            r.setTask(reuseTask, reuseTask, true);
        }
        newTask = true;
        if (!movedHome) {
            moveHomeToFrontFromLaunchLocked(launchFlags);
        }

    } else if (sourceRecord != null) {
            <!--非服务类启动的Activity，如果当前栈能找到目标栈的singleTop,则不新建，否则新建-->
        if (!addingToTask && (launchFlags&Intent.FLAG_ACTIVITY_CLEAR_TOP) != 0) {
            ActivityRecord top = performClearTaskLocked(
                    sourceRecord.task.taskId, r, launchFlags);
            keepCurTransition = true;
            if (top != null) {
                top.deliverNewIntentLocked(callingUid, r.intent);
                if (doResume) {
                    resumeTopActivityLocked(null);
                }
                ActivityOptions.abort(options);
                return ActivityManager.START_DELIVERED_TO_TOP;
            }
        } 
        ...
        r.setTask(sourceRecord.task, sourceRecord.thumbHolder, false);
    } else {
        final int N = mHistory.size();
        ActivityRecord prev =
            N > 0 ? mHistory.get(N-1) : null;
        r.setTask(prev != null
                ? prev.task
                : new TaskRecord(mService.mCurTask, r.info, intent), null, true);
   }
    startActivityLocked(r, newTask, doResume, keepCurTransition, options);
    return ActivityManager.START_SUCCESS;
}

```

![](//upload-images.jianshu.io/upload_images/1460468-18ac28e1a724f9b0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

### 为什么非Activity启动Activity要强制规定使用参数FLAG_ACTIVITY_NEW_TASK

从源码上说，ContextImpl在前期做了检查，如果没添加Intent.FLAG_ACTIVITY_NEW_TASK就抛出异常，

```
@Override
public void startActivity(Intent intent, Bundle options) {
    warnIfCallingFromSystemProcess();
    if ((intent.getFlags()&Intent.FLAG_ACTIVITY_NEW_TASK) == 0) {
        throw new AndroidRuntimeException(
                "Calling startActivity() from outside of an Activity "
                + " context requires the FLAG_ACTIVITY_NEW_TASK flag."
                + " Is this really what you want?");
    }
    ...
}

```

为什么要这么呢？其实直观很好理解，如果不是在Activity中启动的，那就可以看做不是用户主动的行为，也就说这个界面可能出现在任何APP之上，如果不用Intent.FLAG_ACTIVITY_NEW_TASK将其限制在自己的Task中，那用户可能会认为该Activity是当前可见APP的页面，这是不合理的。举个例子：我们在听音乐，这个时候如果邮件Service突然要打开一个Activity，如果不用Intent.FLAG_ACTIVITY_NEW_TASK做限制，那用户可能认为这个Activity是属于音乐APP的，因为用户点击返回的时候，可能会回到音乐，而不是邮件（如果邮件之前就有界面）。

# 总结

以上分析只是针对一个版本的Android，并且只涉及部分Flag，要完全理解各种组合就更麻烦了，所以所，如果面试官问题Activity启动模式的话，随便侃侃还可以，但是要以此来鄙视你，那你有90%的概率可以怼回去，怎么怼？随便几个Flag组合一下，问面试官会有什么结果，保证问死一堆 ；

![](//upload-images.jianshu.io/upload_images/1460468-af9f1129243d1269.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/450/format/webp)

####**福利时间：**

好了，今天的分享就到这里，如果你对在面试中遇到的问题，或者刚毕业及工作几年迷茫不知道该如何准备面试并突破现状提升自己，对于自己的未来还不够了解不知道给如何规划，**可以加一下下面的技术群**。**来看看同行们都是如何突破现状，怎么学习的，来吸收他们的面试以及工作经验完善自己的之后的面试计划及职业规划。**

**这里放上一个两千人的技术交流大群：[Android架构设计](https://links.jianshu.com/go?to=https%3A%2F%2Fjq.qq.com%2F%3F_wv%3D1027%26k%3D5gyv0JM)（185873940）**

**面试相关资料的也可以加这个群免费领取的~**

**PS：群内有许多技术大牛，有任何问题，欢迎广大网友一起前来交流吐槽**

> 这里放上一部分我工作以来以及参与过的大大小小的面试收集总结出来的一套**进阶学习的视频及面试专题资料包**，在这里[免费分享](https://links.jianshu.com/go?to=https%3A%2F%2Fjq.qq.com%2F%3F_wv%3D1027%26k%3D5X2c18T)给大家，主要还是希望大家在如今大环境不好的情况下面试能够顺利一点，希望可以帮助到大家~

![](https://upload-images.jianshu.io/upload_images/15233854-8cd430429871dc8e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/852/format/webp)

![](https://upload-images.jianshu.io/upload_images/15233854-530f2edd56db5462.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/835/format/webp)

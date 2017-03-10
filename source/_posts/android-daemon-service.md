---
title: Android 守护进程的实现方式
categories:
  - Develop
tags:
  - Android
  - Daemon
  - JobService
  - JobScheduler
  - Process
  - Service
  - StartForeground
id: 1488942411000
comments: true
date: 2017-03-08 11:06:51
---

>项目源码：【[lzan13](https://github.com/lzan13) / [VMDaemonService](https://github.com/lzan13/VMDaemonServices)】
>我的简书：【[lzan13](http://jianshu.com/u/f53bcbbc7c3e) / [Android 守护进程的实现方式](http://www.jianshu.com/p/b16631a2fe3c)】

在我们进行应用开发时，会遇到上级的各种需求，其中有一条 刚需：`后台保活`，更有甚者：
<blockquote class="blockquote-center">我要我们的应用永远活在用户的手机后台不被杀死
—— 这都 TM 的扯淡</blockquote>

除了系统级别的应用能持续运行，所有三方程序都有被杀死的那一天！当然`QQ/微信/陌陌`等会好一些，因为他们已经深入设备的`心`；
我们能做的只是通过各种手段尽量让我们的程序在后台运行的时间长一些，或者在被干掉的时候，能够重新站起来，而且这个也不是每次都有效的，也是不能在所有的设备的上都有效的；要做到后台进程保活，我们需要做到两方便：
1. 提高进程优先级，降低被回收或杀死概率
2. 在进程被干掉后，进行拉起

要实现实现上边所说，通过下边几点来实现，首先我们需要了解下进程的优先级划分：
### #进程的优先级
`Process Importance`记录在`ActivityManager.java`类中:
```java
/**
 * Path：SDK/sources/android-25/android/app/ActivityManager#RunningAppProcessInfo.java
 * 
 * 这个进程正在运行前台UI，也就是说，它是当前在屏幕顶部的东西，用户正在进行交互的而进程
 */
public static final int IMPORTANCE_FOREGROUND = 100;

/**
 * 此进程正在运行前台服务，即使用户不是在应用中时也执行音乐播放，这一般表示该进程正在做用户积极关心的事情
 */
public static final int IMPORTANCE_FOREGROUND_SERVICE = 125;
/**
 * 这个过程不是用户的直接意识到，但在某种程度上是他们可以察觉的。
 */
public static final int IMPORTANCE_PERCEPTIBLE = 130;

/**
 * 此进程正在运行前台UI，但设备处于睡眠状态，因此用户不可见，意思是用户意识不到的进程，因为他们看不到或与它交互，
 * 但它是相当重要，因为用户解锁设备时期望的返回到这个进程
 */
public static final int IMPORTANCE_TOP_SLEEPING = 150;

/**
 * 进程在后台，但我们不能恢复它的状态，所以我们想尽量避免杀死它，不然这个而进程就丢了
 */
public static final int IMPORTANCE_CANT_SAVE_STATE = 170;

/**
 * 此进程正在运行某些对用户主动可见的内容，但不是直接显示在UI，
 * 这可能运行在当前前台之后的窗口（因此暂停并且其状态被保存，不与用户交互，但在某种程度上对他们可见）;
 * 也可能在系统的控制下运行其他服务，
 */
public static final int IMPORTANCE_VISIBLE = 200;

/**
 * 服务进程，此进程包含在后台保持运行的服务，这些后台服务用户察觉不到，是无感知的，所以它们可以由系统相对自由地杀死
 */
public static final int IMPORTANCE_SERVICE = 300;

/**
 * 后台进程
 */
public static final int IMPORTANCE_BACKGROUND = 400;

/**
 * 空进程，此进程没有任何正在运行的代码
 */
public static final int IMPORTANCE_EMPTY = 500;

// 此过程不存在。
public static final int IMPORTANCE_GONE = 1000;
```

### #进程回收机制
了解进程优先级之后，我们还需要知道一个进程回收机制的东西；这里参考`AngelDevil`在博客园上的一篇文章:
>详情参考：【[Android Low Memory Killer](http://www.cnblogs.com/angeldevil/archive/2013/05/21/3090872.html)】

`Android`的`Low Memory Killer`基于`Linux`的`OOM`机制，在`Linux`中，内存是以页面为单位分配的，当申请页面分配时如果内存不足会通过以下流程选择bad进程来杀掉从而释放内存：

>alloc_pages -> out_of_memory() -> select_bad_process() -> badness()

在`Low Memory Killer`中通过进程的`oom_adj`与占用内存的大小决定要杀死的进程，`oom_adj`越小越不容易被杀死；
`Low Memory Killer Driver`在用户空间指定了一组内存临界值及与之一一对应的一组`oom_adj`值，当系统剩余内存位于内存临界值中的一个范围内时，如果一个进程的`oom_adj`值大于或等于这个临界值对应的`oom_adj`值就会被杀掉。


下边是表示`Process State`(即老版本里的`OOM_ADJ`)数值对照表，数值越大，重要性越低，在新版SDK中已经在`android`层去除了小于0的进程状态
```java
// Path：SDK/sources/android-25/android/app/ActivityManager#RunningAppProcessInfo.java 
// 进程不存在。 
public static final int PROCESS_STATE_NONEXISTENT = -1;
// 进程是一个持久的系统进程，一般指当前 UI 进程
public static final int PROCESS_STATE_PERSISTENT = 0;
// 进程是一个持久的系统进程，正在做和 UI 相关的操作，但不直接显示
public static final int PROCESS_STATE_PERSISTENT_UI = 1;
// 进程正在托管当前的顶级活动。请注意，这涵盖了用户可见的所有活动。 
public static final int PROCESS_STATE_TOP = 2;
// 进程由于系统绑定而托管前台服务。
public static final int PROCESS_STATE_BOUND_FOREGROUND_SERVICE = 3;
// 进程正在托管前台服务。
public static final int PROCESS_STATE_FOREGROUND_SERVICE = 4;
// 与{@link #PROCESS_STATE_TOP}相同，但设备处于睡眠状态。 
public static final int PROCESS_STATE_TOP_SLEEPING = 5;
// 进程对用户很重要，是他们知道的东西
public static final int PROCESS_STATE_IMPORTANT_FOREGROUND = 6;
// 进程对用户很重要，但不是他们知道的
public static final int PROCESS_STATE_IMPORTANT_BACKGROUND = 7;
// 进程在后台运行备份/恢复操作
public static final int PROCESS_STATE_BACKUP = 8;
// 进程在后台，但我们不能恢复它的状态，所以我们想尽量避免杀死它，不然这个而进程就丢了
public static final int PROCESS_STATE_HEAVY_WEIGHT = 9;
// 进程在后台运行一个服务，与oom_adj不同，此级别用于正常运行在后台状态和执行操作状态。 
public static final int PROCESS_STATE_SERVICE = 10;
// 进程在后台运行一个接收器，注意，从oom_adj接收器的角度来看，在较高的前台级运行，但是对于我们的优先级，这不是必需的，并且将它们置于服务之下意味着当它们接收广播时，一些进程状态中的更少的改变。 
public static final int PROCESS_STATE_RECEIVER = 11;
// 进程在后台，但主持家庭活动
public static final int PROCESS_STATE_HOME = 12;
// 进程在后台，但托管最后显示的活动
public static final int PROCESS_STATE_LAST_ACTIVITY = 13;
// 进程正在缓存以供以后使用，并包含活动
public static final int PROCESS_STATE_CACHED_ACTIVITY = 14;
// 进程正在缓存供以后使用，并且是包含活动的另一个缓存进程的客户端
public static final int PROCESS_STATE_CACHED_ACTIVITY_CLIENT = 15;
// 进程正在缓存以供以后使用，并且为空
public static final int PROCESS_STATE_CACHED_EMPTY = 16;
```

`Process State`（即老版本的`OOM_ADJ`）与`Process Importance`对应关系，这个方法也是在`ActivityManager.java`类中，有了这个关系，就知道可以知道我们的应用处于哪个级别，对于我们后边优化有个很好地参考
```java
/** 
 * Path：SDK/sources/android-25/android/app/ActivityManager#RunningAppProcessInfo.java
 *
 * 通过这个方法，将Linux底层的 OOM_ADJ级别码和 android 层面的进程重要程度联系了起来
 */
public static int procStateToImportance(int procState) {
    if (procState == PROCESS_STATE_NONEXISTENT) {
        return IMPORTANCE_GONE;
    } else if (procState >= PROCESS_STATE_HOME) {
        return IMPORTANCE_BACKGROUND;
    } else if (procState >= PROCESS_STATE_SERVICE) {
        return IMPORTANCE_SERVICE;
    } else if (procState > PROCESS_STATE_HEAVY_WEIGHT) {
        return IMPORTANCE_CANT_SAVE_STATE;
    } else if (procState >= PROCESS_STATE_IMPORTANT_BACKGROUND) {
        return IMPORTANCE_PERCEPTIBLE;
    } else if (procState >= PROCESS_STATE_IMPORTANT_FOREGROUND) {
        return IMPORTANCE_VISIBLE;
    } else if (procState >= PROCESS_STATE_TOP_SLEEPING) {
        return IMPORTANCE_TOP_SLEEPING;
    } else if (procState >= PROCESS_STATE_FOREGROUND_SERVICE) {
        return IMPORTANCE_FOREGROUND_SERVICE;
    } else {
        return IMPORTANCE_FOREGROUND;
    }
}
```

一般情况下，设备端进程被干掉有一下几种情况

| 进程结束场景                       | 结束方式                 | 影响范围                    |
|----------------------------------|-------------------------|----------------------------|
| Android 系统自身内存回收机制         | Low Memory Killer      | Process State 数值从大到小   |
| 第三方管理程序清理进程 无 Root 权限   | killBackgroundProcess  | Process State 数值大于6进程   |
| 第三方管理程序清理进程 有 Root 权限   | force-stop or Kill     | 除当前前台进程外所有非系统进程   |
| Rom 清除进程（用户手动清理）         | force-stop or Kill     | 所有非系统进程                |
| 用户手动强制结束                    | force-stop             | 第三方应用以及非 System 进程   |

由以上分析，我们可以可以总结出，如果想提高我们应用后台运行时间，就需要提高当前应用进程优先级，来减少被杀死的概率

### #守护进程的实现
分析了那么多，现在对Android自身后台进程管理，以及进程的回收也有了一个大致的了解，后边我们要做的就是想尽一切办法去提高应用进程优先级，降低进程被杀的概率；或者是在被杀死后能够重新启动后台守护进程


#### #1.模拟前台进程
第一种方式就是利用系统漏洞，使用`startForeground()`将当前进程伪装成前台进程，将进程优先级提高到最高(这里所说的最高是服务所能达到的最高，即1);

这种方式在`7.x`之前都是很好用的，QQ、微信、IReader、Keep 等好多应用都是用的这种方式实现；因为在7.x 以后的设备上，这种伪装前台进程的方式也会显示出来通知栏提醒，这个是取消不掉的，虽然`Google`现在还没有对这种方式加以限制，不过这个已经能够被用户感知到了，这种方式估计也用不了多久了

下边看下实现方式，这边这个`VMDaemonService`就是一个守护进程服务，其中在服务的`onStartCommand()`方法中调用`startForeground()`将服务进程设置为前台进程，当运行在 API18 以下的设备是可以直接设置，API18 以上需要实现一个内部的`Service`，这个内部类实现和外部类同样的操作，然后结束自己；当这个服务启动后就会创建一个定时器去发送广播，当我们的核心服务被干掉后，就由另外的广播接收器去接收我们守护进程发出的广播，然后唤醒我们的核心服务；
```java

/**
 * 以实现内部 Service 类的方式实现守护进程，这里是利用 android 漏洞提高当前进程优先级
 *
 * Created by lzan13 on 2017/3/7.
 */
public class VMDaemonService extends Service {

    private final static String TAG = VMDaemonService.class.getSimpleName();

    // 定时唤醒的时间间隔，这里为了自己测试方边设置了一分钟
    private final static int ALARM_INTERVAL = 1 * 60 * 1000;
    // 发送唤醒广播请求码
    private final static int WAKE_REQUEST_CODE = 5121;
    // 守护进程 Service ID
    private final static int DAEMON_SERVICE_ID = -5121;

    @Override public void onCreate() {
        Log.i(TAG, "VMDaemonService->onCreate");
        super.onCreate();
    }

    @Override public int onStartCommand(Intent intent, int flags, int startId) {
        Log.i(TAG, "VMDaemonService->onStartCommand");
        // 利用 Android 漏洞提高进程优先级，
        startForeground(DAEMON_SERVICE_ID, new Notification());
        // 当 SDk 版本大于18时，需要通过内部 Service 类启动同样 id 的 Service
        if (Build.VERSION.SDK_INT >= 18) {
            Intent innerIntent = new Intent(this, DaemonInnerService.class);
            startService(innerIntent);
        }

        // 发送唤醒广播来促使挂掉的UI进程重新启动起来
        AlarmManager alarmManager = (AlarmManager) getSystemService(Context.ALARM_SERVICE);
        Intent alarmIntent = new Intent();
        alarmIntent.setAction(VMWakeReceiver.DAEMON_WAKE_ACTION);

        PendingIntent operation = PendingIntent.getBroadcast(this, WAKE_REQUEST_CODE, alarmIntent,
                PendingIntent.FLAG_UPDATE_CURRENT);

        alarmManager.setInexactRepeating(AlarmManager.RTC_WAKEUP, System.currentTimeMillis(),
                ALARM_INTERVAL, operation);

        /**
         * 这里返回值是使用系统 Service 的机制自动重新启动，不过这种方式以下两种方式不适用：
         * 1.Service 第一次被异常杀死后会在5秒内重启，第二次被杀死会在10秒内重启，第三次会在20秒内重启，一旦在短时间内 Service 被杀死达到5次，则系统不再拉起。
         * 2.进程被取得 Root 权限的管理工具或系统工具通过 forestop 停止掉，无法重启。
         */
        return START_STICKY;
    }

    @Override public IBinder onBind(Intent intent) {
        // TODO: Return the communication channel to the service.
        throw new UnsupportedOperationException("onBind 未实现");
    }

    @Override public void onDestroy() {
        Log.i(TAG, "VMDaemonService->onDestroy");
        super.onDestroy();
    }

    /**
     * 实现一个内部的 Service，实现让后台服务的优先级提高到前台服务，这里利用了 android 系统的漏洞，
     * 不保证所有系统可用，测试在7.1.1 之前大部分系统都是可以的，不排除个别厂商优化限制
     */
    public static class DaemonInnerService extends Service {

        @Override public void onCreate() {
            Log.i(TAG, "DaemonInnerService -> onCreate");
            super.onCreate();
        }

        @Override public int onStartCommand(Intent intent, int flags, int startId) {
            Log.i(TAG, "DaemonInnerService -> onStartCommand");
            startForeground(DAEMON_SERVICE_ID, new Notification());
            stopSelf();
            return super.onStartCommand(intent, flags, startId);
        }

        @Override public IBinder onBind(Intent intent) {
            // TODO: Return the communication channel to the service.
            throw new UnsupportedOperationException("onBind 未实现");
        }

        @Override public void onDestroy() {
            Log.i(TAG, "DaemonInnerService -> onDestroy");
            super.onDestroy();
        }
    }
}
```

当我们启动这个守护进程的时候，就可以使用以下`adb`命令查看当前程序的进程情况（需要`adb shell`进去设备），
为了等下区分进程优先级，我启动了一个普通的后台进程，两外两个一个是我们启动的守护进程，一个是当前程序的核心进程，可以看到除了后台进程外，另外两个进程都带有`isForeground=true`的属性：
```bash
# 这个命令的 services 可以换成 service，这样会只显示当前，进程，不显示详细内容
# dumpsys activity services <Your Package Name>
root@vbox86p:/ # dumpsys activity services com.vmloft.develop.daemon           
ACTIVITY MANAGER SERVICES (dumpsys activity services)
  User 0 active services:
  * ServiceRecord{170fe1dd u0 com.vmloft.develop.daemon/.services.VMDaemonService}
    intent={cmp=com.vmloft.develop.daemon/.services.VMDaemonService}
    packageName=com.vmloft.develop.daemon
    processName=com.vmloft.develop.daemon:daemon
    baseDir=/data/app/com.vmloft.develop.daemon-1/base.apk
    dataDir=/data/data/com.vmloft.develop.daemon
    app=ProcessRecord{173fe77f 2370:com.vmloft.develop.daemon:daemon/u0a68}
    isForeground=true foregroundId=-5121 foregroundNoti=Notification(pri=0 contentView=com.vmloft.develop.daemon/0x1090077 vibrate=null sound=null defaults=0x0 flags=0x62 color=0xff607d8b vis=PRIVATE)
    createTime=-6s196ms startingBgTimeout=--
    lastActivity=-6s157ms restartTime=-6s157ms createdFromFg=true
    startRequested=true delayedStop=false stopIfKilled=false callStart=true lastStartId=1

  * ServiceRecord{2fee4f84 u0 com.vmloft.develop.daemon/.services.VMCoreService}
    intent={cmp=com.vmloft.develop.daemon/.services.VMCoreService}
    packageName=com.vmloft.develop.daemon
    processName=com.vmloft.develop.daemon
    baseDir=/data/app/com.vmloft.develop.daemon-1/base.apk
    dataDir=/data/data/com.vmloft.develop.daemon
    app=ProcessRecord{18c6a1b4 2343:com.vmloft.develop.daemon/u0a68}
    isForeground=true foregroundId=-5120 foregroundNoti=Notification(pri=0 contentView=com.vmloft.develop.daemon/0x1090077 vibrate=null sound=null defaults=0x0 flags=0x62 color=0xff607d8b vis=PRIVATE)
    createTime=-28s136ms startingBgTimeout=--
    lastActivity=-28s136ms restartTime=-28s136ms createdFromFg=true
    startRequested=true delayedStop=false stopIfKilled=false callStart=true lastStartId=1

  * ServiceRecord{2ef6909e u0 com.vmloft.develop.daemon/.services.VMBackgroundService}
    intent={cmp=com.vmloft.develop.daemon/.services.VMBackgroundService}
    packageName=com.vmloft.develop.daemon
    processName=com.vmloft.develop.daemon:background
    baseDir=/data/app/com.vmloft.develop.daemon-1/base.apk
    dataDir=/data/data/com.vmloft.develop.daemon
    app=ProcessRecord{29f8734c 2388:com.vmloft.develop.daemon:background/u0a68}
    createTime=-3s279ms startingBgTimeout=--
    lastActivity=-3s262ms restartTime=-3s262ms createdFromFg=true
    startRequested=true delayedStop=false stopIfKilled=false callStart=true lastStartId=1
```

然后我们可以用下边的命令查看`ProcessID`
```bash
# 这个命令可以查看当前DProcessID（数据结果第二列），我们可以看到当前程序有两个进程
# ps | grep com.vmloft.develop.daemon
root@vbox86p:/ # ps | grep com.vmloft.develop.daemon
u0_a68    2343  274   1012408 42188 ffffffff f74f1b45 S com.vmloft.develop.daemon
u0_a68    2370  274   997012 26152 ffffffff f74f1b45 S com.vmloft.develop.daemon:daemon
u0_a68    2388  274   997012 25668 ffffffff f74f1b45 S com.vmloft.develop.daemon:background
```

有了`ProcessID`之后，我们可以根据这个`ProcessID`获取到当前进程的优先级状态`Process State`，对应`Linux`层的`oom_adj`
可以看到当前核心进程的级别为`0`，因为这个表示当前程序运行在前台 UI 界面，守护进程级别为`1`，因为我们利用漏洞设置成了前台进程，虽然不可见，但是他的级别也是比较高的，仅次于前台 UI 进程，然后普通后台进程级别为`4`；当我们退到后台时，可以看到核心进程的级别变为`1`了，这就是因为我们利用`startForeground()`将进程设置成前台进程的原因，这样就降低了进程被系统回收的概率了；
```bash
# 这个命令就是通过 ProcessID 输出其对应 oom_adj
# cat /proc/ProcessID/oom_adj
# 程序在前台时，查询进程级别
root@vbox86p:/ # cat /proc/2343/oom_adj                                      
0
root@vbox86p:/ # cat /proc/2370/oom_adj                                        
1
root@vbox86p:/ # cat /proc/2388/oom_adj                                        
4
# 当程序退到后台时，再次查看进程级别
root@vbox86p:/ # cat /proc/2343/oom_adj                                      
1
root@vbox86p:/ # cat /proc/2370/oom_adj                                        
1
root@vbox86p:/ # cat /proc/2388/oom_adj                                        
4
```

可以看到这种方式确实能够提高进程优先级，但是在一些国产的设备上还是会被杀死的，比我我测试的时候小米点击清空最近运行的应用进程就别干掉了；当把应用加入到设备白名单里就不会被杀死了，微信就是这样，人家直接装上之后就已经在白名单里了，我们要做的就是在用户使用中引导他们将我们的程序设置进白名单，将守护进程和白名单结合起来，这样才能保证我们的应用持续或者


#### #2.JobScheduler机制唤醒
Android系统在5.x以上版本提供了一个`JobSchedule`接口，系统会根据自己实现定时去调用改接口传递的进程去实现一些操作，而且这个接口在被强制停止后依然能够正常的启动；不过在一些国产设备上可能无效，比如小米；
下边是 JobServcie 的实现：
```java
/**
 * 5.x 以上使用 JobService 实现守护进程，这个守护进程要做的工作很简单，就是启动应用的核心进程
 * Created by lzan13 on 2017/3/8.
 */
@TargetApi(Build.VERSION_CODES.LOLLIPOP) public class VMDaemonJobService extends JobService {

    private final static String TAG = VMDaemonJobService.class.getSimpleName();

    @Override public boolean onStartJob(JobParameters params) {
        Log.d(TAG, "onStartJob");
        // 这里为了掩饰直接启动核心进程，没有做其他判断操作
        startService(new Intent(getApplicationContext(), VMCoreService.class));
        return false;
    }

    @Override public boolean onStopJob(JobParameters params) {
        Log.d(TAG, "onStopJob");
        return false;
    }
}
```
我们要做的就是在需要的时候调用`JobSchedule`的`schedule`来启动任务；剩下的就不需要关心了，`JobSchedule`会帮我们做好，下边就是我这边实现的启动任务的方法：
```java
/**
 * 5.x以上系统启用 JobScheduler API 进行实现守护进程的唤醒操作
 */
@TargetApi(Build.VERSION_CODES.LOLLIPOP) 
private void startJobScheduler() {
    int jobId = 1;
    JobInfo.Builder jobInfo = new JobInfo.Builder(jobId, new ComponentName(this, VMDaemonJobService.class));
    jobInfo.setPeriodic(10000);
    jobInfo.setPersisted(true);
    JobScheduler jobScheduler = (JobScheduler) getSystemService(Context.JOB_SCHEDULER_SERVICE);
    jobScheduler.schedule(jobInfo.build());
}
```

#### #3.系统 Service START_STICKY 机制重启
在实现`Service`类时，将`onStartCommand()`返回值设置为`START_STICKY`，利用系统机制在`Service`挂掉后自动拉活；不过这种方式只适合比较原生一些的系统，像小米，华为等这些定制化比较高的第三方厂商，他们都已经把这些给限制掉了；
```java
@Override 
public int onStartCommand(Intent intent, int flags, int startId) {
    Log.i(TAG, "VMDaemonService->onStartCommand");
    /**
     * 这里返回值是使用系统 Service 的机制自动重新启动，不过这种方式以下两种方式不适用：
     * 1.Service 第一次被异常杀死后会在5秒内重启，第二次被杀死会在10秒内重启，第三次会在20秒内重启，一旦在短时间内 Service 被杀死达到5次，则系统不再拉起。
     * 2.进程被取得 Root 权限的管理工具或系统工具通过 forestop 停止掉，无法重启。
     * 3.一些定制化比较高的第三方系统也不适用
     */
    return START_STICKY;
}
```
这种方式在以下两种情况无效：
- `Service`第一次被异常杀死后会在`5`秒内重启，第二次被杀死会在`10`秒内重启，第三次会在`20`秒内重启，一旦在短时间内`Service`被杀死达到`5`次，这个服务就不能再次重启了；
- 进程被取得`Root`权限的管理工具或系统工具通过`fores-top`方式停止掉，无法重启;
- 一些定制化比较高的第三方系统也不适用


#### #4.其他保活方式 
>- 利用 Native 本地进程，这个主要使用到 jni 调用底层实现，而且在 Android 5.x 以后对这个限制也比较高，不适用了，暂时不研究
>- 集成第三方SDK互相唤醒，这个只要正常集成了第三方的SDK，并使用了他们对应的服务，当一个设备安装的多个应用都集成了某一个第三方SDK时，启动任意一个 app 都会唤醒其他的 app，不过这个在一些新版的国内厂商系统也是做了限制，这种方式并没有什么效果
>- 一像素的 Activity 方式（流氓方式），经测试一些手机系统无法检测到解锁和锁屏，不确定是否系统修改了解锁或者锁屏的广播，还是禁用了这些广播，因此此方式无效；

### #结语
事事没有绝对，万物总有一些漏洞，就算上边的那些方式不可用了，后边肯定还会出现其他的方式；我们不能保证我们的应用不死，但我们可以提高存活率；

其实最好的方式还是把程序做好，让程序本身深入人心，别人喜欢你了，就算你被干掉了，他们也会主动的把你拉起来，然后把你加入他们的白名单，然后我们的目的就实现了不是 😁 ~


### #参考资料
- 【[腾讯Bugly干货分享 Android 进程保活招式大全](https://segmentfault.com/a/1190000006251859)】
- 【[Android 官方文档-进程和线程](https://developer.android.com/guide/components/processes-and-threads.html)】
- 【[Android Low Memory Killer](http://www.cnblogs.com/angeldevil/archive/2013/05/21/3090872.html)】
- 【[关于 Android 进程保活，你所需要知道的一切](http://www.jianshu.com/p/63aafe3c12af)】

---

layout: post
title: java wait/notify/notifyAll使用
category: code

---

> JAVA的进程同步是通过synchronized()来实现的,上述三个方法都需要和synchronized(obj)一起使用.

<!-- more -->

在JAVA中的Object类型中,都是带有一个内存锁的,在有线程获取该内存锁后,

其它线程无法访问该内存,从而实现JAVA中简单的同步、互斥操作.

# wait

{% highlight java %}

synchronized(obj) {
    obj.wai();
}

{% endhighlight %}

通俗点来讲就是这条线程开始等待obj了,当obj唤醒了以后这条线程就有机会获取到它的控制权了.

# notify

{% highlight java %}

synchronized(obj) {
    obj.notify();
}

{% endhighlight %}

notify(通知),正如它的翻译,调用这个方法的线程通知其他线程这个对象可以用啦,

然后正在等待的线程中会有某一条被唤醒并获取到obj的控制权,但具体哪条被唤醒要看jvm的调度,具有不确定性.


#notifyAll

{% highlight java %}

synchronized(obj) {
    obj.notifyAll();
}

{% endhighlight %}

调用这个方法的线程通知所有正在等待的线程,被唤醒的线程重新竞争获取到obj的控制权,这个方法比notify用得更多,

因为我们不知道具体要唤醒哪条线程,还不如全部唤醒让他们重新去抢控制权.

> 一个比较详细的例子:[http://blog.csdn.net/zyplus/article/details/6672775](http://blog.csdn.net/zyplus/article/details/6672775)

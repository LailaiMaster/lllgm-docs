interface Executor  这个接口的含义是线程的执行与定义是分开的。

ExecutorService 继承Executor 定义了一些关于线程池的生命周期的方法。

ThreadPoolExecutor 

Callable 类似Runable，不过Callable可以有返回值。

# Future和FutureTask

FutureTask既是Future也是runable

使用对比⬇️：

![图片](https://uploader.shimo.im/f/KTGAT0Fl4QIsRzKe.png!thumbnail)


# CompletableFuture

![图片](https://uploader.shimo.im/f/CEHh5yw1qI0nXR22.png!thumbnail)



# 线程池

## ThreadPoolExcutor

### 自定义线程池

![图片](https://uploader.shimo.im/f/NfwjgNs8t5kzLC4k.png!thumbnail)

参数：

核心线程数

最大线程

存活时间 keepAliveTime  归还操作系统时间，核心线程不归还。

BlockingQueue  ArrayBlockingQueue(4)最大4个任务 SynchroziedQueue 

Executors.defaultThreadFactory()线程工厂可以自定义工厂，指定线程名称，方便出错回溯。

拒绝策略。 线程池忙且任务队列满，执行拒绝策略。jdk默认提供4中，可以自定义「

Abort:抛异常

Discard:扔掉，不抛异常

DiscardOldest:扔掉排队时间最久的

CallerRuns:调用者处理任务

」

## 默认线程池

### *newSingleThreadExecutor *

*为什么要有单线程的线程池？1.线程有任务队列2.生命周期管理*

```
//实现
new ThreadPoolExecutor(1, 1, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue())
```
### *newCachedThreadPool*

```
new ThreadPoolExecutor(0, 2147483647, 60L, TimeUnit.SECONDS, new SynchronousQueue())
```
SynchronousQueue是0容量的Queue。
来个任务就就起个新的线程，马上执行，没有线程空着就起一个线程。一般不会使用这个，线程太多。

### newFixedThreadPool

```
new ThreadPoolExecutor(nThreads, nThreads, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue())
```
固定线程池。
Cache vs Fixed

### newScheduledThreadPool

定时任务线程池 quartz

![图片](https://uploader.shimo.im/f/S1hfKLr5pGkYbXfN.png!thumbnail)

## ForkJoinPool

分解汇总的任务

用很少的线程可以执行很多的任务(子任务) TPE做不到先执行子任务

CPU密集型

### *newWorkStealingPool*

![图片](https://uploader.shimo.im/f/Wsi4c7805NQ7GFdD.png!thumbnail)







![图片](https://uploader.shimo.im/f/wtgkzqQKgIMTfcRw.png!thumbnail)

# 源码分析

addworker


堆内存线程共享区域，每个线程有自己的工作空间，什么时候副本写回堆内存，不确定。

对一个线程副本的修改，并不能及时的同步到另一个线程中。

1.保证线程可见 

MESI

缓存一致性协议

2.禁止指令重排序（CPU）

Double Check Lock

![图片](https://uploader.shimo.im/f/5jnAR8Bbh2AEjJ2p.png!thumbnail)

**要不要加volatile？要加！指令重排序的问题**。JVM new对象的过程：1.申请内存，赋默认值

2.成员变量初始值 3.赋值。如果指令重排序，有可能在第二步的时候 已经把变量的值 赋给栈内引用了。











![图片](https://uploader.shimo.im/f/gPz5y15CTtgaMgmG.png!thumbnail) 

lock(锁定)：将一个变量标识为被一个线程独占状态。

unlock(解锁)：将一个变量从独占状态释放出来，释放后的变量才可以被其他线程锁定。

read(读取)：将一个变量的值从主内存传输到工作内存中，以便随后的load操作。

load(载入)：把read操作从主内存中得到的变量值放入工作内存的变量的副本中。

use(使用)：把工作内存中的一个变量的值传给执行引擎，每当虚拟机遇到一个使用到变量的指令时都会使用该指令。

assign(赋值)：把一个从执行引擎接收到的值赋给工作内存中的变量，每当虚拟机遇到一个给变量赋值的指令时，都要使用该操作。

store(存储)：把工作内存中的一个变量的值传递给主内存，以便随后的write操作。

write(写入)：把store操作从工作内存中得到的变量的值写到主内存中的变量。

![图片](https://uploader.shimo.im/f/siZMuGC6qgQfxfxk.png!thumbnail)


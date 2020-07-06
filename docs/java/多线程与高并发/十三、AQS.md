AbstractQueuedSynchronizer


volatile state+双向链表



VarHandle 普通属性原子操作


公平锁和非公平锁主要区别是tryAcquire方法

非公平锁  tryAcquire  cas操作得到锁直接下一步，如果没得到就加入队列，加队列使用到cas，去保证线程安全，wait等待唤醒后继续cas

公平锁  traAcquire判断当前线程是不是队列头，是的话cas得到锁 不是的话 加队列

加队列是都有的

加队列的时候死循环去tryAcquire 因为try实现不同所以有了公平不公平之分


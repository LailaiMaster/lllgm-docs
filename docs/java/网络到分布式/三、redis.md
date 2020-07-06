![图片](https://uploader.shimo.im/f/UuWEBNb8z4Y2zYki.png!thumbnail)**数据库:表很大，性能下降?**

>**如果表有索引**
>**增删改变慢**
>**查询速度呢?**
>**1，1个或少量查询依然很快**
>**2，井发大的吸侯会受硬盘带宽影响速度**

 1.5M ops/s 秒级十万操作。 1.5M=1500KB

![图片](https://uploader.shimo.im/f/9hn5S6oNXEkNUTct.png!thumbnail)

redis的类型意义在哪？

>计算向数据移动

![图片](https://uploader.shimo.im/f/eJpkAoM6OIQyjpJa.png!thumbnail)


# 安装

CentOS 6.5



# **redis是单线程，并发请求很多，如何变得很快？**

***redis和内核是epoll连接***

![图片](https://uploader.shimo.im/f/z6UXfiXhMAoQo0Bs.png!thumbnail)

## BIO

![图片](https://uploader.shimo.im/f/UGGMFnBEYiUgz4MA.png!thumbnail)

### 问题

每处理以一个fd（文件描述符），**都要一个线程或者进程**，成本太高了。

JVM一个线程成本1MB（默认）

1.线程调度CPU浪费

2.内存成本

## **NIO**

内核跃迁

内核提供了read方法，socket可以 nonblock   （fb文件描述符 noblock），因此，不需要每读一个fd都要建一个线程/进程，轮训调用fb。**同步非阻塞**。

![图片](https://uploader.shimo.im/f/bZoAvyMeJ4AcGiZv.png!thumbnail)

### 问题

技术是进步的，NIO有什么问题？

如果有1000个fd，代表用户进程轮训调用1000词kernel，**用户态和内核态切换成本太高了**，如何解决？

## 多路复用NIO

减少系统调用，因此内核需要发展，把循环调用放到内核中。内核提供了select（），select返回的有结果的fd，再调用read。

![图片](https://uploader.shimo.im/f/omYwFbiCBQw4qJKT.png!thumbnail)


### 问题

向内核传1000个fd，文件描述符数据在内核和用户空间疯狂拷贝。粒度太大。·

mmap，**共享空间**，减少fd拷贝。

  epoll是在select的基础上，改良了几个不够高效的点，引用了先进的数据结构，实现了更高效的多路复用。

![图片](https://uploader.shimo.im/f/mMABBpmL2PQB09Ld.png!thumbnail)

## 

## NIO及epoll

## NIO

  NIO(new IO) ，这个new是相当于BIO来说的，BIO(Blocking IO)是同步阻塞式IO，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销。为了降低开销的问题，就产生了NIO，同步非阻塞式IO，服务器实现模式为一个请求一个线程，即客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到连接有I/O请求时才启动一个线程进行处理。

epoll

  epoll是在select的基础上，改良了几个不够高效的点，引用了先进的数据结构，实现了更高效的多路复用。



## AIO


























附录

零拷贝

![图片](https://uploader.shimo.im/f/QAZkqVZgahEHmiSz.png!thumbnail)

# 使用

nx 不存在的时候才能写

xx 只有存在的时候才能操作

## redis命令分组





# String

总

![图片](https://uploader.shimo.im/f/pdlcWh3jxKoN1A0L.png!thumbnail)


![图片](https://uploader.shimo.im/f/X5gA0QNRkJQq3I6N.png!thumbnail)

![图片](https://uploader.shimo.im/f/kd1Dd3xhRpk7dv37.png!thumbnail)


![图片](https://uploader.shimo.im/f/LOKzJuQ1C78nJUOQ.png!thumbnail)

key其实是个对象

type 判断你的命令是否正确，不至于让你String去做加法，快速返回是否有这个操作。

encoding 

## 数值操作


## 二进制安全

![图片](https://uploader.shimo.im/f/qbJcYE4MofoWrRDE.png!thumbnail)![图片](https://uploader.shimo.im/f/wLZs8nPLILgDSbi2.png!thumbnail)![图片](https://uploader.shimo.im/f/H48w7kxfSDANynPI.png!thumbnail)![图片](https://uploader.shimo.im/f/MZ5kgNin3C4pIfYt.png!thumbnail)

底层是字节存的，通过ecoding预判断类型。

![图片](https://uploader.shimo.im/f/tqRmJkys5o4Ena4r.png!thumbnail)

getset

msetnx

setbit

## bitmap

setbit 

setop 

bitcount

![图片](https://uploader.shimo.im/f/uASk6ejxVLYrIvB6.png!thumbnail)

设计 要么key为用户

要么key为时间 位为用户

# list

lpush lpop

rpush rpop

lrange

linsert

lrem 

blpop brpop 阻塞 单播队列

![图片](https://uploader.shimo.im/f/YBKIzjwfYxUiopE7.png!thumbnail)



ex：

![图片](https://uploader.shimo.im/f/hbadgMTo7BYHtdxZ.png!thumbnail)![图片](https://uploader.shimo.im/f/I94Ba1WIhTw8adVM.png!thumbnail)

left 左侧先入栈。

![图片](https://uploader.shimo.im/f/FASs7FuCfIUSWi6m.png!thumbnail)![图片](https://uploader.shimo.im/f/ObO8ypedQ9YA6fb1.png!thumbnail)

![图片](https://uploader.shimo.im/f/0lxkOWQqtSgyKQ7q.png!thumbnail)![图片](https://uploader.shimo.im/f/g1WQnclQfpgyI5QS.png!thumbnail)

lpush 往上加，往左加

rpush 往后加，往右加

![图片](https://uploader.shimo.im/f/07brGaNBdeA9Bxss.png!thumbnail)

从宏观上看，

**同向操作lpush lpop 是栈操作，rpush rpop是栈操作**

**反向操作是队列操作。**

# hash

![图片](https://uploader.shimo.im/f/48QpWAtrXKEcrR9M.png!thumbnail)

![图片](https://uploader.shimo.im/f/k3iOUDrMqtEL2M4a.png!thumbnail)

![图片](https://uploader.shimo.im/f/dgbs7glmdp4mmySN.png!thumbnail)

计算

![图片](https://uploader.shimo.im/f/LnqQLaFwhEAv4ZKZ.png!thumbnail)



# set

![图片](https://uploader.shimo.im/f/jPyJs6QM8oIPWfDe.png!thumbnail)

set和list

list可以重复出现 有序（插入顺序）

set去重 无序

![图片](https://uploader.shimo.im/f/P3ynqnKbDr8oL0mf.png!thumbnail)

smembers交集 

 SUBSCRIBE并集 

sdiff差集

![图片](https://uploader.shimo.im/f/0nXjWJA6m58j67DN.png!thumbnail)

随机事件

SRANDMEMBER

# sorted set

有序 排序

![图片](https://uploader.shimo.im/f/iZav4L2zwMoWoWfo.png!thumbnail)

跳表

Z

zadd 

![图片](https://uploader.shimo.im/f/IzioVEtHl1YfLRDM.png!thumbnail)


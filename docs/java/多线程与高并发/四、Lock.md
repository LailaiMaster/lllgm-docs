ReentrantLock

可重入锁

可以替代synchroized  用lock.lock(),注意一定要unlock.不会自动释放锁，try finally。

比sync强大的地方

1.**tryLock**尝试锁定 lock.tryLock(5,TimeUnit.SECONDS),5秒钟之内拿到锁，拿不到就不拿了。

2.**lockInterruptibly 可以对interrupt作出响应。**可以被打断的加锁。

3.**公平锁** 构造是传入true。先到先得。检查队列。

```
/**
 * 面试题：写一个固定容量同步容器，拥有put和get方法，以及getCount方法
 * 能够支持两个生产者线程以及10个消费者线程的阻塞调用
 *
 * 使用Lock和Condition来实现
 * 对比两种方式，condition的方式可以更加精确的指定哪些线程被唤醒
 */
public class MyContainer2<T> {
    final private LinkedList<T> lists=new LinkedList<T>();
    final private int MAX=10;   //最多10个元素
    private int count=0;


    private Lock lock=new ReentrantLock();
    private Condition producer=lock.newCondition();
    private Condition consumer=lock.newCondition();


    public void put(T t){
        lock.lock();
        try {
            while (lists.size()==MAX){  //想想为什么用while而不是用if
                producer.await();
            }
            lists.add(t);
            ++count;
            consumer.signalAll();//通知消费者线程进行消费
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }


    public T get(){
        T t=null;
        lock.lock();
        try {
            while (lists.size()==0){
                consumer.await();
            }
            t=lists.removeFirst();
            count--;
            producer.signalAll();   //通知生产者进行生产
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
        return t;
    }


    public static void main(String[] args) {
        MyContainer2<String> c =new MyContainer2<>();

        //启动消费者线程
        for (int i = 0; i < 10; i++) {
            new Thread(()->{
                for (int j = 0; j < 5; j++) {
                    System.out.println(c.get());
                }
            },"c"+i).start();
        }


        try {
            TimeUnit.SECONDS.sleep(2);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }


        //启动生产者线程
        for (int i = 0; i < 2; i++) {
            new Thread(()->{
                for (int j = 0; j < 25; j++) {
                    c.put(Thread.currentThread().getName()+" "+j);
                }
            },"p"+i).start();
        }
    }
}
```


reentrantLock的加锁过程

Sync是AQS的子类

lock -->Sync的acquire---》AQS的acquire

![图片](https://uploader.shimo.im/f/z7IXaFOAS6Y0NisO.png!thumbnail)

不管公平还是不公平 上面的步骤是一样的，

主要区别在于tryAcquire实现不同

# 公平锁：

![图片](https://uploader.shimo.im/f/auXxHzi0wysrsQyj.png!thumbnail)

判断是否有前节点，有true，没有false

![图片](https://uploader.shimo.im/f/bJMSNWsNEOoNDSHJ.png!thumbnail)

没有前节点就去CAS操作设置State，成功就获得锁，失败就加入队列。

![图片](https://uploader.shimo.im/f/CRlIlg4IzJUXvUyR.png!thumbnail)

入队逻辑是相同的，

前节点是head的话，再次去tryAcquire，成功就获取到锁

失败就sholdParkAfterFailedAcquire 阻塞线程

![图片](https://uploader.shimo.im/f/f7j61g3n5QI2FQqV.png!thumbnail)

阻塞在park这里 直到unpark


# 非公平锁的tryAcquire

![图片](https://uploader.shimo.im/f/Zb7UBbHQXdEuMh7t.png!thumbnail)


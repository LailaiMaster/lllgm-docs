LockSupport.park()当前线程阻塞（synchroized wait 是需要锁的）

LopckSupport.unpark()停掉的线程继续执行

**unpark可以在park之前调用。**

```
public class LockSupportDemo {

    public static Object u = new Object();
    static ChangeObjectThread t1 = new ChangeObjectThread("t1");
    static ChangeObjectThread t2 = new ChangeObjectThread("t2");

    public static class ChangeObjectThread extends Thread {
        public ChangeObjectThread(String name) {
            super(name);
        }
        @Override public void run() {
            synchronized (u) {
                System.out.println("in " + getName());
                LockSupport.park();
                if (Thread.currentThread().isInterrupted()) {
                    System.out.println("被中断了");
                }
                System.out.println("继续执行");
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        t1.start();
        Thread.sleep(1000L);
        t2.start();
        Thread.sleep(3000L);
        System.out.println("----");
//        t1.interrupt();
        LockSupport.unpark(t1);
        LockSupport.unpark(t2);
//        t1.join();
//        t2.join();
    }
}
```


unpark 先于park调用执行

![图片](https://uploader.shimo.im/f/mjxpnhWHQRYRkIJU.png!thumbnail)


```
public class T13_TestLockSupport {
    public static void main(String[] args) {
        Thread t = new Thread(()->{
            for (int i = 0; i < 10; i++) {
                System.out.println(i);
                if(i == 5) {
                    LockSupport.park();
                }
                try {
                    TimeUnit.SECONDS.sleep(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });

        t.start();

        LockSupport.unpark(t);

        /*try {
            TimeUnit.SECONDS.sleep(8);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("after 8 senconds!");

        LockSupport.unpark(t);*/

    }
}
```

交换器

Exchanger<String> exchanger=new Exchanger()

![图片](https://uploader.shimo.im/f/dOgBaWvBh9s5mPci.png!thumbnail)

![图片](https://uploader.shimo.im/f/haU1V6IZHqMiNBFy.png!thumbnail)

exchange()阻塞。直到T2线程exchange交换了，两个线程继续执行。

场景：

游戏交换装备

```
public class T12_TestExchanger {

    static Exchanger<String> exchanger = new Exchanger<>();

    public static void main(String[] args) {
        new Thread(()->{
            String s = "T1";
            try {
                s = exchanger.exchange(s);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + " " + s);

        }, "t1").start();


        new Thread(()->{
            String s = "T2";
            try {
                s = exchanger.exchange(s);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + " " + s);

        }, "t2").start();


    }
}
```

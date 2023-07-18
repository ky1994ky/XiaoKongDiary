# 问题:谈谈你对volatile的理解

## 回答:

volatile字面翻译,就是不稳定的,是JAVA语法中的一个关键字,为实例字段的同步访问提供了一种免锁机制,如果声明一个字段为volatile,那么不同线程之间对该字段的读写操作是正确的和同步的。

使用volatile,比起使用显示的创建锁和使用synchronized来讲,代码会更加简洁,同时,性能开销也会更小

volatile保证了可见性,具体来说，当一个线程对volatile修饰的变量进行写操作时，它会将新值立即写入主内存，并且会强制其他线程将本地缓存中该变量的值置为无效，以确保其他线程在下一次读取该变量时从主内存中获取最新的值。同样，当一个线程对volatile修饰的变量进行读操作时，它会直接从主内存中获取最新的值，而不是使用本地缓存中的旧值。这样可以确保所有线程对该变量的读操作都能看到最新的值。

volatile关键字通过内存屏障禁止指令重排

volatile不能保证原子性,比如i++这类非原子操作,是无法通过volatile保证原子性的

## 代码验证

### 一:可见性验证

代码:

```java
    // 可见性验证
    private static boolean sign = true;  

    public static void main(String[] args) {
        new Thread(() -> {
            while (sign) {
            }
            System.out.println("发现sign的值变化,结束程序"); 
        }).start();
        new Thread(() -> {
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            sign = false;
            System.out.println("改变sign的值");
        }).start();
    }
```

当我们不添加volatile关键字的时候,程序没法正常结束,这是因为,上面的线程无法发现sign值的修改,仍然取得是本地缓存的旧址,而加了volatile后,sign值的变化对于上面的线程,是可见的,所以程序正常退出.

### 二:不能保证原子性验证

代码:

```java
// 不能保证原子性验证
    private static volatile int num;
    public static void main(String[] args) {
        for (int i = 0; i < 1000; i++) {
            new Thread(()->{
                for (int j = 0; j < 1000; j++) {
                    num++;
                }
                System.out.println(num);
            }).start();
        }
    }
```

我们可以通过字节码文件看出,num++并不是一个原子操作

![](/Users/kongyao/Library/Application Support/typora-user-images/image-20230718002820021.png)

这段代码的输出结果,一定是小于1000000的,原因就是非原子性操作导致的,多个线程,都读到了最新的值,然后都去+1,写会到num中

### 三:禁止指令重拍

```
// 禁止指令重排
    private static int x = 0;
    private static int y = 0;
    private static int a = 0;
    private static int b = 0;
    private static int count = 0;
    public static void main(String[] args) throws InterruptedException {
        for(;;) {
            Thread thread1 = new Thread(() -> {
                a = 1;
                x = b;
            });
            Thread thread2 = new Thread(() -> {
                b = 1;
                y = a;
            });
            thread1.start();
            thread2.start();
            thread1.join();
            thread2.join();
            count++;
            if (x == 0 && y == 0) {
                System.out.println("count =" + count + ", x = " + x + ", y = " + y);
                break;
            }else {
                System.out.println("count =" + count + ", x = " + x + ", y = " + y);
            }
            a = 0;
            b = 0;
            x = 0;
            y = 0;
        }
    }
```

这段代码,有极小的概率输出x = 0, y = 0,代表发生了指令重排,但是加上volatile后,就不会产生x = 0, y = 0 的现象了

这是不加volatile的情况(这里相当不容易,专门找了台垃圾电脑,才发生的...所以是手机拍照)

![image-20230718144527241](/Users/kongyao/Library/Application Support/typora-user-images/image-20230718144527241.png)

加上以后,程序不会停止
# synchronized的使用方式以及锁膨胀

## synchronized的使用方式

### 普通同步方法

锁的是当前实例对象

```java
		/**
     * 使用 synchronized 修饰普通方法时，锁定的是当前对象实例。
     */
    private int count1 = 0;

    public synchronized void increment1() {
        count1++;
    }
```

### 静态同步方法

```java
		/**
     * 使用 synchronized 修饰静态方法时，锁定的是类对象。
     */
    private static int count2 = 0;

    public synchronized static void increment2() {
        count2++;
    }
```

### 同步代码块

```java
		/**
     * 使用 synchronized (lock) 修饰了代码块，lock 对象用于同步，
     * 确保每次只有一个线程可以进入代码块修改 count 变量,锁住的是synchronized括号里的对象
     */
    private int count3 = 0;
    private final Object lock = new Object();

    public void increment3() {
        synchronized (lock) {
            count3++;
        }
    }
```

介绍完3种使用的方法,我们来看一下synchronized底层实现

## synchronized底层实现

Java 虚拟机中的同步(Synchronization)基于进入和退出管程(Monitor)对象实现。 无论是显式同步(有明确的 monitorenter 和 monitorexit 指令)还是隐式同步(依赖方法调 用和返回指令实现的)都是如此。

在 Java 语言中，同步用的最多的地方可能是被 synchronized 修饰的同步方法。同步方法 并不是由 monitorenter 和 monitorexit 指令来实现同步的，而是由方法调用指令读取运行时 常量池中方法的 ACC_SYNCHRONIZED 标志来隐式实现的

### 隐式实现

普通同步方法

![image-20230722160245247](/Users/kongyao/Library/Application Support/typora-user-images/image-20230722160245247.png)

静态同步方法

![image-20230722160512691](/Users/kongyao/Library/Application Support/typora-user-images/image-20230722160512691.png)

### 显示实现

同步代码块

![image-20230722161148208](/Users/kongyao/Library/Application Support/typora-user-images/image-20230722161148208.png)

monitorenter指令是在编译后插入到同步代码块的开始位置的,而monitorexit则是插入到方法结束位置以及异常处

,这就是为什么,如果在程序抛出异常,synchronized 锁也会被释放的原因,当线程执行到monitorenter指令时,会尝试获取monitor的所有权,当对象与monitor关联且持有monitor后,这个对象将处于锁定的状态.

## synchronized锁膨胀

在jdk1.6,为了减少获得锁与释放锁的性能消耗,引入了偏向锁和轻量级锁,所以,在jdk1.6中,锁一共有4种状态,级别从低到高,依次是 

1. 无锁
2. 偏向锁
3. 轻量级锁
4. 重量级锁

synchronized用的锁,是存在JAVA对象头里的Mark Word中的,在64位虚拟机下,存储结构入下图:

![image-20230723004939053](/Users/kongyao/Library/Application Support/typora-user-images/image-20230723004939053.png)

### 无锁

一个对象没有被作为锁对象，处于无锁状态.

### 偏向锁

在大多数情况下,锁不仅不存在竞争,而且总是由同一个线程多次获得,所以为了让线程以更低的代价获得锁,1.6引入了偏向锁,当1个线程访问同步代码块并获得锁的时候,会在对象头以及栈帧的锁记录中存储ThreadId,以后,这个线程进入和退出代码块的时候,是不需要通过CAS修改Mark Word,这个线程会一直持有这个锁.偏向锁采用等到竞争出现,才会释放锁的机制,如果出现了竞争关系,持有偏向锁的线程才会释放锁,通过CAS修改Mark Word转为无锁或者轻量级锁

### 轻量级锁

线程在执行同步块之前,会先分配空间并复制Mark Word到栈,并通过CAS修改Mark Word,成功的,将Mark Word替换成轻量级锁,失败的线程呢,则自旋获取锁,这种优化方式,大大加快了线程的响应速度.也是1.6引入的.

### 重量级锁

当轻量级锁进行解锁时,会通过CAS修改Mark Word,如果成功,代表没有竞争,如果失败,代表有竞争关系,那么,锁就会膨胀到重量级锁.

## 锁的优缺点对比

![image-20230723014741455](/Users/kongyao/Library/Application Support/typora-user-images/image-20230723014741455.png)


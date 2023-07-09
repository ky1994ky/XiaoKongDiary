# String,StringBuffer,StringBuilder的区别

## 问题:

请说一下String,StringBuffer,StringBuilder的相同点和不同点

## 回答:

相同点:

1. 都是用来表示字符序列的
2. 底层都是char数组
3. 都实现了Serializable(序列化接口)和CharSequence(char序列接口)
4. 都是final类型，不能被继承以及修改的。

不同点:

String的char数组是由final修饰的,所以,是不可变的,每次对String的赋值操作,实际上是新创建一个String对象

StringBuffer,StringBuilder的char数组是可变的,他们的父类AbstractStringBuilder封装了char数组的扩容操作

由于StringBuffer在自己的多个方法中都加了synchronized锁,所以,它是线程安全的,但是,性能较差,而StringBuilder没有加锁,所以它是线程不安全的,性能较好

## 源码:

String的char数组,由final修饰

<img src="/Users/kongyao/Library/Application Support/typora-user-images/image-20230619004642076.png" alt="image-20230619004642076" style="zoom:50%;" />

我们可以看到,char数组是由final修饰的,是不可变的

而StringBuffer,StringBuilder他们的父类AbstractStringBuilder的char数组,是可变的

<img src="/Users/kongyao/Library/Application Support/typora-user-images/image-20230619004850471.png" alt="image-20230619004850471" style="zoom:50%;" />

AbstractStringBuilder的数组扩容方法

![image-20230706162512701](/Users/kongyao/Library/Application Support/typora-user-images/image-20230706162512701.png)

StringBuffer,很多的方法都被synchronized修饰

<img src="/Users/kongyao/Library/Application Support/typora-user-images/image-20230619005148726.png" alt="image-20230619005148726" style="zoom:50%;" />

StringBuilder,并没有

<img src="/Users/kongyao/Library/Application Support/typora-user-images/image-20230619005258254.png" alt="image-20230619005258254" style="zoom:50%;" />

## 扩展:

由于String是不可变的,那么你知道String是如何做字符串拼接的吗?

<img src="/Users/kongyao/Library/Application Support/typora-user-images/image-20230619010656233.png" alt="image-20230619010656233" style="zoom:67%;" />

字节码:

![image-20230706011239563](/Users/kongyao/Library/Application Support/typora-user-images/image-20230706011239563.png)


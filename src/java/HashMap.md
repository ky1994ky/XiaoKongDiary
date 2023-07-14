# HashMap

hashMap应该是被问到最多的面试题了,也是我们经常使用的一种集合,我们今天,就来从源码的角度,让大家了解hashMap

hashMap底层,实际就是Node数组

![image-20230713010413136](/Users/kongyao/Library/Application Support/typora-user-images/image-20230713010413136.png)

而Node,其实有2种表现形式,第一种,链表形式

![image-20230713010626752](/Users/kongyao/Library/Application Support/typora-user-images/image-20230713010626752.png)

第二种: TreeNode,这个TreeNode 是 Node的子类

![image-20230713010729999](/Users/kongyao/Library/Application Support/typora-user-images/image-20230713010729999.png)



为了方便大家理解,我先给大家画一下hashMap的结构,我们都知道,hashMap的数据结构是数组+链表+红黑树

![image-20230712164809418](/Users/kongyao/Library/Application Support/typora-user-images/image-20230712164809418.png)

HashMap底层就是Node数组,Node有2种结构,一种是链表,一种是红黑树,接下来,我们就去看一下它的源码,让大家更加理解

我们先来看一下我们常用的put方法

![image-20230712165141314](/Users/kongyao/Library/Application Support/typora-user-images/image-20230712165141314.png)

当我们进入put的方法的时候,可以看到,这里调用了2个方法,先调用了hash方法,再调用了putVal方法

我们先看hash方法

![image-20230712165858612](/Users/kongyao/Library/Application Support/typora-user-images/image-20230712165858612.png)

这里,我们传入了一个key,如果key为空,返回0,不然返回key的hashCode与hashCode右移16位进行异或运算的结果

然后,我们进入putVal方法

![image-20230712170358663](/Users/kongyao/Library/Application Support/typora-user-images/image-20230712170358663.png)

这里,我们看到,如果tab如果为空,那么,就去创初始化一个Node数组,

然后,数组长度-1 和key运算出来的hash值,进行位与运算,得到下标位置,因为这里进行的是位与运算,所以得到的下标肯定会落在0-数组长度-1这个范围内

接下来,看这个下标有没有元素,

1. 如果没有元素的话,新建一个节点,将节点放入数组的指定下标
2. 如果有元素的话,看这个下标的数据的hash值是否相等,如果相等的话,看key的地址值是否相等或者key不等于空的情况下,key的equals是否为true,如果这些条件都满足,那么直接覆盖该node
3. 如果这个节点的类型是TreeNode或者是TreeNode的子类,将Node节点放入红黑树
4. 否者,进行遍历,找到链表尾部(尾插法),插入新创建的Node,同时,当遍历次数>=8时,也就代码链表中的数量>=8个时候,进行树化,将链表转换为红黑树

如果当前键值对数量 >  下一次调整大小的值为（容量 * 负载因子）,进行扩容

![image-20230712231102840](/Users/kongyao/Library/Application Support/typora-user-images/image-20230712231102840.png)

接下来,我们来看一下,hashMap的扩容方法

![image-20230712231341449](/Users/kongyao/Library/Application Support/typora-user-images/image-20230712231341449.png)

拿到旧的Node数组,容量,以及阈值

If (旧的Node数组的长度>0){

1. 如果旧数组的长度大于等于最大值,将阈值设为最大整数值,返回旧的Node数组
2. 否则,将容量以及阈值进行翻倍

}

else if (旧阈值 > 0){

​	新容量为旧阈值

}

else {

​	创建一个新的Node数组,将容量设置为16,阈值为16*0.75 = 12 

}

如果新容量小于最大容量并且阈值小于最大容量，则将阈值设置为ft，否则设置为最大整数值

将计算得出的新阈值赋值给HashMap的阈值

创建一个新的Node数组

遍历旧数组,依次取出Node

1. 如果该Node的next等于空,也就是该下标只有1个Node,那么,直接计算出新下标位置,放入Node

2. 如果该Node是红黑树类型,那么按照红黑树的进行迁徙
3. 如果该Node有多个Node的链表,那么分为高位链表与低位链表,分别放入新数组中,低位链表的位置在原下标位置上,高位链表的位置在原下标+旧数组长度的位置上

接下来,我们看一下红黑树是如何迁徙数据的

![image-20230713003711834](/Users/kongyao/Library/Application Support/typora-user-images/image-20230713003711834.png)

遍历树节点,将节点数据分别放入低位链表与高位链表中,分别放入新数组中,低位链表的位置在原下标位置上,高位链表的位置在原下标+旧数组长度的位置上,然后分别判断,如果低位链表和高位链表的下标上,节点个数是否小于6,如果小于6,那么就转换为链表形式放入,否则,以红黑树的形式放入


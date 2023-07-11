# LinkedList

我们今天来看一下LinkedList的源码

老样子,看原来,先从成员变量看起

![image-20230711004019157](/Users/kongyao/Library/Application Support/typora-user-images/image-20230711004019157.png)

那么,LinkedList 除了记录元素个数的Size以外,还记录了头尾2个Node节点,那么我们再去看一下Node是什么

![image-20230711004827099](/Users/kongyao/Library/Application Support/typora-user-images/image-20230711004827099.png)

根据代码,我们可以画出双向链表的结构

![image-20230711010047393](/Users/kongyao/Library/Application Support/typora-user-images/image-20230711010047393.png)

不管是单链表还是双向链表,它们都具有以下特点:

1. 快速的插入和删除
2. 随机访问慢
3. 不需要连续的地址空间,不容易产生内存碎片
4. 不需要扩容

接下来,我们来看看源码

我们常用的add方法

![image-20230711012233443](/Users/kongyao/Library/Application Support/typora-user-images/image-20230711012233443.png)

add方法调用了linkLast方法,往尾部添加元素,可以看出,LinkedList直接找到尾结点,然后直接将原来的尾结点的下一个节点指向了新节点,然后将新节点赋值给了尾结点,所以,添加元素是非常快速的

![image-20230711012325924](/Users/kongyao/Library/Application Support/typora-user-images/image-20230711012325924.png)

接下来,我们看一下get方法,看一下,LinkedList是如何查询元素的

![image-20230711013112615](/Users/kongyao/Library/Application Support/typora-user-images/image-20230711013112615.png)

![image-20230711013155546](/Users/kongyao/Library/Application Support/typora-user-images/image-20230711013155546.png)

可以看出,随机访问,对于LinkedList来讲,还是相当缓慢的,需要遍历,时间复杂度为O(n),如果你的下标更靠近头结点,那么就从头节点开始遍历,如果更靠近尾结点,那么从尾结点开始遍历



由于LinkedList 对头尾节点的插入以及删除操作是非常快的,所以,我们可以把它当成是队列或者栈来使用

![image-20230711013711851](/Users/kongyao/Library/Application Support/typora-user-images/image-20230711013711851.png)

所以,我们得出结论

LinkedList 适用于频繁插入与删除的场景,并且,不存在扩容的问题,但是,并不擅长随机访问,由于内存的不连续性,不存在内存碎片的问题,

同时,可以当成是队列和栈来使用
# ArrayList

我们来看一下,ArrayList的源码

我们看源码,推荐先看成员变量

![image-20230709230128480](/Users/kongyao/Library/Application Support/typora-user-images/image-20230709230128480.png)

![image-20230709230200931](/Users/kongyao/Library/Application Support/typora-user-images/image-20230709230200931.png)

通过看成员变量,我们可以看到,ArrayList的底层,是数组 elementData, ArrayList 有默认的初始容量,也有最大容量

那么,什么是数组?

数组是一种数据结构，用于存储多个相同类型的元素。它是一个连续的存储空间，每个元素都可以通过索引来访问。数组的长度是固定的，一旦定义了数组的大小，就不能再改变。数组可以存储各种数据类型，如整数、浮点数、字符等。在很多编程语言中，数组是基本的数据结构之一，常用于存储和处理大量数据。

![image-20230709211605612](/Users/kongyao/Library/Application Support/typora-user-images/image-20230709211605612.png)

查询指定下标的元素,查询效率为O(1),随机查询快

由于需要连续的内存空间,所以容易产生内存碎片

扩容困难,需要重新生成一个新的数组,然后将旧数组的元素进行搬运

指定下标的插入以及删除元素困难,需要下标后面的元素

get方法

先去校验你的下标是否超过数组长度,然后直接去数组中根据下标取元素

![image-20230709215748541](/Users/kongyao/Library/Application Support/typora-user-images/image-20230709215748541.png)

通过查看get方法,我们得出结论,ArrayList随机访问速度快

扩容方法

扩容1.5倍,然后复制出一个新的数组

![image-20230709220336140](/Users/kongyao/Library/Application Support/typora-user-images/image-20230709220336140.png)

通过查看扩容方法,我们得出结论,ArrayList扩容需要复制原来的数组,并且浪费空间,比如我的数组容量,是100,我要放入101个元素,那么,此时,ArrayList会扩容到150个,那么另外49个空间,就浪费掉了

remove方法

![image-20230709221212354](/Users/kongyao/Library/Application Support/typora-user-images/image-20230709221212354.png)

通过查看remove方法,我们得出结论,ArrayList去掉元素,需要复制原来的,并将后续的元素向左移动,也是非常耗时的操作

trimToSize方法,此方法时用来调整数组容量大小的,但是,源码中并没有进行调用,也就是说,ArrayList并没有自动缩容的机制,存在浪费空间的问题,举个例子,我现在往ArrayList中存入了1000个元素,然后再删掉,那么,我的数组,仍然占用了1000个元素的空间

![image-20230709225223939](/Users/kongyao/Library/Application Support/typora-user-images/image-20230709225223939.png)

## 总结:

ArrayList 底层是数组,有初始容量以及最大容量的限制,适用于频繁随机访问的场景,并不适用于频繁插入以及删除的场景,会有浪费空间的可能.
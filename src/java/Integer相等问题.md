# Integer相等问题

场景一:

```
Integer a = new Integer(10);
Integer b = new Integer(10);
System.out.println(a.equals(b));  // true
```

如果是equals比较的话,我们只需要去看value值是否相等

![image-20230704010819099](/Users/kongyao/Library/Application Support/typora-user-images/image-20230704010819099.png)



场景二:

```
Integer c = new Integer(10);
Integer d = new Integer(10);
System.out.println(c == d);    // false
```

如果说是 == 比较的话,那么调用new Integer() 这个构造方法的话,那么一定不等



场景三:

```
Integer e = 10; // 自动装箱   Integer e = Integer.valueOf(10);
Integer f = 10;
System.out.println(e == f);
```

如果说,创建方式采用自动装箱,那么你传进来的值,是在缓存数组的范围内,那么给你返回的是常量池中的对象,如果不在,那么新创建对象,

所以,值在-128到127之间,那么 == 的结果是ture,否则,是false

![](/Users/kongyao/Library/Application Support/typora-user-images/image-20230704011104065.png)

我们在看valueOf的源码

![image-20230704011358912](/Users/kongyao/Library/Application Support/typora-user-images/image-20230704011358912.png)

再去看一下常量池的源码,注意的点都写在了注释中

![image-20230704011743846](/Users/kongyao/Library/Application Support/typora-user-images/image-20230704011743846.png)

常量池可以通过改变VM参数,调整大小

```
Integer g = 300;
Integer h = 300;
System.out.println(g==h); //true
// -XX:AutoBoxCacheMax=300 通过改变JVM的参数,使得相等
```

为什么要设计常量池:

1. 节约内存,不管你要创建多少个常量池范围内的对象,我只需要去创建常量池大小的对象个数
2. 提高效率,对象创建是一个费时操作,提前创建好后,只需要去数组中取就行了,速度更快

常量池的设计采用了享元模式,这种模式,可以帮助我们复用对象,节省内存
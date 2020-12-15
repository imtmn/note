# 为什么一定要了解`HashMap`

首先，`HashMap`几乎是面试必问的`java`知识点，如果你连`HashMap`都不懂，那你这次面试结果一定大打折扣

其次，**常用**，`java`程序猿几乎天天在用它，天天用的东西，你如果不了解可是很危险的哦。

个人觉得，这两点已经足以让咱们好好去学习一番了

文章内容基于：**jdk1.8**

# 底层结构

简单点就是：数组 +（链表 or 红黑树）

```
    //存储结构：Node数组，Node在长度小于8时是链表，当长度大于8时，自动转成红黑树
    transient Node<K,V>[] table;
```

![HashMap底层结构](http://cdn.mtmn.top/image-91c74727972a44e0b386d6f4c723d2ab.png)

# 输入如何插入



## 如何定位hash桶位置，也是数组的下标

1. 获取key的`hashCode`

2. 高位运算：`hashCode`无符号右移16位，异或`hashCode`

   > 无符号右移 >>>：不管正负标志位为0还是1，将该数的二进制码整体右移，左边部分总是以0填充，右边部分舍弃
   >
   > 异或 ^：异或运算符是用符号“^”表示的，其运算规律是：
   >
   > ​			两个操作数的位中，相同则结果为0，不同则结果为1

3. 取模运算

   > 取模运算：可以简单理解为求余数，严谨一点需要自己`google`扩展了

```
    //hash 算法
    static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
    // 确定hash桶位置 关键代码 (n-1)&hash 
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    if ((tab = table) == null || (n = tab.length) == 0)
         n = (tab = resize()).length;
    if ((p = tab[i = (n - 1) & hash]) == null)
         tab[i] = newNode(hash, key, value, null);
```

拆解步骤:

![步骤](http://cdn.mtmn.top/image-8cdd30129ca3474bae74de6cbae1a1e5.png)

以`key`为“码头码农”为例：

![例子](http://cdn.mtmn.top/image-eb796b2c36594b10b4b8fa72c75ccf3b.png)

所以呢，“码头码农”这个key,应该被放在索引为**4**的`hash`桶中

![插入一个节点](http://cdn.mtmn.top/image-58bfe707494f4dfb839c2f739bd4c921.png)

## 进一步看数据插入

暂时先不关注上面提到的如何确定`hash`桶位置，忽略一些细节，进一步看看`HashMap`的数据插入，是不是一张图就有点看明白了呢？

![流程](http://cdn.mtmn.top/image-cc60b37fe5d94845a1445b0994232ac9.png)

## 如何实现扩容？

想要了解扩容，咱必须先搞明白几个概念

最大容量 **threshold**  ：当节点数量超过threshold 时，`HashMap` 就会进行resize(扩容)操作

桶容量 **capacity**  ：hash桶数组的数量，进行扩容时，容量提高一倍

节点数量 **size**：`map`节点的数据，也就是`map.size()`的值

![扩容](http://cdn.mtmn.top/image-3b696c2c8e934f1eb462c0ebf8c63d04.png)



## 扩容后节点会发生什么变化？

![扩容后节点的变化](http://cdn.mtmn.top/image-a42ccaaa5c024531907e9f1c6020d13e.png)

这个设计十分巧妙，既省去了重新计算hash,扩容过程中又把节点均匀的分散到新的哈希桶中

# 小结

至此，应该对`HashMap`有了一个相对全面一点的认识，有些知识点咱们这边补充下，就不做详细解释了

关于线程安全：

​	`HashMap`线程不安全，`HashTable`与Hash类似，他内部通过synchronized实现了线程安全 `HashMap`键和值都可以为null,但是`HashTable`不行 非并发场景推荐使用`HashMap`，并发场景推荐使用`ConcurrentHashMap`

其他：

​	`HashMap`根据键保存和获取值的效率都很高，为O(1)，每个单项链表往往只有一个或少数几个节点，根据hash 值就可以直接快速定位 `HashMap`中的键值没有顺序，因为hash值是随机的。



看完本篇，日常使用和应付面试应该不成问题啦~
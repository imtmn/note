# RandomAccess的理解

`RandomAccess`是一个标记型接口（Marker interface），主要作用是声明实现类支持**随机访问**。

什么是随机访问？

随机访问就是具备类似数组那样的特性，数据在**内存是连续存放**的，根据索引值就可以直接定位到具体的元素，访问效率很高，可以简单的认为支持随机访问的集合，访问**速度更快**。

![图片来源于网络](http://cdn.mtmn.top/randomAccess-3c5dc1e49c05494ba96acaaf195768a2.png)

例如：

**数组**是支持通过下标直接查找元素的，时间复杂度为**O(1)**

**链表**只能通过遍历查找元素，时间复杂度为**O(n)**

`ArrayList`的底层实现是**数组**，所以`ArrayList`支持随机访问

`LinkList`的底层实现是**链表**，因此`LinkList`不支持随机访问



# RandomAccess的应用

> It is recognized that the distinction between random and sequential access is often fuzzy. For example, some `List` implementations provide asymptotically linear access times if they get huge, but constant access times in practice. Such a `List` implementation should generally implement this interface. As a rule of thumb, a `List` implementation should implement this interface if, for typical instances of the class, this loop:
>
> ```java
>      for (int i=0, n=list.size(); i < n; i++)
>          list.get(i);
>  
> ```
>
> runs faster than this loop:
>
> ```java
>      for (Iterator i=list.iterator(); i.hasNext(); )
>          i.next();
> ```

官方文档鼓励使用通用列表算法来检查给定列表是否为`instanceof RandomAccess`判断是否支持随机访问，然后再应用不同的算法实现以保证可接受的性能，简单说就是通过判断是否支持随机访问，采用不用的方式来提高性能。

例如`Collections`中二分法查找

```java
public static <T>
int binarySearch(List<? extends Comparable<? super T>> list, T key) {
    if (list instanceof RandomAccess || list.size()<BINARYSEARCH_THRESHOLD)
        return Collections.indexedBinarySearch(list, key);
    else
        return Collections.iteratorBinarySearch(list, key);
}
```
使用Collections的二分查找时，会先判断列表是否支持随机访问，如果列表没有实现`RandomAccess`接口，并且数量较大，这边数量阀值为`BINARYSEARCH_THRESHOLD   = 5000`，也就是说数量大于5000且没有实现`RandomAccess`的列表则使用**迭代器**的二分法查找。

如果大家看过`Collections`的代码就会知道，这里有很多方法对`RandomAccess`进行判断，区别实现功能。




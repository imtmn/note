

# Iterable

Iterable adj.可迭代的 。

以下几点是了解Iterable接口需要注意的

- 实现这个接口的对象，允许使用 for-each loop语法。
- 由于Collection接口继承Iterable接口，因此所有实现了Collection的实现类都实现了Iterable接口。

![image.png](http://cdn.mtmn.top/image-f4efadc188014a5aba5cf3a0f85b6697.png)

接口方法说明

```
    // 返回一个指定类型的迭代器
    Iterator<T> iterator();
    // 遍历迭代器，对每个元素执行指定操作（JDk1.8）
    default void forEach(Consumer<? super T> action) {
        Objects.requireNonNull(action);
        for (T t : this) {
            action.accept(t);
        }
    }
    //返回拆分的迭代器（JDk1.8）
    default Spliterator<T> spliterator() {
        return Spliterators.spliteratorUnknownSize(iterator(), 0);
    }
```

# Iterator

Iterator  美 /ɪtə'retɚ/   n. 迭代器；迭代程序

了解Iterable接口必不可少需要了解Iterator接口

![image.png](http://cdn.mtmn.top/image-d4f49f9fde424d4490af7de40dea4181.png)

接口方法说明

```
    //判断迭代器是否包含更多元素
    boolean hasNext();
    //返回迭代器下一个元素
    E next();
    //删除迭代器返回的最后一个元素
    default void remove() {
        throw new UnsupportedOperationException("remove");
    }
    //对剩余所有元素执行指定操作
    default void forEachRemaining(Consumer<? super E> action) {
        Objects.requireNonNull(action);
        while (hasNext())
            action.accept(next());
    }

```

这边以基于数组的集合为例，画图展示下Iterator执行next()的操作

![image.png](http://cdn.mtmn.top/image-d180d20c42bf4e46850fc70b89663803.png)

  判断是否有下个节点hasNext()

![image.png](http://cdn.mtmn.top/image-d88a537b0d7c46e081ad5443cd27c2f5.png)

# 迭代器的使用

以Set为例，测试迭代器使用

```
public class Test {
    public static void main(String[] args) {
        Set<Integer> set = new HashSet<>();
        set.add(1);
        set.add(2);
        set.add(3);
        //原数组输出
        System.out.println(Arrays.toString(set.toArray()));
        //迭代输出
        Iterator<Integer> iterator = set.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
        //重新获取迭代器，这里要注意，每个迭代器只用使用一次。因为当迭代器遍历一遍之后，迭代器是指向最后一个元素，继续调用hasNext()返回为false
        iterator = set.iterator();
        //forEachRemaining (要求jdk1.8)
        iterator.forEachRemaining(i -> System.out.println("i=" + i));
    }
}

```

输出结果：

[1, 2, 3]
1
2
3
i=1
i=2
i=3

这边需要注意的是，迭代器每次使用完后不能再继续使用。以上的测试代码中之所以可以遍历两遍，是因为中间使用`iterator = set.iterator();`再次获取set的迭代器。
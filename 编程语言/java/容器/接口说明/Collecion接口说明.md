

# Collection（集合）

首先，这边先用一张图简单概况下集合的概念，帮助大家理解集合

![image.png](http://cdn.mtmn.top/image-b50232e8fd2b4704a5e08ea07ae14058.png)

接下来咱们先来看看Collection的继承关系，这边看到Collection继承了Iterable，关于Iterable接口的详细说明，可以看我之前的博客 [Iterable接口说明](http://www.mtmn.top/archives/javaiterator)，我们知道我们日常中用到的几个常用解救都是继承Collection,例如List接口、Set接口、Queue接口。

![image.png](http://cdn.mtmn.top/image-acf5dc2031cd459d81a47630a17dfce6.png)

首先先来看看Collection接口声明的相关方法

![image.png](http://cdn.mtmn.top/image-24ffc3aca74f4dac8042dcfa9a79c7fc.png)

以下Collection相关的方法及简单说明

```
    // 新增一个元素
    boolean add(E e);
    // 添加指定集合中的所有元素
    boolean addAll(Collection<? extends E> c);
    // 清空集合中的所有元素
    void clear();
    // 判断集合是否包含指定元素
    boolean contains(Object o);
    // 判断集合是否包含指定集合中的所有元素
    boolean containsAll(Collection<?> c);
    // 判断集合是否不包含元素
    boolean isEmpty();
    // 以集合为源，返回一个并行流（这块不理解可以先了解JDK8的流操作）
    default Stream<E> parallelStream() {
        return StreamSupport.stream(spliterator(), true);
    }
    // 删除指定元素
    boolean remove(Object o);
    // 删除指定集合中的所有元素
    boolean removeAll(Collection<?> c);
    // 删除集合中所有满足过滤条件的元素
    default boolean removeIf(Predicate<? super E> filter) {
        Objects.requireNonNull(filter);
        boolean removed = false;
        final Iterator<E> each = iterator();
        while (each.hasNext()) {
            if (filter.test(each.next())) {
                each.remove();
                removed = true;
            }
        }
        return removed;
    }
    // 是否包含指定集合中的所有元素
    boolean retainAll(Collection<?> c);
    // 集合的元素个数
    int size();
    // 以集合为源，返回一个流
    default Stream<E> stream() {
        return StreamSupport.stream(spliterator(), false);
    }
    //返回该集合所有元素的数组
    Object[] toArray();
    //返回该集合所有元素的指定类型数组
    <T> T[] toArray(T[] a);
```

# 使用场景

## 几种常见遍历方式

### iterable 迭代器

```java
public class Test {
    public static void main(String[] args) {
        Collection<Integer> collection = new ArrayList<>();
        collection.add(1);
        collection.add(2);
        collection.add(3);

        Iterator<Integer> iterator = collection.iterator();
        //迭代器第一种遍历方式
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
        //迭代器第二章遍历方式
        iterator = collection.iterator();
        iterator.forEachRemaining(System.out::println);
    }
}
```

### for-each

Collection接口是继承Iterable接口的，在java中实现了[Iterable接口](http://www.mtmn.top/archives/javaiterator)的类都支持for-each语法

```java
public class Test {
    public static void main(String[] args) {
        Collection<Integer> collection = new ArrayList<>();
        collection.add(1);
        collection.add(2);
        collection.add(3);

        //for-each 1
        collection.forEach(System.out::println);
        //for-each 2
        for (Integer i : collection) {
            System.out.println(i);
        }
    }
}
```

### stream 流操作

```java
public class Test {
    public static void main(String[] args) {
        Collection<Integer> collection = new ArrayList<>();
        collection.add(1);
        collection.add(2);
        collection.add(3);

        //流操作
        collection.stream().forEach(System.out::println);
        //并行流操作
        collection.parallelStream().forEach(System.out::println);
    }
}
```

以上三种遍历输出，除了parallelStream 并行流操作以外，其他结果均为：

1
2
3
1
2
3
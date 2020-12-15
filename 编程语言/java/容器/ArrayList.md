# 基本类图

首先咱们先来看看ArrayList的基本类图，由下图可以清楚的知道，ArrayList的继承、实现关系。现在让咱们先来看看这些接口和父类的作用吧
![类图](http://cdn.mtmn.top/image-3bd09f91fa8349b59482b840725f656b.png)

看到这张图，我的第一反应是，为什么AbstractList已经实现实现了List接口，ArrayList还需要实现LIst接口？

这个问题我在网上搜索，发现答案五花八门，个人是觉得有几个观点是比较靠谱的：

1、让用户可以清晰明了的知道，该类实现了List接口，不需要再去父类中查看

2、在stackoverflow 也有大佬的回答，说这是一个错误，其实是没必要的，这个回答虽然是针对Set的，但是两个问题是类似的

> I've asked Josh Bloch, and he informs me that it was a mistake. He used to think, long ago, that there was some value in it, but he since "saw the light". Clearly JDK maintainers haven't considered this to be worth backing out later.
>
> 来自：https://stackoverflow.com/questions/2165204/why-does-linkedhashsete-extend-hashsete-and-implement-sete

## ArrayList相关的接口说明

### 标记接口：RadomAccess、Cloneable、Serializable

ArrayList这边实现了三个标记接口`RadomAccess`、`Cloneable`、`Serializable`

这三个接口的作用分别是

`RadomAccess`表示集合支持随机读取，在大数据量的集合中，支持随机读取的集合查询效率更高，详见[RandomAccess随机访问](http://www.mtmn.top/archives/randomaccess)

`Cloneable`表示实现类支持拷贝，ArrayList实现的是浅拷贝（shallow copy），也就是说当原集合元素发生改变，拷贝后的集合元素也会跟着发生改变，详见[Cloneable简单介绍](http://www.mtmn.top/archives/cloneable)

`ArrayList`浅拷贝代码如下

```java
    public Object clone() {
        try {
            ArrayList<?> v = (ArrayList<?>) super.clone();
            v.elementData = Arrays.copyOf(elementData, size);
            v.modCount = 0;
            return v;
        } catch (CloneNotSupportedException e) {
            // this shouldn't happen, since we are Cloneable
            throw new InternalError(e);
        }
    }
```

`Serializable`表示实现类支持序列化，即可实现类实例的网络传输和持久化存储。详见[Serializable序列化](http://www.mtmn.top/archives/serializable)

### List、Collection、Iterable的关系
![继承关系](http://cdn.mtmn.top/image-604241a4b5cf4313a7089703c53fe7a1.png)

`Iterable`接口表示容器支持迭代器，支持迭代器的实现类可以用for-earch语法，详情可见[Iterable接口说明](http://www.mtmn.top/archives/javaiterator)

`Collection`接口包含集合操作的基础方法，详情可见[Collection接口说明](http://www.mtmn.top/archives/collection)

### List（列表）
![List接口方法](http://cdn.mtmn.top/image-0d683e67921741cda644a9a6e14d6bed.png)

这边可以看到，List相对于Collection增加的几个方法：

```
    //在列表指定位置插入元素
    void add(int index, E element);
    //在指定位置开始插入集合，如果本来就有元素，则向右移动
    boolean addAll(int index, Collection<? extends E> c);
    //返回指定位置的元素
    E get(int index);
    //返回找到的第一个指定元素的位置，没有找到返回-1
    int indexOf(Object o);
    //返回找到的最后一个指定元素的位置，没有找到返回-1
    int lastIndexOf(Object o);
    //返回一个列表迭代器
    ListIterator<E> listIterator();
    //从指定位置开始，返回一个列表迭代器
    ListIterator<E> listIterator(int index);
    //删除指定位置的元素
    E remove(int index);
    //lambda表达式或方法引用的赋值目标（以lambda表达式的方式批量修改列表元素）
    default void replaceAll(UnaryOperator<E> operator) {
        Objects.requireNonNull(operator);
        final ListIterator<E> li = this.listIterator();
        while (li.hasNext()) {
            li.set(operator.apply(li.next()));，。                        
        }
    }
	//替换指定位置的元素
    E set(int index, E element);
    //根据指定比较器产生的顺序对列表进行排序
    default void sort(Comparator<? super E> c) {
        Object[] a = this.toArray();
        Arrays.sort(a, (Comparator) c);
        ListIterator<E> i = this.listIterator();
        for (Object e : a) {
            i.next();
            i.set((E) e);
        }
    }
    //截取列表
    List<E> subList(int fromIndex, int toIndex);
```

# 底层实现

ArrayList的底层实现是数组

```java
    transient Object[] elementData; // non-private to simplify nested class access
```

[^transient]: 序列化对象的时候，这个属性不会被序列化

## 插入

`new ArrayList()`时ArrayList 会默认初始化一个空数组，当插入元素时，`ArrayList`会确保数据容量足够（详见`private void ensureCapacityInternal(int minCapacity)`方法），如果数组容量不够会进行扩容，调用`private void grow(int minCapacity)`方法，现在ArrayList是如何实现扩容的

```java
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```

上面可以看到`int newCapacity = oldCapacity + (oldCapacity >> 1);`

新的数组容量的计算方式是：(左移一位有不懂的朋友可以看)
$$
新数组容量 = 旧数组容量 + (旧数组容量 \times 2)
$$
如果传入的最小容量还大于新的容量，那就用最小，如果新的容量大于最大数组容量 `MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8` 则会调用 `hugeCapacity(int minCapacity) `方法，数组的最大绒里不会超过`Integer.MAX_VALUE` 具体值`MAX_VALUE = 0x7fffffff` 也就是$$2^{31} - 1 $$ ，这边`hugeCapacity`的入参就是int，其实也不可能传入比int 大的数字

扩容之后就是正常插入了

![插入元素](http://cdn.mtmn.top/image-c6a87de59fb340039eda9bf4ca242e72.png)

## 查找

直接根据索引位置进行查询(超简单……)

```java
    public E get(int index) {
        rangeCheck(index);
        return elementData(index);
    }

    E elementData(int index) {
        return (E) elementData[index];
    }
```

![根据索引查询](http://cdn.mtmn.top/image-baad7480f132447b9417ed6d20d67d3f.png)

根据元素查询索引位置`indexOf(Object o)`  (也是超级简单……)

```java
    public int indexOf(Object o) {
        if (o == null) {
            for (int i = 0; i < size; i++)
                if (elementData[i]==null)
                    return i;
        } else {
            for (int i = 0; i < size; i++)
                if (o.equals(elementData[i]))
                    return i;
        }
        return -1;
    }
```

![根据元素查询](http://cdn.mtmn.top/image-4700e7950f5e44d6b523f21ccb99c19d.png)

## 删除

小删除主要就是根据索引位置删除和根据元素删除，根据元素删除就是遍历元素，然后根据索引位置进行删除（调用的是 `fastRemove`相对于remove省略了范围检查和获取元素的步骤），现在来看看根据索引位置删除元素的具体实现

```java
    public E remove(int index) {
        rangeCheck(index);
        modCount++;
        E oldValue = elementData(index);
        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // clear to let GC do its work
        return oldValue;
    }
```

检查索引位置是否在范围内 > 修改次数+1 > 根据索引位置获取元素 >确认需要移动的元素>移动数组>将数组最后一位置空（这部操作上面也注释了，就是为了让GC回收）

![删除元素](http://cdn.mtmn.top/image-3718cbc3ae1145b0ba9c2fc7569bd8e3.png)

# 小结

1. ArrayList可以随机访问，按照索引位置从访问效率高，时间复杂度是O(1)

2. 根据元素查找的效率比较低，时间复杂度是O(n)

3. 插入和删除元素的效率比较低，因为需要移动元素，时间复杂度为O(n)


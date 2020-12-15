

# 基本类图

`LinkedList`的内部实现是**双向链表**，那什么是双向链表呢？来，上图

![双向链表](http://cdn.mtmn.top/image-584e792424a34b9fb09e1c8272ba0038.png)

**双向链表**是一个特殊的链表，链表两端都支持插入、查询或检出元素

接下来咱们看看`LinkedList`的继承关系

![继承关系](http://cdn.mtmn.top/image-9e1150afd91c45758775e2fdc4633725.png)

这边可以知道`LinkedList`及实现了[Deque接口](http://www.mtmn.top/archives/deque)， `Deque`是**双向队列**的声明接口，而`LinkedList`内部实现是链表，这也是为什么文章开头说`LinkedList`是一个**双向链表**

而`LinkedList`的类图相对于[ArrayList](http://www.mtmn.top/archives/arraylist)少了[RandomAccess](http://www.mtmn.top/archives/randomaccess)接口，因此`LinkedList`是不支持随机访问的，因此`LinkedList`根据索引位置查找元素的速度比`ArrayList`慢





# 方法实现

## 插入元素

`LinkedList` 插入元素方法有：

```java
    // 添加元素 在链表尾部添加元素
	public boolean add(E e);
	// 在指定索引位置插入元素，索引越界则抛出IndexOutOfBoundsException
    public void add(int index, E element);   
    // 在链表头部添加元素
	public void addFirst(E e);
    // 在链表尾部添加元素
    public void addLast(E e);
	// 在尾部添加元素
    public boolean offer(E e);
	// 在头部插入元素
    public boolean offerFirst(E e);
	// 在尾部插入元素
    public boolean offerLast(E e);
	// 在尾部批量插入元素
    public boolean addAll(Collection<? extends E> c);
    // 在指定索引位置批量插入元素，如果链表已满 则返回false，索引越界则抛出IndexOutOfBoundsException
    public boolean addAll(int index, Collection<? extends E> c);
```

这边以`add`为例

```java
    public boolean add(E e) {
        linkLast(e);
        return true;
    }
    void linkLast(E e) {
        final Node<E> l = last;
        final Node<E> newNode = new Node<>(l, e, null);
        last = newNode;
        if (l == null)
            first = newNode;
        else
            l.next = newNode;
        size++;
        modCount++;
    }
```

`add`方法调用的是`linkLast`也就是在链表末尾添加元素。之前咱们再介绍`ArrayList`新增元素的时候，每次都需要调用`ensureCapacityInternal(int minCapacity)`方法，确保数组空间足够，不够的话要进行扩容。

而`LinkedList`就不需要，这也是`LinkedList`的主要优点：它不需要初始化容器容量大小，也就无需动态扩容。也就是说`LinkedList`比`ArrayList`更省内存空间，插入速度更快。

但是如果需要根据索引位置插入元素，效率就比较低，因为需要先逐个查找元素，这边看下 `add(int index, E element)`的实现

```java
    public void add(int index, E element) {
        checkPositionIndex(index);

        if (index == size)
            linkLast(element);
        else
            linkBefore(element, node(index));
    }
    Node<E> node(int index) {
        // assert isElementIndex(index);

        if (index < (size >> 1)) {
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }
```

根据索引位置插入，需要先调用 `Node<E> node(int index)`先找到元素，然后在元素前面插入**新的元素**

因此在中间插入元素，要先定位，效率比较低，为O(N/2)，但修改本身效率很高为O(1)

那根据元素查询索引下标呢？这边看看`indexOf(Object o)`方法

```java
    public int indexOf(Object o) {
        int index = 0;
        if (o == null) {
            for (Node<E> x = first; x != null; x = x.next) {
                if (x.item == null)
                    return index;
                index++;
            }
        } else {
            for (Node<E> x = first; x != null; x = x.next) {
                if (o.equals(x.item))
                    return index;
                index++;
            }
        }
        return -1;
    }

```

很显然，按照内容查找元素，效率比较低，必须逐个比较，效率为O(N)

## 删除元素

`LinkedList` 删除元素方法有：

```java
    // 删除头部元素
	public E remove();
	// 删除指定索引位置元素
    public E remove(int index);
	// 删除指定元素
    public boolean remove(Object o);
	// 删除头部元素
    public E removeFirst();
	// 删除第一个匹配元素
	public boolean removeFirstOccurrence(Object o);
    // 删除最后一个元素
	public E removeLast();
    // 删除最后一个匹配元素
	public boolean removeLastOccurrence(Object o);
    // 删除一个个元素
	public E poll();
	// 删除第一个元素
    public E pollFirst();
	// 删除最后一个元素
    public E pollLast();
```

以`remove`为例

```java
    public E remove() {
        return removeFirst();
    }
    public E removeFirst() {
        final Node<E> f = first;
        if (f == null)
            throw new NoSuchElementException();
        return unlinkFirst(f);
    }
    private E unlinkFirst(Node<E> f) {
        // assert f == first && f != null;
        final E element = f.item;
        final Node<E> next = f.next;
        f.item = null;
        f.next = null; // help GC
        first = next;
        if (next == null)
            last = null;
        else
            next.prev = null;
        size--;
        modCount++;
        return element;
    }
```

这边可以看到，`remove`方法调用的是`removeFirst`删除头元素，这边的移除元素的方法也是简单，将头元素指向第二个元素，将第一个元素置空。如果是数组删除第一个元素，数组元素的位置都要发生改变，而链表不用。所以，链表删除元素比数组更快。

## 查询元素

`LinkedList`查询元素的方法有：

```java
    // 根据索引位置查询元素
    public E get(int index);
	// 获取第一个元素
    public E getFirst();
	// 获取最后一个元素
    public E getLast();
    // 查询头部元素（第一个元素）
    public E peek();
    // 查询头部元素（第一个元素）
    public E peekFirst();
    // 查询尾部元素（最后一个元素）
    public E peekLast();
	// 查询第一个元素
    public E element();
```

这边查询以`get`为例

```java
    public E get(int index) {
        checkElementIndex(index);
        return node(index).item;
    }
    private void checkElementIndex(int index) {
        if (!isElementIndex(index))
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
    private boolean isElementIndex(int index) {
        return index >= 0 && index < size;
    }
    Node<E> node(int index) {
        // assert isElementIndex(index);

        if (index < (size >> 1)) {
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }
```

查询方法也简单，调用`checkElementIndex`判断数组是否越界，然后根据索引下标查询元素。这边有个判断 `index < (size >> 1)` 这个判断相当于 `index < size/2 ` ，这个意思是如果把对半分开，如果查询的索引位置在**前半部分**就**从头开始**查找，如果索引位置在**后半部分**就从尾部开始查询，这是提高查询速度的一种策略。

相对于**数组**，链表根据索引位置访问元素的效率就要低得多，数组可以根据索引下标直接获取元素

# 使用场景

1. 需要快速插入和删除的场景，这边插入主要是列表**头部**和**尾部**的操作，**不包含中间操作**的场景
2. 需要节省内存的情况下，多数情况下`LinkedList`占用内存更低

# 小结

1、按需分配空间，不需要预先分配很多空间，更加节省内存空间
2、不支持随机访问，按照索引位置访问效率比较低，必须从头或尾顺着链表找 效率为O(N/2)
3、不管列表是否已经排序，只要按照内容查找元素，效率都比较低，必须逐个比较，效率为O(N)
4、根据索引位置进行插入和删除元素，要先定位，效率比较低，为O(N/2)，但修改本身效率很高为O(1)
# 基本说明

`Queue`队列，常见的队列有

**FIFO队列**：先进先出（first in first out）

**FILO队列**：先进后出（first in last out）

**优先队列**：根据比较器对元素进行排序

![Queue](http://cdn.mtmn.top/image-43496b3871444d629e5ebc33655eff61.png)

`Queue`的继承关系如下：

![Queue继承关系](http://cdn.mtmn.top/image-d4ea75b104734a328b770a974f5b232f.png)

这边可以看到`Queue`也继承了[Collection接口](http://www.mtmn.top/archives/collection)，接下来咱们看看`Queue`的接口方法：

![接口方法](http://cdn.mtmn.top/image-d4e2256073e54812b74537312f5daa59.png)

队列对于添加、删除、检出方法都提供了两种形式，主要的差异是：一个操作失败会抛出异常，另一个方法则是返回特殊值null或false

| 操作 | 抛出异常的方法 | 返回特殊值的方法（null/false） |
| ---- | -------------- | ------------------------------ |
| 插入 | add(e)         | offer(e)                       |
| 删除 | remove()       | poll()                         |
| 检索 | element()      | peek()                         |

```
    //插入元素，如果没有超过容量限制则返回true，如果超过容量限制则抛出IllegalStateException
    boolean add(E e);
    //插入元素，如果没有超过容量限制则返回true，如果超过容量限制则返回false
    boolean offer(E e);
    //检索并删除队列的头元素,如果为空会抛出NoSuchElementException
    E remove();
    //检索并删除队列的头元素，如果队列为空，则返回null
    E poll();
    //检索队列头元素，但不删除,如果为空会抛出NoSuchElementException
    E element();
    //检索列表头元素，但不删除，队列为空时，返回null
    E peek();
```

# 基础应用

```java
    public static void main(String[] args) {
        Queue<String> queue = new LinkedList<>();
        queue.add("前排");// 添加元素，超过容量抛出异常IllegalStateException
        queue.offer("中排"); // 添加元素，超过容量返回false
        queue.add("后排");

        System.out.println(queue.element()); // 输出"前排" 检索头元素但不删除，但不删除,如果为空会抛出NoSuchElementException
        System.out.println(queue.peek());//输出"前排" 检索头元素，但不删除，队列为空返回 null

        System.out.println(queue.remove());//输出"前排"  检出并删除元素，如果为空会抛出异常NoSuchElementException
        System.out.println(queue.peek());//输出“中排”   检出并删除元素,如果为空返回false

        System.out.println(queue.poll());//输出“中排”
        System.out.println(queue.peek());//输出"后排"
        System.out.println(queue.poll());//输出“后排”
        System.out.println(queue.peek());//继续检索 队列已经为空，输出null
        System.out.println(queue.poll());//继续检出 队列已经为空，输出null
    }
```


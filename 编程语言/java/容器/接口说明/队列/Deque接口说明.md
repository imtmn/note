# 基本说明

`Deque`双端队列，是队列的一种扩展。一般队列都是一边进，一边出。双端队列是两边都能进，两边都能出。下面画个简图比较一下

![Queue与Deque的比较](http://cdn.mtmn.top/image-dbf1083de0c445e4b9bfa030d688b898.png)

是不是很简单，`Deque`就像是`Queue`的升级版本，更加灵活。看下图，也证明了这一点，`Deque`继承了`Queue`，`Deque`是`Queue`的扩展。需要了解`Queue`可以看之前博客：[Queue队列接口说明](http://www.mtmn.top/archives/queue)

![继承关系](http://cdn.mtmn.top/image-c5bdcc6c9bb5460ab4bce78734fbd1ce.png)

接下来看看`Deque`接口方法

![Deque接口方法](http://cdn.mtmn.top/image-0164657c7d0e4176bb6aa4dd4bcbb863.png)

以下是`Queue`接口的方法和区别，主要的区别是异常情况下是**返回特殊值**还是**抛出异常**

| 操作               | 抛出异常的方法 | 返回特殊值的方法（null/false） |
| ------------------ | -------------- | ------------------------------ |
| 插入               | add(e)         | offer(e)                       |
| 检出（查询并删除） | remove()       | poll()                         |
| 检索（查询不删除） | element()      | peek()                         |

`Deque`继承了`Queue`自然也拥有`Queue`的全部方法，接下来看看`Deque`相对与`Queue`**扩展的方法**

| 操作                       | 抛出异常的方法          | 返回特殊值的方法（null/false） |
| -------------------------- | ----------------------- | ------------------------------ |
| 插入头部元素               | addFirst(e)/**push()**  | offerFirst(e)                  |
| 检出头部元素（查询并删除） | removeFirst()/**pop()** | pollFirst()                    |
| 检索头部元素（查询不删除） | getFirst()              | peekFirst()                    |
| 插入尾部元素               | addLast(e)              | offerLast(e)                   |
| 检出尾部元素（查询并删除） | removeLast()            | pollLast()                     |
| 检索尾部元素（查询不删除） | E getLast()             | peekLast()                     |

这边的方法相对都比较简单，就不一一介绍了，这边有两个方法单独介绍下

```java
    //删除第一个匹配到的元素
	boolean removeFirstOccurrence(Object o);
    //删除最后一个匹配到的元素
    boolean removeLastOccurrence(Object o);
```

# Deque的使用

基本操作

```java
    public static void main(String[] args) {
        Deque<Integer> deque = new LinkedList(Arrays.asList(1, 2, 3, 4, 5, 6));
        System.out.println(deque.getFirst()); //查询第一个元素 输出结果3
        System.out.println(deque.removeLast());//删除最后一个元素 输出结果6
        System.out.println(deque.getLast());//查询最后一个元素 输出5

        System.out.println(Arrays.toString(deque.toArray())); //当前队列情况：[1, 2, 3, 4, 5]
        System.out.println(deque.pollFirst());//检出第一个元素，输出结果1
        System.out.println(deque.getFirst());//再次查询一个元素，结果2，由于上一步操作已经检出

        System.out.println(Arrays.toString(deque.toArray())); //当前队列情况：[2, 3, 4, 5]
        System.out.println(deque.pollLast());//检出最后一个元素 5
        System.out.println(deque.getLast());//再次查询最后一个元素，结果4，上一步已经检出
    }

```

| 结果输出                                                     |
| ------------------------------------------------------------ |
| 1<br/>6<br/>5<br/>[1, 2, 3, 4, 5]<br/>1<br/>2<br/>[2, 3, 4, 5]<br/>5<br/>4<br/><br/>Process finished with exit code 0 |

删除指定元素：

```java
    public static void main(String[] args) {
        Deque<Integer> deque = new LinkedList(Arrays.asList(3, 1, 2, 3, 4, 5, 6, 3));
        deque.removeFirstOccurrence(3);
        System.out.println(Arrays.toString(deque.toArray())); //匹配到第一个元素删除，输出结果[1, 2, 3, 4, 5, 6, 3]
        deque.removeLastOccurrence(3);
        System.out.println(Arrays.toString(deque.toArray())); //匹配到最后一个元素删除，输出结果[1, 2, 3, 4, 5, 6]
    }
```

| 输出                                                         |
| ------------------------------------------------------------ |
| [1, 2, 3, 4, 5, 6, 3]<br/>[1, 2, 3, 4, 5, 6]<br/><br/>Process finished with exit code 0 |
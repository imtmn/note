# 基本说明

`BlockingQueue` **阻塞队列**，什么是阻塞队列呢？

**阻塞队列**：当队列已满时，生成者会被阻塞；当队列为空时，消费者会被阻塞；

**队列**还可以分为**有限队列**和**无限队列**，**有限队列**是**指定**队列**容量**大小的队列，无限队列则是**不指定**队列**容量**大小，实际上也不是真的无限大，而是`Integer.MAX_VALUE`

![BlockingQueue](http://cdn.mtmn.top/image-4500495ddfae490bbc8487661d0a2383.png)

接下来看看`BlockingQueue`的继承关系，`BlockingQueue` 是阻塞队列，其实现类应该是**线程安全**的队列`Queue`，关于队列可以看[Queue队列接口说明](http://www.mtmn.top/archives/queue)

![BlockingQueue](http://cdn.mtmn.top/image-09642a0f7e224af5b8ae95196854833f.png)



`BlockingQueue`的接口方法

![BlockingQueue的方法](http://cdn.mtmn.top/image-c223326fe030427db0921293e1067620.png)

阻塞队列的操作，当队列为空继续检索、检出、删除或队列满时继续插入 可能有四种情况：

1. 直接抛出异常
2. 返回特殊值 false/null
3. 等待直到成功
4. 等待直到限定期限放弃

| 操作               | 抛出异常的方法 | 返回特殊值的方法（null/false） | 阻塞（直到成功） | 阻塞（直到超时）   |
| ------------------ | -------------- | ------------------------------ | ---------------- | ------------------ |
| 插入               | add(e)         | offer(e)                       | put(e)           | offer(e,time,unit) |
| 检出（查询并删除） | remove()       | poll()                         | take()           | poll(time,unit)    |
| 检索（查询不删除） | element()      | peek()                         | 无               | 无                 |



# 使用场景

`BlockingQueue`主要是用于**生产者-消费者**模型，这边**oracle**提供的接口文档也提供了示例

```
class Producer implements Runnable {
   private final BlockingQueue queue;
   Producer(BlockingQueue q) { queue = q; }
   public void run() {
     try {
       while (true) { queue.put(produce()); }
     } catch (InterruptedException ex) { ... handle ...}
   }
   Object produce() { ... }
 }

 class Consumer implements Runnable {
   private final BlockingQueue queue;
   Consumer(BlockingQueue q) { queue = q; }
   public void run() {
     try {
       while (true) { consume(queue.take()); }
     } catch (InterruptedException ex) { ... handle ...}
   }
   void consume(Object x) { ... }
 }

 class Setup {
   void main() {
     BlockingQueue q = new SomeQueueImplementation();
     Producer p = new Producer(q);
     Consumer c1 = new Consumer(q);
     Consumer c2 = new Consumer(q);
     new Thread(p).start();
     new Thread(c1).start();
     new Thread(c2).start();
   }
 }
```

所以小伙伴们，以后想要实现生产者-消费者模型，直接用`BlockingQueue`是不是很简单呢？
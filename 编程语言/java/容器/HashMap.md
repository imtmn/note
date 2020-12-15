> 本篇文章关于`HashMap`的说明基于JDK1.8

<span style="color:red">HashMap</span> 数据结构

.

- 基本概念

容量：当前Map的容量

resize 的操作

​	final float loadFactor;//负载因子 默认是0.75

Node[] table的默认长度length 是16， threshold = length * Load factor



当节点数量超过threshold 时，hashMap 就会进行resize(扩容)操作



红黑树，特点:快速增删改查



- put  

  1. hash key

  2. 如果table为空，初始化表格，默认长度16   resize

  3. 表格最后一个元素和当前hash进行&操作，如果为空，表格最后一个元素新增一个链表 -- **没搞懂**

  4. 否则将元素插入链表或红黑树

     如果key和hash都相同，节点修改成新节点

     如果属于红黑树，在红黑树插入节点

     否则，在链表最后一个节点插入，当链表长度大于8时，转成红黑树treeifyBin

  5. 

- HashMap 如何减少hash碰撞，保证hash桶的均匀分配呢？

  好的Hash算法和扩容









当key 为可变对象时，key 指向的是对象的引用，当对象发生改变时key也随之改变 

例如

```java
    static class Key{
        private String key;

        public Key(String key) {
            this.key = key;
        }

        public void setKey(String key) {
            this.key = key;
        }

    }
    public static void main(String[] args) {
        Map map = new HashMap<Key, String>();
        //新建一个key对象
        Key sourceKey = new Key("1");
        map.put(sourceKey, "1");
        //根据key对象取值
        System.out.println("原Key对象取值："+map.get(sourceKey));
        System.out.println("新增Key对象取值："+map.get(new Key("1")));
        //修改原key对象
        sourceKey.setKey("0");
        System.out.println("修改原Key对象取值："+map.get(sourceKey));
    }
```

| 输出                                               |
| -------------------------------------------------- |
| 当前Key：[1]<br/>1<br/>null<br/>当前Key：[0]<br/>1 |

这边会发现，新增一个相同对象取取值，依然无法取到值。接下去，重写下Key的`hashCode`方法试试，将key对象修改为

```java
    static class Key{
        private String key;

        public Key(String key) {
            this.key = key;
        }

        public void setKey(String key) {
            this.key = key;
        }

        @Override
        public int hashCode() {
            return Objects.hash(key);
        }
    }
```

| 继续执行上面的测试方法，输出                                 |
| ------------------------------------------------------------ |
| 原Key对象取值：1<br/>新增Key对象取值：null<br/>修改原Key对象取值：null |

新增对象和修改原对象都无法取到值，继续添加`equals`方法

```java
    static class Key{
        private String key;

        public Key(String key) {
            this.key = key;
        }

        public void setKey(String key) {
            this.key = key;
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            Key key1 = (Key) o;
            return Objects.equals(key, key1.key);
        }

        @Override
        public int hashCode() {
            return Objects.hash(key);
        }
    }
```

| 输出                                                         |
| ------------------------------------------------------------ |
| 原Key对象取值：1<br/>新增Key对象取值：1<br/>修改原Key对象取值：null |



HashMap线程不安全，HashTable与Hash类似，他内部通过synchronized实现了线程安全
HashMap键和值都可以为null,但是HashTable不行
非并发场景推荐使用HashMap，并发场景推荐使用ConcurrentHashMap



HashMap
1、根据键保存和获取值的效率都很高，为O(1)，每个单项链表往往只有一个或少数几个节点，根据hash 值就可以直接快速定位
2、HashMap中的键值没有顺序，因为hash值是随机的。
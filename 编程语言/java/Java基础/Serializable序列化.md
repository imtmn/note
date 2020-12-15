# 什么是序列化

**序列化**是将java对象转化成字节流的一个过程，既然有序列化那就有反序列化，**反序列化**就是将字节流转换成java对象的过程。

![java序列化](http://cdn.mtmn.top/image-1f68f30bf5d6411690d72d8814a485a1.png)

根据上图，应该大家对序列化都有了初步的了解，那为什么要序列化呢？其实答案就是上图的应用场景，主要用于**网络传输**和**对象存储**。这跟图片、音视频的传输有点类似，在计算机网络中，图片和音视频的传输也是字节流的形式存在。

![图片传输](http://cdn.mtmn.top/image-f27830441355406f8af092830adb6839.png)

其实对象传输也有其他途径，可以将对象转换成JSON、xml等形式的数据。

# 如何实现序列化

1. 首先新建个测试实体，咱们这边建了一个青蛙实体，要对实体进行序列化，需要实现<font color="#dd0000">Serializable</font>接口

   <font color="#dd0000">Serializable</font>接口属于标记型方法，本身没有声明任何方法。

   ```java
   import java.io.Serializable;
   
   /**
    * 青蛙
    */
   public class Frog implements Serializable {
       //品种
       private String species;
   
       public Frog(String species) {
           this.species = species;
       }
   
       @Override
       public String toString() {
           return "Frog{" +
                   "species='" + species + '\'' +
                   '}';
       }
   }
   ```

2. 实例化一只青蛙王子，并且对其进行序列化

   序列化第一步 创建<font color="#dd0000">ObjectOutputStream</font>对象

   序列化第二部 调用<font color="#dd0000">ObjectOutputStream</font> 的`writeObject`方法

   ```java
   public class Test {
       public static void main(String[] args) throws IOException{
           //青蛙王子
           Frog frogPrince = new Frog("王子");
   
           //序列化
           ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("frog"));
           oos.writeObject(frogPrince);
       }
   }
   ```

3. 上面的代码，将序列化对象青蛙王子保存到了frog文件中，现在咱们读取文件进行反序列化

   反序列化第一步 创建<font color="#dd0000">ObjectInputStream</font>对象

   反序列化第二部 调用<font color="#dd0000">ObjectInputStream</font>对象的`readObject`方法

   ```java
   public class Test {
       public static void main(String[] args) throws IOException, ClassNotFoundException {
           //反序列化
           ObjectInputStream ois = new ObjectInputStream(new FileInputStream("frog"));
           Frog frog = (Frog) ois.readObject();
   
           System.out.println(frog.toString());
       }
   }
   ```

   输出：

   Frog{species='王子'}


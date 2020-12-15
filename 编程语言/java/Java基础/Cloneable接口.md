# Cloneable简单介绍

> A class implements the `Cloneable` interface to indicate to the [`Object.clone()`](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html#clone--) method that it is legal for that method to make a field-for-field copy of instances of that class.
>
> Invoking Object's clone method on an instance that does not implement the `Cloneable` interface results in the exception `CloneNotSupportedException` being thrown.
>
> By convention, classes that implement this interface should override `Object.clone` (which is protected) with a public method. See [`Object.clone()`](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html#clone--) for details on overriding this method.
>
> Note that this interface does *not* contain the `clone` method. Therefore, it is not possible to clone an object merely by virtue of the fact that it implements this interface. Even if the clone method is invoked reflectively, there is no guarantee that it will succeed.

上面这段是官方解释，意思是说一个类实现`Cloneable`接口，调用`Object.clone()`对类进行拷贝就是合法的，如果没有声明直接调用会抛出`CloneNotSupportException`，这边还让咱们注意，不是重写了clone就能进行对象拷贝，即使用类反射也不行。

接下来，咱们进一步来进一步验证下

1. 这边以青蛙实体为例，先创建一个实体类

   ```java
   /**
    * 青蛙
    */
   public class Frog {
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

2. 现在咱们来复制一只青蛙王子试试

   ```java
   public class Test {
       public static void main(String[] args) {
           //青蛙王子
           Frog frogPrince = new Frog("王子");
           try {
               //克隆一只青蛙王子
               Frog cloneFrog = (Frog) frogPrince.clone();
               System.out.println(cloneFrog.toString());
           } catch (CloneNotSupportedException e) {
               e.printStackTrace();
           }
       }
   }
   ```

   结果：

   java.lang.CloneNotSupportedException: cloneable.Frog
   	at java.lang.Object.clone(Native Method)
   	at cloneable.Frog.clone(Frog.java:16)
   	at cloneable.Test.main(Test.java:9)

3. 正如官方所说，没有接口声明，直接调用是不行的。那咱们再验证下，反射是否能正常调用

   ```java
   public class Test {
       public static void main(String[] args) {
           //青蛙王子
           Frog frogPrince = new Frog("王子");
           try {
               Class clazz = frogPrince.getClass();
               Method cloneMethod = clazz.getDeclaredMethod("clone");
               //克隆一只青蛙王子
               Frog cloneFrog = (Frog) cloneMethod.invoke(frogPrince);
               System.out.println(cloneFrog.toString());
           } catch (Exception e) {
               e.printStackTrace();
           }
       }
   }
   ```

   结果依然如官方所说，不行滴

   java.lang.reflect.InvocationTargetException
   	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
   	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
   	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
   	at java.lang.reflect.Method.invoke(Method.java:498)
   	at cloneable.Test.main(Test.java:13)
   Caused by: java.lang.CloneNotSupportedException: cloneable.Frog
   	at java.lang.Object.clone(Native Method)
   	at cloneable.Frog.clone(Frog.java:16)
   	... 5 more

4. 按规矩办事，将`Cloneable`接口安排上

```java
/**
 * 青蛙
 */
public class Frog implements Cloneable{
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
    
    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```
5. 测试上面**步骤2**和**步骤3**，得到结果是一致的，都是**正常输出**

Frog{species='王子'}



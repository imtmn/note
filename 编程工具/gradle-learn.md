# gradle

gradle 是一款基于Jvm 的构建工具，gradle结合了maven 和ant的有点，她具有ant的强大和灵活，又有maven的生命周期管理且易于使用。

在gradle中：

待编译的工程叫做project，每一个构建由一个或多个project构成

每个project中包含一系列的task

一个project到底需要多少个task，可以用plugins赖一定

### 工作流程

initialization —> configuration -> Execution







## 0、gradle的两个概念projects和tasks

## 1、wrapper 

读音：wrapper  /ˈræpər/

```shell
gradle wrapper 
```

保存gradle版本信息

```shell
./gradlew
```

如果没有对应版本的gradle，会先去下载对应版本的gradle.保证运行构建的版本和当初构建的版本是一致的

停止

## 2、gradleUserHome

```js
├── build-scan-data               
|						 
├── caches    本地缓存，类似于maven的.m2，但是又不局限于此.      
|
├── daemon   执行指定是用client进程，真正执行是发给daemon进程（第一次启动后常驻）去执行，这
| 是由于如果每次Client进程去执行的话，启动JVM和加载jar会很费时间 ，--no-daemon可以关闭daemon   
|  						 
├── native               
│   
├── notifications                
|						 
├── workers             
│   
└── wrapper 保存gradlew执行的版本号               
  
```



## 3、gradlew

1. 运行

   ```shell
   ./gradlew
   ```

   执行流程：

   1、执行脚本 ./gradlew compileJava

   2、启动一个轻量的JVM

   3、判断gradle版本是否存在，如果不存在进行下载

   4、查找对应版本的daemon，如果没找到则启动一个daemon，所以第一次启动较慢，后续启动速度会快很多

   5、连接daemon

2. 停止

   ```
   ./gradlew --stop
   ```

3. 帮助

   ```
   ./gradlew --help
   ```



### 4、groovy

运行在jvm之上的脚本语言，强类型，但是动态调用的。 

groovy in action2



### 5、gradle 生命周期

初始化（Initialization）

配置(Configuration)

​	->  执行build.gradle



afterEvaluate 钩子函数，在configure后执行

发现函数会先去project里面查找有没有对应的方法，



buildScript{

​	//在这边加依赖相当于给脚本加依赖，可以在脚本中直接使用第三方库

}

buildsrc 插件





闭包
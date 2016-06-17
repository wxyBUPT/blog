#天池竞赛中遇到的问题

##Tair 与 Jstorm 的slf4j-api 冲突

###问题发现与原因

####问题发现

代码中添加 Tair 逻辑的时候总有错误，通过 mvn dependency:tree 发现

具体错误如下：

* 使用 tair-client 的slf4j-api 报错如下
  
```
xiyuanBuptdeMacBook-Pro-4696:PreliminaryDemo xiyuanbupt$ java -cp ./target/preliminary.demo-1.0-SNAPSHOT.jar com.alibaba.middleware.race.Tair.TairOperatorImpl
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
Exception in thread "main" java.lang.NoClassDefFoundError: org/slf4j/impl/StaticLoggerBinder
	at org.slf4j.LoggerFactory.<clinit>(LoggerFactory.java:60)
```  

* 使用 jstorm 的 slf4j-api 报错如下 

``` 
xiyuanBuptdeMacBook-Pro-4696:PreliminaryDemo xiyuanbupt$ java -cp ./target/preliminary.demo-1.0-SNAPSHOT.jar com.alibaba.middleware.race.Tair.TairOperatorImpl
Exception in thread "main" java.lang.NoClassDefFoundError: org/slf4j/LoggerFactory
	at org.apache.mina.filter.executor.ExecutorFilter.<init>(ExecutorFilter.java:48)
	at org.apache.mina.filter.executor.ExecutorFilter.<init>(ExecutorFilter.java:57)
	at org.apache.mina.common.ExecutorThreadModel.<init>(ExecutorThreadModel.java:77)
	at org.apache.mina.common.ExecutorThreadModel.getInstance(ExecutorThreadModel.java:65)
	at org.apache.mina.common.support.BaseIoServiceConfig.<init>(BaseIoServiceConfig.java:45)
	at org.apache.mina.common.support.BaseIoConnectorConfig.<init>(BaseIoConnectorConfig.java:35)
	at org.apache.mina.transport.socket.nio.SocketConnectorConfig.<init>(SocketConnectorConfig.java:40)
	at org.apache.mina.transport.socket.nio.SocketConnector.<init>(SocketConnector.java:62)
	at com.taobao.tair.comm.TairClientFactory.<init>(TairClientFactory.java:56)
	at com.taobao.tair.comm.TairClientFactory.<clinit>(TairClientFactory.java:46)
	at com.taobao.tair.impl.DefaultTairManager.<init>(DefaultTairManager.java:79)
	at com.taobao.tair.impl.DefaultTairManager.<init>(DefaultTairManager.java:73)
	at com.alibaba.middleware.race.Tair.TairOperatorImpl.<init>(TairOperatorImpl.java:20)
	at com.alibaba.middleware.race.Tair.TairOperatorImpl.main(TairOperatorImpl.java:69)
Caused by: java.lang.ClassNotFoundException: org.slf4j.LoggerFactory
	at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
	at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:331)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
``` 
####原因

Tair-client 的 slf4j-api 版本是 1.4.3
jstorm 的 slf4j-api 的版本是 1.75  

jstorm 使用了logback 的日志框架，logback 的jar 包版本要比 slf4f 的版本高。 slf4j 的 1.7.5 版本中LoggerFactory 中相关的代码被重写了。


###解决办法

添加依赖  

```
<dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>jcl-over-slf4j</artifactId>
        <version>1.6.1</version>
</dependency>
```  

#### jcl-over-slf4j 介绍

jcl是为了解决两个组件的不同日志输出问题的。jcl 只提供log 接口，但是程序多的时候 jcl 的动态绑定不是总能成功，解决办法是在程序部署的时候静态绑定指定的日志工具，所以有了 slf4f


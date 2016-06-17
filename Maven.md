#Maven 笔记

##比较棘手的问题

###遇到第一个比较棘手的问题：Mvn依赖 的exclusion  

在使用 Tair 的过程中出现如下Exception

```
xiyuanBuptdeMacBook-Pro-4155:wxyRace xiyuanbupt$ java -cp ./target/wxyRace-1.0-SNAPSHOT.jar com.alibaba.middleware.race.tair.LearnTair
Exception in thread "main" java.lang.NoSuchMethodError: org.slf4j.spi.LocationAwareLogger.log(Lorg/slf4j/Marker;Ljava/lang/String;ILjava/lang/String;Ljava/lang/Throwable;)V
	at org.apache.commons.logging.impl.SLF4JLocationAwareLog.info(SLF4JLocationAwareLog.java:141)
	at com.taobao.tair.impl.ConfigServer.retrieveConfigure(ConfigServer.java:156)
	at com.taobao.tair.impl.DefaultTairManager.init(DefaultTairManager.java:97)
	at com.alibaba.middleware.race.tair.LearnTair.main(LearnTair.java:23)
```  

于是决定一步一步解决，步骤如下：


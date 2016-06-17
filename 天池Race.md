#阿里中间件性能挑战赛第一季赛题笔记

##题目

###比赛环境

每个拓扑总计算资源：6核、12G内存
拓扑运行最大worker（进程数量） 数量不超过4个

##要解决的问题

* 题目以准确性为评判标准，所以Tair 访问是否要加同步，同步的位置设置在哪里？
* OrderMessage 和 PayMessage 放在不同的topic 中，并且平台信息存储在OrderMessage 中，消费三个队列的方式也要考虑。可能程序消费PayMessage 消费的比较快，就找不到相关的平台信息。

##策略 

##实现

###程序1

对于每分钟交易量：在程序的内存中计算实时信息，并定时写入Tair。存在的问题是程序的内存会越来越大。但是好在每天的分钟数不多，现实使用中不是那么靠谱。


##添加rocketMq-storm 依赖

rocketmq-storm 地址 [rocketmq-storm](https://github.com/rocketmq/rocketmq-storm.git), git clone 之后使用mvn install 安装，然后在自己的项目中的pom.xml 文件中添加如下依赖：

```  
    <dependency>
      <groupId>com.alibaba.rocketmq</groupId>
      <artifactId>rocketmq-storm</artifactId>
      <version>1.0.0-SNAPSHOT</version>
    </dependency>
    
```

mvn clean install 之后可以使用rocketmq 的spout
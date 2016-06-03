#Kafka学习笔记

启动kafka
bin/zookeeper-server-start.sh config/zookeeper.properties

bin/kafka-server-start.sh config/server.properties

创建一个topic  

bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 -- partitions 1 --topic test


列出所有topic

bin/kafka-topics.sh --list --zookeeper localhost:2181

收发消息  
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test > /dev/null  

收取message  
bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning 2 > /dev/null  


**一个花费很多时间的配置**

配置的目的是为了能够在远程主机连接到kafak，最后相关的配置项如下  

**listeners=PLAINTEXT://10.109.247.29:9092**

解释如下
  
  
```
 # The address the socket server listens on. It will get the value returned from
 # java.net.InetAddress.getCanonicalHostName() if not configured.
 #   FORMAT:
 #     listeners = security_protocol://host_name:port
 #   EXAMPLE:
 #     listeners = PLAINTEXT://your.host.name:9092
 #listeners=PLAINTEXT://:9092
 #使得在任意地址均可以访问kafka
 listeners=PLAINTEXT://10.109.247.29:9092

 # Hostname and port the broker will advertise to producers and consumers. If not set,
 # it uses the value for "listeners" if configured.  Otherwise, it will use the value
 # returned from java.net.InetAddress.getCanonicalHostName().
 #advertised.listeners=PLAINTEXT://your.host.name:9092
```   

 
教训：  

* 正确的google方式能够节省不少的时间，例如这个问题是查找 remote connect to kafka ，之前google 了很多其他内容都是徒劳的。
* 使用自己熟悉的方式解决问题，之前尝试了java 解决问题，不光因为自己不熟悉java，而且编译找错同时很浪费时间。

##storm-kafka Scheme 是什么

Scheme 主要负责从消息流中解析出所需要的数据

```
public interface Scheme extends Serializable {
    public List<Object> deserialize(byte[] ser);
    public Fields getOutputFields();
}
```

下面是StringScheme 的实现  

```  

public class StringScheme implements Scheme {
    public static final String STRING_SCHEME_KEY = "str";

    public StringScheme() {
    }

    public List<Object> deserialize(byte[] bytes) {
        return new Values(new Object[]{deserializeString(bytes)});
    }

    public static String deserializeString(byte[] string) {
        try {
            return new String(string, "UTF-8");
        } catch (UnsupportedEncodingException var2) {
            throw new RuntimeException(var2);
        }
    }

    public Fields getOutputFields() {
        return new Fields(new String[]{"str"});
    }
}
```  



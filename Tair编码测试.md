#Tair编码测试  

*主要根据文档： [阿里云Tair](http://code.taobao.org/p/tair/wiki/guide/) 学习使用* 

测试是否可以使用 tairclient (-g 参数视具体情况而定)

```
./tairclient -c 115.28.93.106 -g group_1 -v
```


mvn 依赖  

```
<!-- http://mvnrepository.com/artifact/com.taobao.tair/tair-client -->
<dependency>
    <groupId>com.taobao.tair</groupId>
    <artifactId>tair-client</artifactId>
    <version>2.3.4</version>
</dependency>
```  

中间遇到了依赖问题
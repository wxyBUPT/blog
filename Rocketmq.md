#RocketMQ 安装运行

##下载

```
wget https://github.com/alibaba/RocketMQ/releases/download/v3.2.6/alibaba-rocketmq-3.2.6.tar.gz
tar -xzf alibaba-rocketmq-3.2.6.tar.gz
```  

##部署NameServer

```
cd alibaba-rocketmq/bin/
nohup sh mqnamesrv &
```  

##部署broker（因为单机，只部署Master）

``` 
nohup sh mqbroker -n "10.109.247.29:9876" -c ../conf/2m-2s-async/broker-a.properties &
```  

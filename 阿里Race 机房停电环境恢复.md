#阿里Race 机房停机环境恢复

* Zookeeper启动  ./bin/zkServer.sh start 
* webUI启动 tmocat  ./bin/startup.sh
* jstorm 启动，在 nimbus 节点上执行 nohup jstorm nimbus & 
* 在 supervisor 节点上执行 nohup jstorm supervisor & 
* 启动rocketmq 进入 /alibaba-rocketmq/bin 调用  nohup sh mqnamesrv & 启动 NameServer
* 因为是单机部署，所以启动单 Master  nohup sh mqbroker -n "10.109.247.29:9876" -c ../conf/2m-2s-async/broker-a.properties &  所有环境启动成功


```
#!/bin/sh

 if [ -e ~/.bashrc ]
 then
     source ~/.bashrc
 fi

 if [ -e ~/.bash_profile ]
 then
     source ~/.bash_profile
 fi

 if [ "x$ZOOKEEPER_HOME" != "x" ]
 then
     echo "ZOOKEEPER_HOME has been set "
     echo "Start zkServer "
     $ZOOKEEPER_HOME/bin/zkServer.sh start
 else
     echo "ZOOKEEPER_HOME has not been set"
     exit 1
 fi

 if [ "x$TOMCAT_HOME" != "x" ]
 then
     echo "TOMCAT_HOME has been set"
     echo "Start webUI"
     $TOMCAT_HOME/bin/startup.sh
 else
     echo "TOMCAT_HOME has non't been set"
     exit 1
 fi

 echo "Start jStrom nimbus "

 nohup jstorm nimbus &

 echo "nimbus status"
 find . -name 'nohup.out' -exec cat {} \;
 echo "rm nohup.out"

 find . -name 'nohup.out' -exec rm -f {} \;

 echo "start jstorm supervisor "
 
 
 nohup jstrom supervisor &
 echo "supervisor status"
 find . -name 'nohup.out' -exec cat {} \'
 echo "rm nohup.out"

 find . -name 'nohup.out' -exec rm -f {} \;

 echo "start rocketmq NameServer"
 if [ "x$ROCKETMQ_HOME" != "x" ]
 then
     echo "ROCKETMQ_HOME has been set"
     echo "Start RocketMq NameServer"
     nohup sh $ROCKETMQ_HOME/bin/mqnamesrv &
     echo "mqnamesrv status "
     find . -name 'nohup.out' -exec cat {} \;
     echo "rm nohup.out"
     find . -name 'nohup.out' -exec rm -f {} \;
     echo "Start RocketMq Master "
     nohup sh $ROCKETMQ_HOME/bin/mqbroker -n \"$NAMESRV_ADDR\" -c $ROCKETMQ_HOME/conf/2m-2s- async/broker-a.properties &
     echo "Master status"
     find . -name 'nohup.out' -exec cat {} \;
     echo "rm nohup .out"
     find . -name 'nohup.out' -exec rm -f {} \;
 else
     echo "ROCKETMQ_HOME has not been set"
     exit 1
 fi

 echo "all done , please check"
```

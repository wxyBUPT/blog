#Redis 安装使用笔记

##下面为安装旧版本的redis，jh29的redis已经更新
在交换29机器上通过如下命令安装了redis  
sudo apt-get install redis-server 

安装之后redis 自动启动。  
如果需要更改配置项目，例如需要更改redis 为局域网内机器能够访问，执行了如下两个步骤：  

*  sudo vim /etc/redis/redis.conf
*  执行 sudo /etc/init.d/redis-server --help 

##新的redis安装在 wxy 的家目录下，启动方式如下

nohup ./src/redis-server ./redis.conf &


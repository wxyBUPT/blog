#交换29机器的redis 安装与配置

```
wget http://download.redis.io/releases/redis-3.2.1.tar.gz
tar xzf redis-3.2.1.tar.gz
cd redis-3.2.1
make 
./src/redis-server ./redis.conf --daemonize yes
```  


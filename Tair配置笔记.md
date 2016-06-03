#Tair 配置笔记

**Tair 安装参见 [Tair安装](https://github.com/wxyBUPT/blog/blob/master/Tair%E5%AE%89%E8%A3%85%E7%AC%94%E8%AE%B0.md)**

资源有限，尝试使用单机配置 

##开始配置

###配置configserver.conf

配置如下  

``` 
[root@iZ28jtz3hm3Z etc]# cat ~/tair_bin/etc/configserver.conf
#
# tair 2.3 --- configserver config
#

[public]
config_server=x.x.x.x:5198
#config_server=192.168.1.2:5198

[configserver]
port=5198
#log_file=logs/config.log
log_file=/tmp/tair/log/config.log
#pid_file=logs/config.pid
pid_file=/tmp/tair/log/config.pid
log_level=warn
group_file=/root/tair_bin/etc/group.conf
#data_dir=data/data
data_dir=/tmp/tair/data/configserver
dev_name=eth0
```  

###配置group.conf 

原始配置基本没有更改，只更改了 _server_list 部分

```
_server_list=115.28.93.106:5191
```

###配置dataserver  

因为配置项较长，所以只列出了更改的部分，配置文件中建议单机配置可以忽略 mdb_shm_path 选项，但是配置并没有忽略。


```  
#因为单机配置资源有限，所以限制了mdb 
slab_mem_size=512
log_file=/tmp/tair/log/dataserver/server.log
pid_file=/tmp/tair/log/dataserver/server.pid

```

其他配置作为默认配置未更改 


##运行前准备

注意下面的脚本是在编译前的文件夹中，并需要使用root 权限执行。

```
sh ~/tair/scripts/set_shm.sh
```

##单机启动

通过安装文件下的 tair.sh 启动集群

~/tair_bin/tair.sh start_ds 启动data server

~/tair_bin/tair.sh start_cs 启动config server(不知道执行此命令没有反应是不是因为启动失败了，并且使用ps aux | grep 的时候没有看到相应的进程信息) 可以通过如下的方式确认执行失败 

```
netstat -ant | grep 5198 
```

如果没有看到端口信息，则未能执行成功。

###解决办法

查看日志  

```
less /tmp/tair/log/config.log 
```
显示 

``` 
[2016-06-03 15:06:33.223873] ERROR initialize (tair_cfg_svr.cpp:130) [139842555987744] my Ip 10.163.179.176:5198 is not in the list of config_server check it out.
```  

解决方法：

* 查看所有的配置文件中是否有类似于 10.163.179.176:5198 的内容。 cat /root/tair_bin/etc/* | grep 10.163.176.176  ，发现没有。
* 查看 hosts 配置文件 cat /etc/hosts （但是问题并不在此）


```
[root@iZ28jtz3hm3Z etc]# cat /etc/hosts
127.0.0.1 localhost
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.163.179.176 iZ28jtz3hm3Z
```
* 查看各个网卡信息（问题出现在这里） ifconfig 

```
[root@iZ28jtz3hm3Z tair_bin]# ifconfig
eth0      Link encap:Ethernet  HWaddr 00:16:3E:00:3B:7C
          inet addr:10.163.179.176  Bcast:10.163.191.255  Mask:255.255.240.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:1087 errors:0 dropped:0 overruns:0 frame:0
          TX packets:1531 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:98040 (95.7 KiB)  TX bytes:125117 (122.1 KiB)
          Interrupt:165

eth1      Link encap:Ethernet  HWaddr 00:16:3E:00:14:49
          inet addr:x.x.x.x    Bcast:115.28.95.255  Mask:255.255.252.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:39512 errors:0 dropped:0 overruns:0 frame:0
          TX packets:18206 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:31513827 (30.0 MiB)  TX bytes:2765117 (2.6 MiB)
          Interrupt:164
          
```

发现需要使用的ip 没有分配给 eth0，马上更改，尝试是否可以启动。通过 netstat -ant | grep 5198  发现端口被占用，使用ps 也可以发现进程。 

```
[root@iZ28jtz3hm3Z tair_bin]# ps aux | grep tair
root     10764  0.1 16.0 1511772 163720 pts/1  Sl   15:04   0:06 ./sbin/tair_server -f ./etc/dataserver.conf
root     11850  0.0  0.2 648280  2080 pts/1    Sl   16:39   0:00 ./sbin/tair_cfg_svr -f ./etc/configserver.conf
root     11869  0.0  0.0 103224   792 pts/1    S+   16:41   0:00 grep tair
```
但是查看日志发现 ERROR 和 WARN

```
[2016-06-03 16:39:47.248634] ERROR open (conf_server_table_manager.cpp:267) [139958573475584] file (/tmp/tair/data/configserver/group_1_server_table) not exit
```

但是后来发现Tair ConfigServer 已经帮助创建

##存在问题与解决办法

###不能使用 tair.sh 控制进程

查看 tair.sh 代码发现所有的kill 都是通过类似于

```
 stop_cs()
 {
  kill `cat logs/config.pid`
 }
```  
方式实现，所以将所有的 pid_file 配置项都改回默认，reboot。





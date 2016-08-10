#Mongo 运维笔记

在声学所的机器上安装了mongo，使用了sudo 权限启动了mongo，命令是 
mongod --config /etc/mongod.conf 

##  June 14 2016 出现故障

###原因：
没有细查，但是可能原因是mongo 压力太大，该建索引的没有创建索引，执行的插入操作过多（六个进程一直不停的插入）

###修复：

删除mongo pid 文件，删除mongo log文件，删除mongo lock 文件，
执行 mongod --repair --dbpath /var/lib/mongo --repairpath /tmp/mongodb 

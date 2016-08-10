#天池Race学习笔记（2016第二赛季）

Race2

##MongoDB 的存储结构

**[how-big-is-your-mongodb](http://blog.mlab.com/2014/01/how-big-is-your-mongodb/)**  

mongo 中nsSizeMB 是16 ,并且创建了之后不可以改变，也就是说ns 都是16MB  

mongo 中的bTree 是 4k

淘宝的一篇数据库内核月报:  **[MongoDB特性分析 MMAPv1存储引擎原理](http://mysql.taobao.org/)**


**注意**   
mongo 中 db.stats() 中的单位都是 bytes

```  
> db.stats()
{
	"db" : "tianchi",
	"collections" : 6,
	"objects" : 10814,
	"avgObjSize" : 293.5479933419641,
	"dataSize" : 3174428,
	"storageSize" : 6459392,
	"numExtents" : 14,
	"indexes" : 4,
	"indexSize" : 392448,
	"fileSize" : 201326592,
	"nsSizeMB" : 16,
	"ok" : 1
}
``` 

validate 相关内容

```  
> db.runCommand({validate:"order_records",full:true})
{
	"ns" : "tianchi.order_records",
	"firstExtent" : "0:e000 ns:tianchi.order_records",
	"lastExtent" : "0:18f000 ns:tianchi.order_records",
	"extentCount" : 5,
	"extents" : [
		{
			"loc" : "0:e000",
			"xnext" : "0:1b000",
			"xprev" : "null",
			"nsdiag" : "tianchi.order_records",
			"size" : 16384,
			"firstRecord" : "0:e0b0",
			"lastRecord" : "0:11ee4"
		},
		{
			"loc" : "0:1b000",
			"xnext" : "0:2b000",
			"xprev" : "0:e000",
			"nsdiag" : "tianchi.order_records",
			"size" : 65536,
			"firstRecord" : "0:1b0b0",
			"lastRecord" : "0:2aea8"
		},
		{
			"loc" : "0:2b000",
			"xnext" : "0:8f000",
			"xprev" : "0:1b000",
			"nsdiag" : "tianchi.order_records",
			"size" : 262144,
			"firstRecord" : "0:2b0b0",
			"lastRecord" : "0:6af0c"
		},
		{
			"loc" : "0:8f000",
			"xnext" : "0:18f000",
			"xprev" : "0:2b000",
			"nsdiag" : "tianchi.order_records",
			"size" : 1048576,
			"firstRecord" : "0:8f0b0",
			"lastRecord" : "0:18eecc"
		},
		{
			"loc" : "0:18f000",
			"xnext" : "null",
			"xprev" : "0:8f000",
			"nsdiag" : "tianchi.order_records",
			"size" : 4194304,
			"firstRecord" : "0:18f0b0",
			"lastRecord" : "0:31e604"
		}
	],
	"datasize" : 2867804,
	"nrecords" : 10000,
	"lastExtentSize" : 4194304,
	"padding" : 1,
	"firstExtentDetails" : {
		"loc" : "0:e000",
		"xnext" : "0:1b000",
		"xprev" : "null",
		"nsdiag" : "tianchi.order_records",
		"size" : 16384,
		"firstRecord" : "0:e0b0",
		"lastRecord" : "0:11ee4"
	},
	"objectsFound" : 10000,
	"invalidObjects" : 0,
	"bytesWithHeaders" : 3027804,
	"bytesWithoutHeaders" : 2867804,
	"deletedCount" : 3,
	"deletedSize" : 2558260,
	"nIndexes" : 3,
	"keysPerIndex" : {
		"tianchi.order_records.$_id_" : 10000,
		"tianchi.order_records.$orderid_1" : 10000,
		"tianchi.order_records.$createtime_1" : 10000
	},
	"valid" : true,
	"errors" : [ ],
	"ok" : 1
}
``` 




日志文件 -> 与数据存储无关

命名文件 dbname.ns 存储整个数据库的集合以及索引的名字，存储集合或者索引名以及集合和索引在数据文件中的具体位置。

数据文件  dbname.0 dbname.1, ....dbname.n mongo的数据及索引都存储在一个或者多个mongodb 数据文件里面。文件大小会从 64M 指数增加，直到增加到 2G 之后停止。

Extent  每一个数据文件内，mongoDb 把所有存储的BSON 文档的数据和 B树索引组织到容器 Extent 里面。

* 每个文件可以有多个 Extent
* 每个 Extent 只包含一个集合的数据或者索引
* 同一个集合的数据或索引可以分布在多个Extent 内。这几个Extent 又可以分布于多个文件内
* 同一个Extent 不会既有数据又有索引

Record 记录

里面存放的是具体的bson 数据以及 heading 与 padding

下图中一个extent 是一个物理磁盘页有错误，有关8B的一些记录有错误

![](http://7vzu8r.com1.z0.glb.clouddn.com/mongo_p1.png)

![](http://7vzu8r.com1.z0.glb.clouddn.com/mongo_m1.png)
![Mong 文件存储形式](http://7vzu8r.com1.z0.glb.clouddn.com/mongo_%E5%9B%BE%E7%89%873.png)

###Mongo 能够借鉴的几个关键的技术

* B树索引
* 内存映射表，实际上是 mmap 技术，将数据文件通过mmap 映射到虚拟内存，用到的时候再载入物理内存
* 

###自己想到的几个点子

* 数据分片存储
* 数据可以副本存储，Race 中给了三块磁盘，可以设计为三个副本，然后分别存储

##Innodb存储引擎

Inndodb 每页 16K 是管理磁盘最小的单位  , 每个Page 由一个32 位的int 值唯一标识·
Inndodb 每个页节点存储了 468 行的数据，每个非叶节点大约存储1200 个键值，这是一颗平衡的1200 路搜索树。

![](http://www.admin10000.com/UploadFiles/Document/201410/27/20141027091523976520.PNG)

[InnoDB 索引原理详解](http://www.admin10000.com/document/5372.html)  

* 索引指向文档在磁盘上的存储

##HBASE 的存储结构

##赛题


##思路

* 将赛题文件按照一定结构写入文件系统


##需要了解的相关知识

* 线程池
* Java 异步
* Java 回调机制
* Java 

##相关知识

* 虚拟内存  

![](http://incdn1.b0.upaiyun.com/2014/12/3efe3d5db9fe2e9bee8b0e27c9de60d3.png)
* 磁盘块大小一般是 512 字节，典型的内存页面大小一般是 1024、2048、4096
* 
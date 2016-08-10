#问题

**我程序大概的思想是这样的（还没有加缓存）：**  

##construct 阶段：  

原始数据保存为**Demo**中的Row 格式。 

并简单的序列化存储到磁盘（使用MappedByteBuffer）每个文件1G，根据每条记录的索引 key 值类似下面代码： 

```  
public class ComparableKeysByGoodId implements Indexable,Comparable<ComparableKeysByGoodId>,Serializable{

    private String goodId;
    private DiskLoc dataDiskLoc;

    public ComparableKeysByGoodId(String goodId,DiskLoc dataDiskLoc){
        this.goodId = goodId;
        this.dataDiskLoc = dataDiskLoc;
    }

    @Override
    public int compareTo(ComparableKeysByGoodId o) {
        return this.goodId.compareTo(o.goodId);
    }

    @Override
    public String toString(){
        return "ComparableKeysByGoodId: goodId: " + goodId;
    }

    @Override
    public DiskLoc getDataDiskLoc(){
        return dataDiskLoc;
    }

} 
```  

根据索引在磁盘中创建 b+ 树，b+ 树节点和叶子节点容量均为 1200 。

##查询阶段
根据key 值查询磁盘中的b+ 树，根据查询到的结果再访问磁盘

##<strong>问题</strong>

* 序列化使用 bytearrayoutputstream 直接序列化的，序列化后原始数据大了很多，是否有更好的序列化方式推荐？
* 程序设计是否有问题，（例如使用了MappedByteBuffer，叶节点容量，有没有必要在内存中持久化b+ 树的非叶子节点）？


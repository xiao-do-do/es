#Mapping 映射
对数据字段进行类型映射、分词器等

获取mapping映射参数


1.dynamic mapping   
2.explicit mapping  

mapping数据类型
```
GET /product/_mapping   
``` 
ES数据类型  
```
数字      long integer short byte double float half_float scale_float unsigned long   
keywords 精确搜索，用于过滤、排序、聚合。  
date 时间 
alias 别名    
text    大数据全文搜索使用text, 字段会被分析生成倒排索引，一般不做排序、聚合，加载text会耗费大量堆控件。 正排索引适用于排序、聚合， 

object 对象类型，json
nested json数组，这两种用于保存复杂对象

结构化 
geo-point 经纬度
geo-shape: 多边形复杂形状
point 笛卡尔坐标点
shape    笛卡尔任意几边形  
binary 二进制  
range 区间    

````


#mapping参数
```
index true:false 是否创建倒排索引， false不能被索引搜到，但是仍会在_source里   
analyzer: 指定分词器     
doc_values: 为了提升排序和索引效率，禁用可以提升效率，少数情况需要开启。      
eager_global_ordinals: 用于聚合的字段，优化聚合性能       
enable 是否创建倒排索引，可以对字段炒作，也可以对索引操作，不指定字段则是全局  
fielddata 正排索引，在JVM内存里构建索引，排序聚合这个会非常消耗内存        
fields创建多个字段        
norms 是否禁用评分    
null_value 为null设置默认值       
search_analyzer 设置单独的查询分词器  
store 设置字段是否查询  
term_vecotr 运维参数    
```



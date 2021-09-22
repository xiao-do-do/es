#query
官方文档
https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html

```GET product/_search    
{   
    "_source":false
    "query":{   
       " match_all":{}  
    }   
}   
```
查询时候过滤字段  
source:false    

```
GET product2{
    "_source":"xxx.xx",
    "query":{   
       " match_all":{}  
    }   
}


GET product2{
    "_source":["xxx.xx","xxx.xx"],
    "query":{   
       " match_all":{}  
    }   
}


GET product2{
     "_source":{
        "includes":["xxx.xx","xxx.xx"],
        "excludes":["xxx.xx","xxx.xx"]
    },
    "query":{   
       " match_all":{}  
    }   
}


        
```


mapping禁用索引，过滤字段
节省储存开销
缺点  
不支持update、reindex
压缩索引比禁用_source更好
mapping不可变，不好改所以最好查询时候动态指定
```
PUT product2{
    "mappings":{
        "_source":{
            "includes":["",""],
            "excludes":["",""]
        }
    }
}
```



#Query
查询所有

```
GET /product/_search
```
#带参数

字段name带有小米的都会被查询出来
```
GET /product/_search?q=name:xiaomi
```

分页
```
GET /product/_search?form=0&size=2&sort=price:asc
```

查询日期
```
GET /product/_search?q=data:2021-06-1
```

不包含参数名，则会把所有有索引的都会被检索出来
```
GET /product/_search?q=:2021-06-1
```

不包含参数名，则会把所有 有索引的都会被检索出来
```
GET /product/_search?q=:2021-06-1
```


```
```
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



#match
 检索name包含xiaomi的数据， text会被分词    
```
GET product/_search
{
    "query"{
       "match":{
            "name":"xiaomi"
        }
    }
}

--------------------------------------------
GET product/_search
{
    "query"{
       "match_all":{}   
    }
}

--------------------------------------------
name包含 query任意一个或desc query包含任意一个就能搜出来
GET product/_search
{
    "query"{
       "multi_match":{
            "query":"phone huangmengji",
            "fields":["name","desc"]
       }   
    }
}

---------------------------------------------
同时包含且顺序一致，会被分词
GET product/_search
{
    "query"{
       "match_phrase":{
              "name":"xiaomi nfc"
       }   
    }
}
--------------------------------------------
获取text怎么被分词的
GET　＿analyze
{
    "analyzer":"standard",
    "text":"xiaomi nfc phone"
}

-----------------------------------------------
#exact query 精准匹配 
做了分词，所以在text中无法查到数据
GET /product/_search
{
    "query":{
        "term":{
            "name":"xiaomi phone"
        }
    }
}
term与match pharse区别
match pharse 可以分词，必须连续并包含，颠倒不行



-----------------------------------------------
term可keyword区别
动态映射会自动创建keywords, 
....
mappings:{
   "name":{
        "type":"text",
        "fields":{
            "keyword":{
                "type":"keyword", 
                "ignore_above":256 //忽略256大小后面的字符
            }
        }
   }
}


keyword保留前256个，所以通过name.keyword可以搜出来

  "query":{
        "term":{
            "name.keyword":"xiaomi phone"
        }
    }
    
----------------------------


terms , tags有，或b 就能查出来
GET product/_search
{
    "query":{
        "terms":{
           "tags":["a","b"]
        }
    }
}
-----------------
GET product/_search{
    "query":{
        "range":{
            "price":{
                "gte":399,
                "lte":2399
            }
        }
    }
}

//gt大于 lt小于， e等于  gte大于等于
//日期也可以， 大于等于前一天，小于等于今天
"gte":"now-1d/d",
"lte":"now/d"

"time_zone":"+08:00" 结果加8小时
-------------------------------

filter 不做排序，只查询，所以比query更快速，且有缓存
query做评分
GET product/_search
{
    "query":{
        "constant_scoore":{
            filter:{
                "filter":{
                    "name":"phone"
                }
            }
        },
        "boost":"1.2" //score=1.2, 默认0
    }
}



GET product/_search
{
    "query":{
        "bool":{
            filter:[
               {
                "terms":{
                    "name":"phone"
                }
               }
            }
        },
    }
}
作用就是筛选数据，filter可以先筛选再排序。
--------------------------------------

bool 组合查询
must,shoule会计算相关度评分， filter,must_not不计算评分
 

```
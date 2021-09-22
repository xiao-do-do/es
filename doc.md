#官方文档学习 QuickStart
https://www.eastic.co/guide/en/elasticsearch/reference/current/getting-started.html

添加一个文档记录，提交下面请求来提交一条日志记录到 log-my_app-default数据流中，因为log-my log-my_app-default不存在所有请求将会使用logs-*-*索引模板自动创建它
```
POST logs-my_app-default/_doc
{
    "@timestamp": "2099-05-06T16:21:15.000Z",
    "event": {
        "original": "192.0.2.42 - - [06/May/2099:16:21:15 +0000] \"GET /images/bg.jpg HTTP/1.0\" 200 24736"
    }
}
```
这个后备响应包含了es为这条数据生成的元数据metadata,后背索引包含了这个文档，es会自动生成备份索引名称,在索引中给这个文档唯一的ID  


#添加多个文档
在一个请求中使用_bulk端点添加多个文档， bulk数据必须在新的一行JSON数据，在每行的结尾必须换行\n结尾。

```
PUT logs-my_app-default/_bulk
{ "create": { } }
{ "@timestamp": "2099-05-07T16:24:32.000Z", "event": { "original": "192.0.2.242 - - [07/May/2020:16:24:32 -0500] \"GET /images/hm_nbg.jpg HTTP/1.0\" 304 0" } }
{ "create": { } }
{ "@timestamp": "2099-05-08T16:25:42.000Z", "event": { "original": "192.0.2.255 - - [08/May/2099:16:25:42 +0000] \"GET /favicon.ico HTTP/1.0\" 200 3638" } }
```


搜索数据
被索引的文档可以近乎实时的被搜索到，下面的搜索匹配了所有在log-my_app-default中的log日志 并且通过@timestamp降序排序
```
GET logs-my_app-default/_search
{
    "query": {
         "match_all": { }
    },
    "sort": [
    {
         "@timestamp": "desc"
    }
]
}
```




默认的，hist区域包含了前10个搜索结果，每个命中的_source包含了在索引期间提交的原JSON对象
```
{
    "took": 2,
    "timed_out": false,
    "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
},
    "hits": {
        "total": {
        "value": 3,
        "relation": "eq"
    },
    "max_score": null,
    "hits": [
    {
        "_index": ".ds-logs-my_app-default-2099-05-06-000001",
        "_type": "_doc",
        "_id": "PdjWongB9KPnaVm2IyaL",
        "_score": null,
        "_source": {
            "@timestamp": "2099-05-08T16:25:42.000Z",
        "event": {
            "original": "192.0.2.255 - - [08/May/2099:16:25:42 +0000] \"GET /favicon.ico HTTP/1.0\" 200 3638"
             }
        },
        "sort": [
            4081940742000
            ]
        },
        ...
        ]
    }
}
```


获取指定字段  
对于大的文档，展示所有的字段是不灵活不方便的，为了从响应中去除它，设置 _source为false，使用fields参数来检索你想要的字段
```
GET logs-my_app-default/_search
{
    "query": {
        "match_all": { }
    },
    "fields": [
        "@timestamp"
    ],
    "_source": false,
    "sort": [
        {
            "@timestamp": "desc"
        }
    ]
}
```




这个响应包含了每一个命中的字段作为一个一维数组。    
```
...
"hits": {
    ...
    "hits": [
        {
            "_index": ".ds-logs-my_app-default-2099-05-06-000001",
            "_type": "_doc",
            "_id": "PdjWongB9KPnaVm2IyaL",
            "_score": null,
            "fields": {
                "@timestamp": [
                     "2099-05-08T16:25:42.000Z"
                ]
             },
        "sort": [
            4081940742000
            ]
        },
         ...
        ]
    }
}
```



区域搜索    
为了在一个指定的时间、ip范围中搜索，使用range范围查找  
```
GET logs-my_app-default/_search
{
    "query": {
        "range": {
            "@timestamp": {
                "gte": "2099-05-05",
                "lt": "2099-05-08"
            }
        }
    },
    "fields": [
            "@timestamp"
        ],
        "_source": false,
        "sort": [
        {
        "@timestamp": "desc"
        }
    ]
}
```




你可以使用日期定义相对的时间范围，下面的查询搜索了后面一天的结果，这个将不会搜索到logs-my_app-default中的任何一条日志记录
```
GET logs-my_app-default/_search
{
    "query": {
        "range": {
        "@timestamp": {
            "gte": "now-1d/d",
            "lt": "now/d"
          }
        }
    },
    "fields": [
     "@timestamp"
    ],
    "_source": false,
    "sort": [
        {
        "@timestamp": "desc"
        }
    ]
}
```



从非结构话的内容中提取字段   
你可以在搜索时候从非结结构化的内容中提取runtime字段，例如Log中的消息。    
使用下面的搜索从event.original中提取运行中的字段source.ip，为了在响应中包含它添加sourec.ip到参数字段中 
```
GET logs-my_app-default/_search
{
"runtime_mappings": {
        "source.ip": {
        "type": "ip",
        "script": """
        String sourceip=grok('%{IPORHOST:sourceip} .*').extract(doc[ "event.original" ].value)?.sourceip;
        if (sourceip != null) emit(sourceip);
            """
        }
    },
    "query": {
        "range": {
            "@timestamp": {
                "gte": "2099-05-05",
                "lt": "2099-05-08"
            }
        }
    },
    "fields": [
        "@timestamp",
        "source.ip"
    ],
    "_source": false,
    "sort": [
    {
        "@timestamp": "desc"
    }
    ]
}
```



结合查询
你可以使用bool查询来结合多个查询，下面的搜索结合2个范围搜索，一个在@timestamp 另一个运行中字段source.ip
```
GET logs-my_app-default/_search
{
    "runtime_mappings": {
        "source.ip": {
        "type": "ip",
        "script": """
        String sourceip=grok('%{IPORHOST:sourceip} .*').extract(doc[ "event.original" ].value)?.sourceip;
        if (sourceip != null) emit(sourceip);
             """
        }
    },
    "query": {
        "bool": {
        "filter": [
            {
                "range": {
                    "@timestamp": {
                        "gte": "2099-05-05",
                        "lt": "2099-05-08"
                    }
                }
            },
                {
                    "range": {
                    "source.ip": {
                        "gte": "192.0.2.0",
                        "lte": "192.0.2.240"
                        }
                    }
                }
            ]
        }
    },
    "fields": [
        "@timestamp",
        "source.ip"
    ],
    "_source": false,
    "sort": [
        {
            "@timestamp": "desc"
        }
    ]
}
```


聚合数据    
使用聚合来概括概括数据指标、 统计、 或其他分析    
下面的搜索使用了一个聚合来计算average_response_size，使用http.response.body.bytes运行中字段，这个聚合只在匹配的查询上运行。    
```
GET logs-my_app-default/_search
{
    "runtime_mappings": {
        "http.response.body.bytes": {
        "type": "long",
        "script": """
        String bytes=grok('%{COMMONAPACHELOG}').extract(doc[ "event.original" ].value)?.bytes;
             if (bytes != null) emit(Integer.parseInt(bytes));
        """
        }
        },
        "aggs": {
            "average_response_size":{
                "avg": {
                     "field": "http.response.body.bytes"
                }
            }
        },
        "query": {
        "bool": {
        "filter": [
            {
            "range": {
                "@timestamp": {
                    "gte": "2099-05-05",
                    "lt": "2099-05-08"
                    }
                }
            }
            ]
        }
    },
    "fields": [
        "@timestamp",
        "http.response.body.bytes"
    ],
    "_source": false,
    "sort": [
        {
        "@timestamp": "desc"
        }
    ]
}
```


这个返回的聚合对象包含了聚合结果    
```
{
    ...
    "aggregations" : {
    "average_response_size" : {
    "value" : 12368.0
    }
    }
}

```


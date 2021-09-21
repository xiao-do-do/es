 # 索引CRUD

创建索引
```
PUT product 
``` 
创建数据
```
PUT /product/_doc/1
{
    "name":"xiaomi phone",
    "desc":"shouji zhong de zhandouji",
    "price":"1399",
    "tags":["xingjiabi","fashao","buka"]
}
```
查询索引
```
GET product
```

更新
```
POST /product/_upda
{
    "doc":{
    "price":2399
    }
}
```
删除索引
```
DELETE product
```

#测试数据
```
PUT /product/_doc/1
{
"name":"xiaomi phone",
"desc":"shouji zhong de zhandouji",
"price":"1399",
"tags":["xingjiabi","fashao","buka"]
}

#创建数据
PUT /product/_doc/2
{
"name":"xiaomi nfc phone",
"desc":"zhichi quangongneng nfc, shouji zhong de zhandouji",
"price":"4999",
"tags":["xingjiabi","fashao","buka"]

}

#创建数据
PUT /product/_doc/3
{
"name":"nfc phone",
"desc":"shouji zhong de zhandouji",
"price":"2999",
"tags":["xingjiabi","fashao","menjinka"]
}

#创建数据
PUT /product/_doc/4
{
"name":"xiaomi erji",
"desc":"erji zhong de huangmenji",
"price":"999",
"tags":["low","bufangshui","yinzhicha"]
}


#创建数据
PUT /product/_doc/5
{
"name":"hongmi erji",
"desc":"erji zhong de kendeji",
"price":"399",
"tags":["lowbee","xuhangduan","zhiliangcha"]
}
```
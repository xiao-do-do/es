#ELK Install
###Elasticsearch   
下载  
https://www.elastic.co/cn/downloads/elasticsearch   

启动  
执行elasticsearch.bat 

访问  
http://localhost:9200

###Kibana
下载  
https://www.elastic.co/cn/downloads/kibana

启动  
kibana运行需要先启动ES     
执行bin/kibana.bat    


访问  
http://localhost:5601


##ES Head插件
1.git clone https://github.com/mobz/elasticsearch-head.git    

2.编辑Gruntfile.js文件，添加hostname:'*',
```connect: {   
server: {   
    options: {  
        hostname:'*',   
        port: 9100, 
        base: '.',  
        keepalive: true 
        }   
}
```
3.启动    
```
npm i
npm run serve
```     

无节点信息 ES配置运行跨域访问    
config/elasticsearch.yml
```
http.cors.enabled: true
http.cors.allow-origin: "*"
```  

错误解决    
``` 
npm ERR! code ELIFECYCLE
npm ERR! errno 1
npm ERR! phantomjs-prebuilt@2.1.16 install: `node install.js`
npm ERR! Exit status 1
npm ERR!
npm ERR! Failed at the phantomjs-prebuilt@2.1.16 install script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.

npm ERR! A complete log of this run can be found in:
npm ERR!     C:\Users\do\AppData\Roaming\npm-cache\_logs\2021-09-20T14_51_03_893Z-debug.log

执行
npm install phantomjs-prebuilt@2.1.16 --ignore-scripts
```

访问  
http://localhost:9100/      


版本 7.12.0

#### 创建用户
创建Elasticsearch启动用户，并设置权限等
groupadd elsearch
useradd elsearch -g elsearch -p elsearch
chown -R elsearch:elsearch /opt/

#### 修改配置
vim /etc/security/limits.conf

hard    nofile           65536
soft    nofile           65536

vim /etc/sysctl.conf

vm.max_map_count=262144

#### 修改 es 配置
```
cluster.name: apm-application
node.name: node-1
path.data: /app/elasticsearch/elasticsearch-7.2.0/data
path.logs: /app/elasticsearch/elasticsearch-7.2.0/logs
#####  ES监听的ip地址

network.host: 0.0.0.0
cluster.initial_master_nodes: ["node-1"]

#####  需要开启跨域才能给elasticsearch-head，kibana等连接

http.cors.enabled: true
http.cors.allow-origin: "*"
http.cors.allow-headers: Authorization,X-Requested-With,Content-Length,Content-Type
```


#### 最后
访问ip:9200,出现以下信息即为启动成功
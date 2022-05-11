先到 rabbitMQ 安装目录

C:\Program Files\RabbitMQ Server\rabbitmq_server-3.7.7\sbin>

执行
 ```
.\rabbitmq-plugins.bat list 
 ```
查看是否开启 rabbit 管理插件  

rabbitmq_management

rabbitmq_management_agent
 
如果没有，则执行 
```
.\rabbitmq-plugins enable rabbitmq_management
```

开启 后台管理

localhost:15672

默认密码

账号：guset 

mima: guest


#### 开启远程链接

找到这个文件
```
C:\Program Files\RabbitMQ Server\rabbitmq_server-3.7.7\ebin\rabbit.app
```
找到 
```
{loopback_users, [<<"guest">>]}
```
 这一行 改为
```
{loopback_users, [<<>>]} 
```


然后重启
如果没有权限修改这个文件，就先复制出来，修改后，再进行覆盖


rabbitmq 从 3.3.0 开始禁止使用 guest/guest 权限访问 除localhost 外的访问


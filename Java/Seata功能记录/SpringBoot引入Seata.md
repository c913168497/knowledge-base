#### 整体结构
##### AT 模式
两阶段提交

一阶段：业务数据和回滚日志记录在同一个本地事务中提交，释放本地锁和连接资源

二阶段：
 - 提交异步化，非常快速的完成
 - 回滚通过一阶段的回滚日志进行反向补偿，UndoLog日志表

一阶段， Seata会拦截业务SQL
1. 解析SQL语义，找到业务SQL要更新的业务数据，在业务数据被更新前，将其保存为"before image"

2. 执行业务SQL 更新业务数据，在业务数据之后

3. 其保存成 "after image"，最后生成行锁


二阶段：

顺利提交，业务SQL在一阶段已经提交到数据库，所以Seata框架只需将一阶段快照数据和行锁删掉，完成数据清理即可

失败，

回滚的用"before image"还原业务数据；但在还原前首先要校验脏写，对比"数据库当前业务数据"和"after image"，如果两份数据完全一致就说明没有脏写，可以还原业务数据，如果不一致就说明有脏写，出现脏写就需要转人工处理
 

#### seata-server下载地址
https://github.com/seata/seata/releases


#### 代码，配置
```
update users set name = 'cozyxiong' where id = 1
```
1. 客户端通过连接器建立连接
2. 清空查询缓存
3. 解析器词法分析、语法分析
4. 预处理器判断表和字段是否存在
5. 优化器确定执行计划
6. 执行器执行执行计划
   1. 搜索记录
   2. 查询 Buffer Pool
   3. 判断记录是否一致
   4. 执行器传参给 InnoDB 层
   5. 开启事务
      1. 记录 undo log
         - 写入 Buffer Pool 的 Undo 页面
         - 该 Undo 页面被修改时，记录 redo log
      2. 更新记录
         - 更新 Buffer Pool 的数据页（同时标记为脏页）
         - 记录 redo log
         -  WAL 技术写入磁盘
       3. 记录 binlog
          - 保存至 binlog cache
6. 事务提交
   1. prepare 阶段：将 redo log 事务状态设为 prepare，然后将 redo log 刷新到硬盘
   2. commit 阶段：将 binlog 刷新到磁盘，接着调用引擎的提交事务接口，将 redo log 状态设为 commit，然后刷入到磁盘 redo log 文件


### 主从一致
主从一致即保证 redo log 和 binlog 保持一致。事务使用 MySQL 存储引擎实现的，InnoDB 存储引擎设计为 redo log 写入完成即事务提交完成，因此要保证 redo log 和 binlog 保持一致，就要将 redo log 的写入分为两个阶段，中间插入 binlog 写入完成，称为两阶段提交。
- prepare 阶段：将 XID（内部 XA 事务的 ID） 写入到 redo log，同时将 redo log 对应的事务状态设置为 prepare，然后将 redo log 持久化到磁盘
- commit 阶段：把 XID 写入到 binlog，然后将 binlog 持久化到磁盘，接着调用引擎的提交事务接口，将 redo log 状态设置为 commit，事务执行成功

当异常重启时，如果 binlog 中没有当前内部 XA 事务的 XID，说明 redolog 完成刷盘，但是 binlog 还没有刷盘，则回滚事务；如果 binlog 中有当前内部 XA 事务的 XID，说明 redolog 和 binlog 都已经完成了刷盘，则提交事务。

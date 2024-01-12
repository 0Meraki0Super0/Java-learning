### 介绍

MySQL表类型 由存储引擎决定，主要有MyISAM, InnoDB(事务安全型), Memory

- MyISAM：不支持事务，外键。但访问速度快
- InnoDB：提供可提交，回滚，崩溃恢复的事务安全。但写入效率较低，且占用更多磁盘保存数据，索引
- Memory：使用存在内存中的内容创建表，访问速度非常快，且默认使用HASH索引。但所有修改在退出MySQL服务后消失

![[Pasted image 20231201142041.png]]


### 应用
- 设置引擎：create  table  table_name(…………) engine myisam
- 修改引擎：alter  table  table_name  engine = memory
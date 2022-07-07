<!--
 * @Author: Null Zhao
 * @Date: 2022-04-07 09:19:17
 * @LastEditors: Null Zhao
 * @LastEditTime: 2022-06-17 14:32:34
 * @FilePath: \my-docs\docs\trouble-shooting\postgre-trouble-shooting.md
 * @Description: 
 * ctrl+alt+i ctrl+win+t win+y
 * Copyright (c) 2022 by null, All Rights Reserved. 
-->
## 覆盖IDENTITY列，系统自动生成的自增值。

**OVERRIDING SYSTEM VALUE**

```
postgres=# insert into test (id, info) values (1,'test');  
错误:  cannot insert into column \"id\"  
DETAIL:  Column "id" is an identity column defined as GENERATED ALWAYS.
HINT:  Use OVERRIDING SYSTEM VALUE to override.  
postgres=# insert into test (id, info) OVERRIDING SYSTEM VALUE values (1,'test');  
INSERT 0 1 
postgres=# select * from test;  
id | info   
----+------  
1 | test  
(1 row)  
```

## column "columnname" does not exist

> 表名字段必须小写

## 无法编辑

> 未设主键

## csv导入中文数据

> 可以用编码GB18030
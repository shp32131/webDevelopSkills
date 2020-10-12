## 数据库学习的三个阶段 
- 基础阶段
  + 基本操作 增 删 改 查  
- 优化阶段
  + 提高数据处理效率 
  + 索引，分表...
- 部署阶段
  + 搭建真实的环境系统 
  + 服务器集群 
  + 负载均衡...
> 什么是数据库？  
> 高效存储和处理数据的介质(硬盘,内存) 
- 关系型数据 SQL:数据存储载体为硬盘 
  + 大型 Orace,DB2 
  + 中型 SQL-SERVER,Mysql 
  + 小型 Acccess 
- 非关系型数据库 NoSQL:数据保存在内存中,掉电丢失 
  + Redis,Mongdb,memcached 
> 什么是关系型数据库?  
> 一种建立在关系模型上的数据库  
- 关系模型:一种建立在关系上的数学模型 
  + 数据结构 数据存储在一张二维表中 有行有列 
  + 操作指令集 所有的SQL语句 
  + 完整性约束 
    - 表内数据约束(字段与字段之间)
    - 表与表之间的约束(外键)  
> 行与记录的区别？
> 本质上是一个东西，column和field 都是指表中的一行或一条记录
> 行是从数据表的结构角度来说，记录是从数据的角度来说  

### SQL structured query language 结构化查询语句 
- 数据以查询为主，%99的操作都是在进行查询操作 
- SQL关系型数据库的操作指令是和种约束，但不强制
- 不同的数据库产品会有区别 
> SQL语言分为三个部分  
- DDL data definition language 数据定义语言 
  + 维护存储数据的结构 
  + 指令有 create drop alter 
- DML data manippulation language 数据操作语言 
  + 对数据进行操作(数据表中的内容)
  + 指令 insert  delete update select 
- DCL data control language 数据控制语言 
  + 权限控制 
  + 指令 grant revoke 
  + grant select,insert,delete,update on table1 * to user @localhost identified by "password"

### Mysql 服务器对象 
- 四层关系  
  + 系统 DBMS 
  + 数据库 DB 
  + 数据表 Table
  + 字段 field
> 基本操作  CRUD => create read update  delete   

### 数据库操作
- `create database demo [库选项]` 
- 库选项: 用来约束数据库，分为两个库选项 
  + 字符集设定 `charactor set/charset gbk/utf8` 数据库存储数据的编码格式 
  + 校对集设置 `collate utf8_general_ci` 
  + _bin 二进制,_ci 大小写不敏感,_cs 大小写敏感 
```sql
create database demo [库选项]
```

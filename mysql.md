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
  + 大型 Oracle,DB2 
  + 中型 SQL-SERVER,Mysql 
  + 小型 Access 
- 非关系型数据库 NoSQL:数据保存在内存中,掉电丢失 
  + Redis,MongDb,memcached 
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
- DML data manipulation language 数据操作语言 
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
  + 字符集设定 `character set/charset gbk/utf8` 数据库存储数据的编码格式 
  + 校对集设置 `collate utf8_general_ci` 
    - _bin 二进制,_ci 大小写不敏感,_cs 大小写敏感 
- 自定义名字可以加反引号以区分系统保留的关键字``
```sql
create database demo [库选项]
create database demo charset utf8 collate utf8_general_ci
```
- 查看数据库
```shell
 # 查看所有数据库 
show databases;
# pattern 模式  %表示所有字符 - 表示单个字符
show databases like "pattern"
# 查看数据库创建语句,数据库在执行sql前，会先对sql语句进行优化
show create database demo
```
- 更新数据库
- 数据库名字不可以更改，只能修改数据库的库选项
```shell
# 库选项与值之间可以用等号 = 也可以不用 charset=utf8  
alter database demo charset utf8 collate utf8_general_ci
```
- 删除数据库
```shell
# 单个删除，不能连续删除
drop database demo
```
### 表操作
- 创建一个表
```sql
create Table tb (
  id int(4) not null primary key auto_increment,
  name varchar(10) not null
)engine=myisam charset=utf8;
```
- 查看表 `show tables / show tables like "pattern"`
- 查看创建表的语句 `show create table tb`
- 查看结构 `desc tb / describe tb`
- 查看表结构 `show columns from tb`
- 修改表 包括修改表本身 和 修改表的字段 
  + 修改表本身
```shell
# 修改表名
rename table tb to student
# 修改表选项
alter table tb charset=gbk
```
  + 修改表字段
```shell
# 新增字段 alter table 表名 add [column] 字段名 [列属性] 位置
# 新增的字段可以存放在表中的任意位置 first / after 字段名
alter table tb add column id int(10) first 
# 修改字段 alter table 表名 modify 字段名 数据类型 [属性] 位置
# 重命名字段 change alter table 表名 change 旧字段名 新字段名 数据类型 [属性] 位置
# 删除字段 drop
# 如果表中已有数据，会把对应字段数据清空
alter table tb drop 字段名
# 删除数据表 可连续操作
drop table tb1 tb2 ...
```
- 数据操作 CRUD
- 新增数据 `insert into tb values(),(),()...` 
- 新增数据 `insert into tb [字段名] values(),(),()...` 
- 修改数据 `update table tb set 字段=值 where id=2` 
- 查看数据 `select * from tb`
  + `select * / 字段列表 from 表名 [where 条件]`
- 删除表数据 `delete from tb where id=3` 

### 字符集问题
- 改变变量的值 `set 变量名=值` 对话级别，临时改变，本次连接有效，关闭连接后，恢复原来的值 
- `set names gbk`统一设置客户端，连接层，查询结果的字符集 
  + `utf8`每个中文字符占三个字节
  + `gbk`每个中文字符占二个字节
```shell
# 查看所有系统字符集变量
show character set ;
show character like "character_set%";
# 改变字符集变量
# 客户端字符集设置
set character_set_client = gbk 
# 连接层字符集设置，是字符集转变的中间层，如果统一，效率更高
set character_set_connection = gbk
# 服务器给客户端查询查询结果的字符集
set character_set_result = gbk
# 以上三个变量可以统一设置
set names gbk 
```
### 校对集问题
- 校对集是数据比较的方式 `utf8_general_ci`
- 查看所有字符校对集`show collation`
- 修改一定要在表中没有数据时修改，有数据后修改无效 
- 三种格式：
  + _bin:binary 二进制比较，区分大小写 
  + _ci:case insensitive 不区分大小写 
  + _cs:case sensitive 大小写敏感
> 表中有数据后，修改字符校对集无效 
### 字符乱码问题
- 有字符乱码问题主要从以下三个方面解决 
- 1.前端浏览器 
  + 浏览器提交数据的字符集由表单控制
  + `<meta http-equiv='content-type' content='text/html;charset=utf-8'>`
- 2.后台服务器
  + PHP后台`header("content-type:text/html;charset=utf-8")`
  + 操作系统调用`iconv();`
- 3.数据库
  + 统一设置`set names utf8`

## MYSQL数据类型
- `mysql`中没有`boolean`类型，用`tinyint`的代替 
-列类型:对数据进行统一的分类，提升亲率，更好的利用有限空间
  + 数值类型 
  + 字符串类型
  + 时间日期类型
### 数值类型 
- 整型值 
  + tinyint  1个字节 常用来替代布尔类型 
  + smallint 2个字节
  + mediuint 3个字节
  + int      4个字节
  + bigint   8个字节
- 字段中 `int(10)`表示显示宽度为10，列属性为`zerofill`才有效，添加`zerofill`列属性时，会自动为`unsigned` 
- 小数值`float/decimal/double` 
- `float(m,d)` 4字节单精度浮点数，m为总位数，d为小数个数 
### 日期类型 
- `date` 4字节 YYY-MM-DD 日期值 
- `time` 3字节 HH:MM:SS 时间值或持续时间 
- `year` 1字节 YYY 年份值 
- `datetime` 8字节 YYY-MM-DD HH:MM:SS 混合日期与时间 
- `timestamp` 4字节 时间戳 自动更新特点 
```PHP
// php中data()函数，把一个时间秒数转换为格式的日期
date("YY-mm-dd H:i:s",time());
// time() 得到时间戳
date_default_timezone_set('PRC');//更改时区
// php时间函数 mktime(),strtotime(),micrototime()
```
### 字符串
- `char(n)` 固定长度，最多255个字符，n代表字符字数，不是字节个数 
  + 固定占用n个字符空间,点出n个字符会截断，`char`在存储的时候会截断尾部的空格 
- `varchar(n)` 可变长度，最多65535个字符 
  + 占用n+1个字节空间,超出n字符时会截断，`varchar`会使用1-3个字节来存储长度值
- `tinytext` 可变长度，最多255个字符 
- `text` 可变长度，最多65535个字符 
- `longtext` 可变长度，最多2^32-1个字符 
- `mysql`记录长度:65535个字节，`varchar`达不到理论长度 
- `null`占用一个字节，`text`文本不占用记录长度，但是`text`本身占用10字节
### 字段属性:空属性，主键,列描述，默认值，唯一键 
- 主键:
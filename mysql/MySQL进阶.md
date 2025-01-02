# MySQL进阶

刻意练习原则：发现不懂（短板之处） -> 补齐知识，练习 -> 检测知识、练习情况（可以尝试根据关键词来回忆内容，自己给自己出题目，解决题目）-> 改善自身

默认存储引擎是InnoDB

```mysql
show engines;#查看当前mysql支持的所有存储引擎
```

创建表 my_myisam，并指定MyISAM存储引擎

```mysql
create table my_myisam(
  id int,
  name varchar(12)
)engine=myisam;
```

# 存储引擎特点

- InnoDB

  - 介绍

    InnoDB是一种兼顾高可靠性和高性能的通用存储引擎，在MySQL5.5之后，InnoDB是默认的存储引擎

  - 特点

    DML操作遵循ACID模型，支持 **事务**；

    **行级锁**，提高并发访问性能；

    支持**外健**Foreign key约束，保证数据的完整性和正确性；

  - 文件

    xxx.ibd; xxx代表的是表名，innoDB引擎的每张表都会对应这样一个表空间文件，存储该表的表结构（frm，sdi）、数据和索引。

    参数：innoDB_file_per_table

- MyISAM

  - 介绍

    MyISAM是MySQL早起的默认存储引擎。

  - 特点

    不支持事务，不支持外键

    支持表锁，不支持行锁

    访问速度快

  - 文件

    xxx.sdi: 存储表结构信息
  
    xxx.MYD: 存储数据
  
    xxx.MYI: 存储索引
  
- Memory

  - 介绍

    Memory引擎的表数据时存储在内存中的，
  
  - 特点

  - 文件



面试题：InnoDB、MyISAM、Memory存储引擎有什么区别？

InnoDB支持**事务、行级锁、外键**，而MyISAM不支持这三点

| 特点         | InnoDB          | MyISAM | Memory |
| ------------ | --------------- | ------ | ------ |
| 存储限制     | 64TB            | 有     | 有     |
| 事务安全     | **支持**        | -      | -      |
| 锁机制       | **行锁**        | 表锁   | 表锁   |
| B+tree索引   | 支持            | 支持   | 支持   |
| Hash索引     | -               | -      | 支持   |
| 全文索引     | 支持（5.6之后） | 支持   | -      |
| 空间使用     | 高              | 低     | N/A    |
| 内存使用     | 高              | 低     | 中等   |
| 批量插入速度 | 低              | 高     | 高     |
| 支持外键     | **支持**        | -      | -      |



# 存储引擎的选择

InnoDB

MyISAM常用来做存储用户的日志、评论、足迹

memory存储引擎



# 索引概述

介绍：索引（index）是帮助MySQL**高效获取数据**的**数据结构（有序）**。

执行下面一条查询语句时：

```mysql
select * from user where age = 45;
```

无索引时会进行全表扫描的情况，有索引的话非常高效的查到

优缺点：

​	优势：提高查询效率，降低数据的IO成本；通过索引对数据进行排序，降低数据排序的成本，降低CPU的消耗

​	劣势：索引列是需要占用空间的；索引大大提高了查询效率，同时降低了更新表的速度，如对表进行增删改操作时，效率降低

# 索引结构

MySQL的索引是在存储引擎层实现的，不同的存储引擎有不同的结构，主要包含以下几种：

| 索引结构              | 描述                                                         |
| --------------------- | ------------------------------------------------------------ |
| B+Tree索引            | 最常见的索引类型，大部分引擎都支持B+树索引                   |
| Hash索引              | 底层数据结构是用哈希表实现的，只有精确匹配索引列的查询才有效，不支持范围查询 |
| R-Tree（空间索引）    | 空间索引是MyISAM引擎的一个特殊索引类型，主要用于地理空间数据类型，通常使用较少 |
| Full-Text（全文索引） | 是一种通过建立倒排索引，快速匹配文档的方式。类似于Lucene,Solr,ES |



| 索引       | InnoDB          | MyISAM | Memory |
| ---------- | --------------- | ------ | ------ |
| B+Tree索引 | 支持            | 支持   | 支持   |
| Hash索引   | 不支持          | 不支持 | 支持   |
| R-Tree索引 | 不支持          | 支持   | 不支持 |
| Full-Text  | 5.6版本之后支持 | 支持   | 不支持 |



重点：**B+树索引结构**

红黑树：大数据量的情况下，层级较深，查询效率低下

B树结构、B+树结构、Hash结构



思考：为什么InnoDB存储引擎选择使用B+树索引结构？

答：相对于二叉树，层级更少，效率更高；相对于Hash索引，B+树支持范围匹配及排序操作，Hash索引只支持等值匹配



# 索引分类

| 分类         | 含义                                                 | 特点                     | 关键字   |
| ------------ | ---------------------------------------------------- | ------------------------ | -------- |
| **主键索引** | 针对于表中主键创建的索引                             | 默认自动创建，只能有一个 | PRIMARY  |
| **唯一索引** | 避免同一个表中某数据列中的值重复                     | 可以有多个               | UNIQUE   |
| **常规索引** | 快速定位特定数据                                     | 可以有多个               |          |
| **全文索引** | 全文索引查找的是文本中的关键词，而不是比较索引中的值 | 可以有多个               | FULLTEXT |



在InnoDB存储引擎中，根据索引的存储形式，又可以分为以下两种：

| 分类                          | 含义                                                       | 特点                 |
| ----------------------------- | ---------------------------------------------------------- | -------------------- |
| **聚集索引(Clustered Index)** | 将数据存储与索引放到了一块，索引结构的叶子结点保存了行数据 | 必须有，而且只有一个 |
| **二级索引(Secondary Index)** | 将数据与索引分开存储，索引结构的叶子结点关联的是对应的主键 | 可以存在多个         |



聚集索引选取规则：

- 如果存在主键，主键索引就是聚集索引。

- 如果不存在主键，将使用第一个唯一（UNIQUE）索引作为聚集索引。

- 如果表没有主键，或没有合适的唯一索引，则 InnoDB 会自动生成一个 rowid 作为隐藏索引。



思考题：

1.根据id查询和根据name查询效率谁更快？

根据id查询的话，直接在聚集索引里面查询，即可查出那一行的数据，而根据name查询的话，先在二级索引里面查，查到id主键后，再根据id去聚集索引里面查找，此时这种就叫回表查询，效率会比较低。

2.InnoDB主键索引的B+树高度为多高？

假设：一行数据大小为1k，一页中可以存储16行这样数据。InnoDB的指针占用6个字节的空间，主键即使为bigint，占用字节数为8。



# 索引语法

- 创建索引

```mysql
CREATE [UNIQUE|FULLTEXT] INDEX index_name ON table_name(index_col_name,...);
```

如果一个索引只关联一个字段，那么称之为单列索引，如果一个索引关联多个字段，称之为联合索引或者组合索引。

- 查看索引

```mysql
SHOW INDEX FROM table_name;
```



- 删除索引

```mysql
DROP INDEX index_name ON table_name;
```

案例：按照下面的需求，完成索引的创建

执行如下SQL命令：

```mysql
# 创建数据库 itcast
create database itcast;
# 使用数据库 itcast
use itcast;
# 创建表 tb_user
create table tb_user(
	id int primary key auto_increment comment '主键',
	name varchar(50) not null comment '用户名',
	phone varchar(11) not null comment '手机号',
	email varchar(100) comment '邮箱',
	profession varchar(11) comment '专业',
	age tinyint unsigned comment '年龄',
	gender char(1) comment '性别 , 1: 男, 2: 女',
	status char(1) comment '状态',
	createtime datetime comment '创建时间'
) comment '系统用户表';

# 插入数据
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('吕布', '17799990000', 'lvbu666@163.com', '软件工程', 23, '1', '6', '2001-02-02 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('曹操', '17799990001', 'caocao666@qq.com', '通讯工程', 33, '1', '0', '2001-03-05 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('赵云', '17799990002', '17799990@139.com', '英语', 34, '1', '2', '2002-03-02 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('孙悟空', '17799990003', '17799990@sina.com', '工程造价', 54, '1', '0', '2001-07-02 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('花木兰', '17799990004', '19980729@sina.com', '软件工程', 23, '2', '1', '2001-04-22 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('大乔', '17799990005', 'daqiao666@sina.com', '舞蹈', 22, '2', '0', '2001-02-07 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('露娜', '17799990006', 'luna_love@sina.com', '应用数学', 24, '2', '0', '2001-02-08 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('程咬金', '17799990007', 'chengyaojin@163.com', '化工', 38, '1', '5', '2001-05-23 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('项羽', '17799990008', 'xiaoyu666@qq.com', '金属材料', 43, '1', '0', '2001-09-18 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('白起', '17799990009', 'baiqi666@sina.com', '机械工程及其自动化', 27, '1', '2', '2001-08-16 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('韩信', '17799990010', 'hanxin520@163.com', '无机非金属材料工程', 27, '1', '0', '2001-06-12 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('荆轲', '17799990011', 'jingke123@163.com', '会计', 29, '1', '0', '2001-05-11 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('兰陵王', '17799990012', 'lanlinwang666@126.com', '工程造价', 44, '1', '1', '2001-04-09 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('狂铁', '17799990013', 'kuangtie@sina.com', '应用数学', 43, '1', '2', '2001-04-10 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('貂蝉', '17799990014', '84958948374@qq.com', '软件工程', 40, '2', '3', '2001-02-12 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('妲己', '17799990015', '2783238293@qq.com', '软件工程', 31, '2', '0', '2001-01-30 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('芈月', '17799990016', 'xiaomin2001@sina.com', '工业经济', 35, '2', '0', '2000-05-03 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('嬴政', '17799990017', '8839434342@qq.com', '化工', 38, '1', '1', '2001-08-08 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('狄仁杰', '17799990018', 'jujiamlm8166@163.com', '国际贸易', 30, '1', '0', '2007-03-12 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('安琪拉', '17799990019', 'jdodm1h@126.com', '城市规划', 51, '2', '0', '2001-08-15 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('典韦', '17799990020', 'ycaunanjian@163.com', '城市规划', 52, '1', '2', '2000-04-12 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('廉颇', '17799990021', 'lianpo321@126.com', '土木工程', 19, '1', '3', '2002-07-18 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('后羿', '17799990022', 'altycj2000@139.com', '城市园林', 20, '1', '0', '2002-03-10 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('姜子牙', '17799990023', '37483844@qq.com', '工程造价', 29, '1', '4', '2003-05-26 00:00:00');
```



练习：

1. name字段为姓名字段，该字段的值可能会重复，为该字段创建索引。
2. phone手机号字段的值是非空，且唯一的，为该字段创建唯一索引。
3. 为profession、age、status创建联合索引。
4. 为email建立合适的索引来提升查询效率。

```mysql
# 查询tb_user所有信息
select *
from tb_user;

# 查询表中的所有索引
show index from tb_user;

# 给name字段添加常规索引
create index idx_user_name on tb_user (name);

# 给 phone 字段添加唯一索引
create unique index idx_user_phone on tb_user(phone);

# 给profession, age, status三个字段添加联合索引
create index idx_user_pro_age_sta on tb_user(profession, age, status);

# 给 email字段添加索引
create index idx_user_email on tb_user(email);

# 删除tb_user表中的email字段索引，根据当时建立这个索引时，索引的名称来删除
drop index idx_user_email on tb_user;
```



# SQL性能分析

## 慢查询日志

慢查询日志记录了所有执行时间超过指定参数（long_query_time, 单位：秒，默认10秒）的所有SQL语句的日志。

```mysql
# 慢查询日志状态查看（可查看是否开启慢查询日志）
show variables like 'slow_query_log';
```



MySQL的慢查询日志默认没有开启，需要在MySQL的配置文件（etc/my.cnf）中配置如下信息：

```mysql
 
```



## profile详情

查询当前mysql是否支持profile操作的sql命令：

```mysql
select @@have_profiling;
```

默认情况下profile开关是关闭的，查看prifle开关是否开启的命令：

```mysql
 select @@profiling;
```

开启profile的命令：

```mysql
set profiling = 1;
```



```mysql
# 查看 SQL语句执行性能情况
show profiles;

```

## explain执行计划

EXPLAIN执行计划的各字段含义：

- Id

  select查询的序列号，表示查询中执行select子句或者时操作表的顺序（id相同，执行顺序从上到下，id不同，值越大，越先执行）

- select_type

  表示select的类型，常见的取值有 SIMPLE（简单表，即不使用表连接或者子查询）、PRIMARY（主查询，即外层的查询）、UNION（UNION中的第二个或者后面的查询语句）、SUBQUERY（SELECT/WHERE之后包含了子查询）

- type

  表示连接类型，性能由好到差的连接类型为NULL、system、const、eq_ref、ref、range、index、all

  **无表查询类型为NULL，一般使用唯一索引或者主键查询时，类型就是const，使用其他常规索引查询的话，类型就是？**

  一般我们优化，主要就是将连接类型尽可能的优化到排在前面的连接类型，不要出现 all，因为出先 all 的时候就是全表扫描，性能极差。

- possible_key

  显示可能应用在这张表上的索引，一个或多个。

- key

  实际使用到的索引，如果为NULL，则没有使用索引。

- key_len

   表示索引中使用的字节数，该值为索引字段最大可能长度，并非实际使用长度，在不损失精度的前提下，长度越短越好。

- rows

  MySQL认为必须要执行查询的行数，在innoDB引擎的表中，是一个估计值，可能并不总是准确的。

- filtered

  表示返回结果的行数占需读取行数的百分比，filtered的值越大越好。
  
- Extra

重点要关注的是：**type、possible_keys、key、key_len**

还要关注Extra，额外内容

# 索引使用原则

索引使用的原则：

- 最左前缀法则（最左匹配原则）

​		如果索引了多列（联合索引），要遵守最左前缀法则。**最左前缀法则**指的是查询从索引的最左列开始，并且不跳过索引中间的列。如果跳跃某一列，**索引将部分失效（后面的字段索引失效）**。

- 范围查询

​		**联合索引中，出现范围查询（>,<），范围查询右边的列索引会失效**，如果业务允许的情况下尽量使用>=或<=的符号，>=来判断范围不会导致右边的列索引失效

- 索引计算

​		不要在索引列上进行运算操作，否则索引将失效

- 字符串不加引号

​		字符串不加引号，索引失效

- 模糊查询

​		如果仅仅是尾部模糊匹配，索引不会失效。如果是头部模糊匹配，索引将会失效。

```mysql
explain select * from tb_user where profession like '软件%';#索引有效
explain select * from tb_user where profession like '%软件';#索引失效，会进行全表扫描
explain select * from tb_user where profession like '软%件';#索引有效
```



- or连接的条件

​		用or分割开的条件，如果or前的条件中的列有索引，而后面的列中没有索引，那么涉及的索引都不会被引用到。

```mysql
explain select * from tb_user where id = 10 or age = 23;
explain select * from tb_user where phone = '17799990000' or age = 23;
```

​		由于age没有索引，索引即使id、phone有索引，索引也会失效。所以需要针对age也要建立索引。

```mysql
create index idx_user_age on tb_user(age);#给age添加索引
```

​		然后再执行带or分割的条件查询，此时索引就会生效了。

- 数据分布影响

​		如果MySQL评估使用索引比全表扫描更慢，则不会使用索引。

- SQL提示

​		SQL提示，是优化数据库的一个重要手段，简单来说，就是在SQL语句中加入一些人为的提示来达到优化操作的目的。

​		如use index、ignore index、force index

```mysql
explain select * from tb_user use index(idx_user_pro_age_sta) where profession = '软件工程';

explain select * from tb_user use index(idx_user_pro) where profession = '软件工程';

explain select * from tb_user ignore index(idx_user_pro) where profession = '软件工程';

explain select * from tb_user force index(idx_user_pro_age_sta) where profession = '软件工程';
```



- 覆盖索引

​		尽量使用**覆盖索引**（查询使用了索引，并且需要返回的列，在该索引中已经全部能够找到），减少使用select *。

​		**知识小贴士：Extra区域的using index condition表示查找使用了索引，但是需要回表查询数据，而using where;using index则表示查找使用了索引，但是需要的数据都在索引列中能找到，索引不需要回表查询数据，另外NULL表示需要回表查询**

​		

- 前缀索引

​		当字段类型为字符串（varchar, text等）时，有时候需要索引很长的字符串，这会让索引变得很大，查询时，浪费大量磁盘IO，影响查询效率。此时可以只将字符串的一部分前缀，建立索引，这样可以大大节约索引空间，从而提高效率。

语法：

```mysql
create index idx_xxx on table_name(column(n));
```

前缀长度：

​		可以根据索引的选择性来决定，而选择性是指不重复的索引值（基数）和数据表的记录总数的比值，索引选择性越高则查询效率越高，唯一索引的选择性是1，这是最好的索引选择性，性能也是最好的。



- 单列索引和联合索引

​		单列索引：即一个索引只包含单个列。

​		联合索引：即一个索引包含了多个列。

​		在业务场景中，如果存在多个查询条件，考虑针对查询字段建立索引时，建议建立联合索引，而非单列索引。



# 索引设计原则

```
1. 针对数据量较大，且查询比较频繁的表建立索引。
2. 针对于常作为查询条件（where）、排序（order by）、分组（group by）操作的字段建立索引。
3. 尽量选择区分度高的列作为索引，尽量建立唯一索引
4. 
5. 
6. 
7. 
```



# 索引总结

1.索引概述：

索引是高效获取数据的数据结构

2.索引结构：

B+Tree、Hash

3.索引分类：

**主键索引、唯一索引、常规索引、全文索引**

在InnoDB引擎中，根据索引的结构又分为：**聚集索引、二级索引**

默认主键是聚集索引，如果没有主键，那么唯一索引会作为聚集索引，如果两个都没有，那么MySQL会自动生成一个隐藏的rowid作为聚集索引

通常聚集索引中存储的是表的行数据，二级索引中存储的是主键id

4.索引语法



5.SQL性能分析工具

**执行频次、慢查询日志、profile、explain**



6.索引使用原则



7.索引设计原则



# SQL优化

## 插入数据

- insert优化

​	批量插入

注意修改文件路径

```mysql
load data local infile '/usr/local/mysql/support-files/load_user_100w_sort.sql' into table tb_user fields terminated by ',' lines terminated by '\n';
```

​	手动提交事务

​	主键顺序插入

## 主键优化

- 数据组织方式

​		在InnoDB存储引擎中，表数据都是根据主键顺序组织存放的，这种存储方式的表称为**索引组织表**（index organized table 简称IOT）

- 页分裂
- 页合并
- 主键设计原则

```
1.满足业务需求的情况下，尽量降低主键的长度
2.插入数据时，尽量选择顺序插入，选择使用AUTO_INCREMENT自增主键。
3.尽量不要使用UUID做主键或者是其他自然主键，如身份证号。
4.业务操作时，避免对主键的修改。
```



## Order By优化

Using filesort和Using index的区别

```
1.Using filesort : 通过表的索引或全表扫描，读取满足条件的数据行，然后在排序缓冲区sort buffer中完成排序操作，所有不是通过索引直接返回排序结果的排序都叫FileSort排序。
2.Using index ：通过有序索引顺序扫描直接返回有序数据，这种情况即为 using index，不需要额外的排序，操作效率高。
```

Order by优化方法

```
1.根据排序字段建立合适的索引，多字段排序时，也遵循最左前缀法则。
2.尽量使用覆盖索引。
3.多字段排序，一个升序一个降序，此时需要注意联合索引在创建时的规则（ASC/DESC）。
4.如果不可避免的出现filesort，大数据量排序时，可以适当增大排序缓冲区大小 sort_buffer_size（默认256k）。
```

## group By优化

也需要遵循**最左前缀法则**



## limit优化

一个常见又非常头疼的问题就是 limit 2000000,10，此时需要MySQL排序前 2000010 记录，仅仅返回2000000 - 2000010之间的记录，其他记录丢弃，查询排序的代价非常大。



优化思路：一般分页查询时，通过创建覆盖索引能够比较好地提高性能，可以通过覆盖索引加自查询的形式进行优化。



## count优化

- count的几种用法

```
1.count()是一个聚合函数，对于返回的结果集，一行行地判断，如果count函数的参数不是NULL，累计值就加1，否则不加，最后返回累计值。
2.用法：count(*)、count(主键)、count(字段)、count(1)
```



count(主键)

​	InnoDB引擎会遍历整张表，把每一行的主键id值都取出来，返回给服务层。服务层拿到主键后直接进行累加（主键不可能为NULL）

count(字段)

​	没有not null约束：InnoDB引擎会遍历整张表把每一行的字段值都取出来，返回给服务层，服务层判断是否为NULL，不为NULL，计数累加。

count(1)

​	InnoDB引擎遍历整张表，但不取值。服务层对于返回的每一行，放一个数字"1"进去，直接进行累加。

count(*)

​	InnoDB引擎不会把全部字段取出来，而是专门做了优化，不取值，服务层直接按行进行累加。

```
按照效率排序的话，count(字段)<count(主键id)<count(1)约等于count(*)，所以尽量使用count(*)
```



## update优化

注意事项：InnoDB的行锁是针对索引加的锁，不是针对记录加的锁，并且该索引不能失效，否则会从行锁升级为表锁。

当行锁升级为表锁时，并发性能会降低。



## SQL优化总结

1.插入数据

```
先通过SQL命令查看本地文件导入是否开启，如果没有开启，先开启，再执行后面的
insert:批量插入，手动控制事务，主键顺序插入
大批量插入：load data local infile
```



2.主键优化

```
主键长度尽量短、顺序插入	AUTO_INCREMENT（推荐）、UUID（不推荐）
```



3.order by优化

```
using index: 直接通过索引返回数据，性能高
using filesort: 需要将返回的结果在排序缓冲区排序
```



4.group by优化

```
索引，多字段分组满足最左前缀法则
```



5.limit优化

```
覆盖索引 + 子查询
```



6.count优化

```
性能：count(字段) < count(主键id) < count(1)约等于count(*)
```



7.update优化

```
尽量根据主键/索引字段进行数据的更新操作
```



# 视图

- 介绍：视图（view）是一种虚拟存在的表。视图中的数据并不在数据库中实际存在，行和列数据来自定义视图的查询中使用的表，并且是在使用视图时动态生成的。

​		通俗的讲，**视图只保存了查询的SQL逻辑，不保存查询结果**。所以我们在创建视图的时候，主要的工作就落在创建这条SQL查询语句上。



- 创建视图语法：

```mysql
create [or replace] view 视图名称[(列名列表)] as select语句 [with[cascaded|local]check option]
```

查询视图：

```mysql
show create view 视图名称; -- 查看视图创建语句
select * from 视图名称; -- 查询视图
```

修改视图：

```mysql
方式一：create [or replace] view 视图名称[(列名列表)] as select语句
[with[cascaded|local]check option]
方式二：alter view 视图名称 as select语句
```

删除视图：

```mysql
drop view if exists 视图名称;
```



例子：

```mysql
-- 创建视图
create or replace view stu_v_1 as select id,name from student where id <= 10;
-- 查询视图
show create view stu_v_1;
select * from stu_v_1;
select * from stu_v_1 where id <= 3;
-- 修改视图
create or replace view stu_v_1 as select id,name,no from student where id <= 10;
alter view stu_v_1 as select id,name from student where id <= 10;
-- 删除视图
drop view if exists stu_v_1;

```



- 视图检查选项

​	当使用 with check option子句创建视图时，MySQL会通过视图检查正在更改的每个行，例如 插入、更新、删除，以使其符合视图的定义。MySQL允许基于另一个视图创建视图，它还会检查依赖视图中的规则以保持一致性。为了确定检查的范围，MySQL提供了两个选项：CASCADED 和 LOCAL，默认值为 CASCADED。

CASCADED检查选项会积累下来，会去检查当前视图依赖的其他视图是否满足条件，举一个例子：创建视图stu_v_1时没有加上CASCADED检查选项，而基于stu_v_1创建的视图stu_v_2有加上CASCADED检查选项，那么在向视图stu_v_2插入数据时，不仅会检查插入的值是否满足stu_v_2的条件，还会检查是否满足stu_v_1的条件，都满足才插入成功，此时如果基于stu_v_2创建视图stu_v_3视图，stu_v_3视图没有加CASCADED检查选项，那么在向stu_v_3插入数据时，不会检查stu_v_3的条件是否满足，而是检查stu_v_2的是否满足，然后再检查stu_v_1的是否满足条件。

```mysql
create or replace view stu_v_1 as select id,name from student where id <= 20;

insert into stu_v_1 values(6, 'Tom');

insert into stu_v_1 values(30, 'Tom');
-- 基于 stu_v_1视图创建 stu_v_2视图
create or replace view stu_v_2 as select id,name from stu_v_1 where id > 10 with cascaded check option ;

-- 这条语句插入时，不满足stu_v_2条件，不能插入
insert into stu_v_2 values(7, 'Tom');

-- 这条语句插入时，不满足stu_v_1条件，不能插入
insert into stu_v_2 values(27, 'Tom');

insert into stu_v_2 values(15, 'Tom');

-- 插入的视图尽管是stu_v_2，但是stu_v_1中也有数据
select * from stu_v_1;
select * from stu_v_2;

-- 在视图 stu_v_2的基础上，创建视图 stu_v_3
create or replace view stu_v_3 as select id,name from stu_v_2 where id <= 15;

insert into stu_v_2 values(11, 'Tom');
insert into stu_v_2 values(17, 'Tom');
insert into stu_v_2 values(28, 'Tom');

```



Local检查选项与CASCADED类似

```mysql
create or replace view stu_v_4 as select id,name from student where id <= 20;

insert into stu_v_4 values(6, 'Tom');

-- 基于 stu_v_4视图创建 stu_v_5 视图
create or replace view stu_v_5 as select id,name from stu_v_4 where id >= 10 with local check option ;

-- 这条语句插入时，不满足 stu_v_5 条件，不能插入
insert into stu_v_5 values(7, 'Tom');

insert into stu_v_5 values(27, 'Tom');

-- 在视图 stu_v_5 的基础上，创建视图 stu_v_6
create or replace view stu_v_6 as select id,name from stu_v_5 where id <= 15;

insert into stu_v_6 values(14, 'Tom');
```



创建视图时使用了聚合函数，那么就会导致视图没法插入数据

```mysql
create view stu_v_count as select count(*) from student;

insert into stu_v_count values(10);
```

## 视图作用：

- 简单

视图不仅可以简化用户对数据的理解，也可以简化他们的操作。那些被经常使用的查询可以被定义为视图，从而用户不需要为以后的操作每次制定全部的条件。

- 安全

数据库可以授权，但不能授权到数据库特定行和指定的列上。通过视图用户只能查询和修改他们所见到的数据

- 数据独立

视图可以帮助用户屏蔽真实表结构变化带来的影响。



案例：

```
1.为了保证数据库表的安全性，开发人员在操作tb_user表时，只能看到的用户的基本字段，屏蔽手机号和邮箱两个字段。
2.查询每个学生所选修的课程（三张表联查），这个功能在很多业务中都有使用到，为了简化操作，定义一个视图
```

```mysql
create view tb_user_view as select id,name,profession,age,gender,status,createtime from tb_user;

select * from tb_user_view;

create view tb_stu_course_view as select s.id, s.name student_name, s.no, c.name course_name from student s, student_course sc, course c where s.id = sc.studentid and sc.courseid = c.id;

select * from tb_stu_course_view;
```





# 存储过程

- 介绍

**存储过程是事先经过编译并存储在数据库中的一段SQL语句的集合**。调用存储过程可以简化应用开发人员的很多工作，减少数据在数据库和应用服务器之间的传输，对于提高数据处理的效率是有好处的。

存储过程思想上很简单，就是数据库SQL语言层面的代码封装与重用。

- 特点

封装、复用

可以接收参数，也可以返回数据

减少网络交互，效率提升

- 创建存储过程

```mysql
create procedure 存储过程名称([参数列表])
begin
		SQL语句
end
```



- 调用存储过程

```mysql
call 存储过程名称;
```



- 查看存储过程

```mysql
select * from information_schema.routines where routine_schema = 'xxx'; -- 查询指定数据库的存储过程及信息，xxx指的是数据库名称
show create procedure 存储过程名称; -- 查询某个存储过程的定义
```



- 删除存储过程

```mysql
drop procedure [if exists] 存储过程名称;
```



案例：

```mysql
-- 存储过程语法
-- 创建
create procedure p1()
begin
    select count(*) from student;
end;

-- 调用存储过程
call p1();

-- 查看
select * from information_schema.ROUTINES where ROUTINE_SCHEMA = 'itcast';

show create procedure p1;
-- 删除
drop procedure if exists p1;
```



**注意：在命令行中，执行创建存储过程的SQL时，需要通过关键字delimiter指定SQL语句**

```mysql
delimiter $$; -- 这句的意思是以 $$ 为结束标记，替换原来的;结束
```

举例：

```mysql
delimiter $$;
select * from student; -- 这一行在命令行中按回车键，mysql不会认为可以执行了，需要改为下面的
select * from student$$
```



- 变量

**系统变量**是MySQL服务器提供，不是用户定义的，属于服务器层面。分为全局变量(**global**)、会话变量(**session**)。

查看系统变量：

```mysql
show [session|global] variables; -- 查看所有系统变量
show [session|global] variables like '.....'; -- 可以通过like模糊匹配方式查找变量
select @@[session|global] 系统变量名;	-- 查看指定变量的值
```

设置系统变量

```mysql
set [session|global] 系统变量名 = 值;
set @@[session|global]系统变量名 = 值;
```

案例：

```mysql

-- 变量：系统变量
-- 查询所有变量，默认当前会话的变量
show variables ;
-- 查看当前会话变量
show session variables ;
-- 查看全局变量
show global variables ;

-- 查看与事务相关的系统变量，通过模糊匹配
show session variables like 'auto%';
show global variables like 'auto%';


select @@session.autocommit;
select @@global.autocommit;

-- 设置系统变量
-- 关闭当前会话事务自动提交
set session autocommit = 0;
insert into course(id, name) values (5, 'Oracle'); -- 由于事务已经关闭了，所以需要手动提交事务，才会生效
commit ; -- 手动提交事务

-- 开启当前会话事务自动提交
set session autocommit = 1;
insert into course(id, name) values (6, 'ES');
```

**注意：**

**如果没有指定session/Global，默认是session，会话变量。mysql服务重启后，所设置的全局参数会失效，要想不失效，可以在/etc/my.cnf中配置**



用户自定义变量是用户根据需要自己定义的变量，用户变量不用提前声明，在用的时候直接用"@变量名"使用就可以。其作用域为当前连接。

- 赋值

```mysql
set @var_name = expr [,@var_name = expr]...;
set @var_name = expr [,@var_name := expr]...;

select @var_name := expr [,@var_name := expr]...;
select 字段名 into @var_name from 表名;
```



- 使用

```mysql
select @var_name;
```

案例

```mysql

-- 变量：用户变量
-- 赋值，可以使用 =
-- 也可以使用 :=
-- 推荐使用 :=
set @myname = 'itcast';
set @myage := 10;
set @mygender := '男', @myhobby := 'java';

select @mycolor := 'red';
select count(*) into @mycount from tb_user;

-- 使用
select @myname, @myage, @mygender, @myhobby;

select @mycolor, @mycount;
```

**注意：**

用户定义的变量无需对其进行声明或初始化，只不过获取到的值为NULL。



局部变量

局部变量是根据需要定义的在局部生效的变量，访问之前，需要declare声明。可用作存储过程内的局部变量和输入参数，局部变量的范围是在其内声明的 begin ... end块。

- 声明

```mysql
declare 变量名 变量类型[default ...];
```

变量类型就是数据库字段类型：INT、BIGINT、 CHAR 、VARCHAR、 DATE、 TIME等。

- 赋值

```mysql
set 变量 = 值;
set 变量名 = 值;
select 字段名 into 变量名 from 表明 ...;
```

案例

```mysql
-- 局部变量
-- 声明  declare
-- 赋值
create procedure p2()
begin
    -- 定义的局部变量在 begin ~ end之间生效
    declare stu_count int default 0;
#     set stu_count := 100;
    select count(*) into stu_count from student;
    select stu_count;
end;

call p2();
```



if条件

语法：

```mysql
if 条件1 then
		...
elseif 条件2 then		-- 可选
		...
else				    	-- 可选
		...
end if;		
```

练习

定义存储过程，完成如下需求：

1. score >= 85分，等级为优秀。
2. score >= 60 且 score < 85分，等级为及格。
3. score < 60，等级不及格。

```mysql
create procedure p3()
begin
    -- 声明一个局部变量 score
    declare score int;
    -- 声明一个局部变量 result
    declare result varchar(10);
    -- 给变量score赋值
    set score = 50;

    -- 判断 score的值
    if score >= 85 then
        set result = '优秀';
    elseif score >=60 then
        set result = '及格';
    else
        set result = '不及格';
    end if;

    select result;
end;

call p3();
-- 删除存储过程 p3
drop procedure p3;
```



存储过程的参数

| 类型  | 含义                                     | 备注 |
| ----- | ---------------------------------------- | ---- |
| IN    | 该类作为输入，也就是需要调用时传入值     | 默认 |
| OUT   | 该类作为输出，也就是该参数可以作为返回值 |      |
| INOUT | 既可以作为输入参数，也可以作为输出参数   |      |

练习2:

数据传入（in）参数score，判定当前分数对应的等级，并返回（out）

1. score >= 85分，等级为优秀。
2. score >= 60 且 score < 85分，等级为及格。
3. score < 60，等级不及格。

```mysql
create procedure p4(in score int, out result varchar(10))
begin
    -- 判断 score的值
    if score >= 85 then
        set result = '优秀';
    elseif score >=60 then
        set result = '及格';
    else
        set result = '不及格';
    end if;
end;

call p4(19, @result);

select @result;

-- 将200分制的分数换算成百分制，然后返回分数，inout
create procedure p5(inout score double)
begin
    set score := score * 0.5;
end;

set @score = 179;
call p5(@score);
select @score;


```



存储过程case

语法一：

```mysql
case case_value
			when when_value1 then statement_list1
			[when when_value2 then statement_list2]...
			[else statement_list]
end case;
```

语法二：

```mysql
case
		when search_condition1 then statement_list1
		[when when_value2 then statement_list2]...
		[else statement_list]
end case;
```

案例

根据传入的月份，判定月份所属的季节（要求使用case结构）

1、1-3月份，为第一季度

2、4-6月份，为第二季度

3、7-9月份，为第三季度

4、10-12月份，为第四季度

```mysql
create procedure p6(in month int)
begin
    declare result varchar(10);
    case
        when month >= 1 and month <= 3 then
            set result := '第一季度';
        when month >= 4 and month <= 6 then
            set result := '第二季度';
        when month >= 7 and month <= 9 then
            set result := '第三季度';
        when month >= 10 and month <= 12 then
            set result := '第四季度';
        else
            set result := '非法参数';
    end case;
    select concat('您输入的月份为：', month, '，所属的季度是：', result);
end;

call p6(5);

```

存储过程while语法结构

语法：

```mysql
-- 先判定条件，如果条件为true，则执行逻辑，否则不执行逻辑
while 条件 do
			SQL逻辑...
end while;
```

案例：

计算从1累加到n的值，n为传入的参数值。

```mysql
-- while 计算从1累加到n的值，n为传入的参数值。

-- A.定义局部变量，用于存储每次累加之后的值
-- B.每循环一次，就会对n进行减1，当n为0时，退出循环
create procedure p7(in n int)
begin
    declare total int default 0;
    while n > 0 do
        set total := total + n;
        set n := n - 1;
    end while;
    select total;
end;

call p7(100);

```

存储过程repeat语法

repeat是有条件的循环控制语句，当满足条件的时候退出循环。具体语法为：

```mysql
-- 先执行一次逻辑，然后判定逻辑是否满足，如果满足，则退出。如果不满足，则继续进行下一次循环
repeat
			SQL逻辑
			until 条件
end repeat;
```

案例：

计算从1累加到n的值，n为传入的参数值。

```mysql
create procedure p8(in n int)
begin
    declare total int default 0;
    repeat
        set total := total + n;
        set n := n - 1;
        until n = 0
    end repeat;
    select total;
end;

call p8(100);
```

存储过程loop

LOOP实现简单的循环，如果不在SQL逻辑中增加退出循环的条件，可以用来实现简单的死循环。LOOP可以配合以下两个语句使用：

1.LEAVE：配合循环使用，退出循环。

2.ITERATE：必须用在循环中，作用是跳过当前循环剩下的语句，直接进入下一次循环。

```mysql
[begin_label:]LOOP
		SQL逻辑....
end LOOP [end_label];

leave label; -- 退出指定标记的循环体
iterate label; -- 直接进入下一次循环
```

案例：

1.计算从1累加到n的值，n为传入的参数值。

```mysql
create procedure p9(in n int)
begin
    declare total int default 0;

    sum:loop
        if n <= 0 then
            leave sum;
        end if;
        set total := total + n;
        set n := n - 1;
    end loop sum;

    select total;
end;

call p9(10);
```



2.计算从1到n之间的偶数累加的值，n为传入的参数值。

```mysql
create procedure p10(in n int)
begin
    declare total int default 0;

    sum:loop
        if n <= 0 then
            leave sum;
        end if;

        if n % 2 = 1 then
            -- 说明 n是奇数
            -- 跳过本次循环，进入下一次循环
            set n := n - 1;
            iterate sum;
        end if;

        set total := total + n;
        set n := n - 1;
    end loop sum;

    select total;
end;

call p10(10);

```

存储过程之游标

**游标**（cursor）是用来存储查询结果集的数据类型，在存储过程和函数中可以使用游标对结果集进行循环的处理。游标的使用包括游标的声明、open、fetch和close，其语法分别如下：

```mysql
-- 声明游标
declare 游标名称 cursor for 查询语句;
-- 打开游标
open 游标名称;
-- 获取游标记录
fetch 游标名称 into 变量[变量];
-- 关闭游标
close 游标名称;
```

练习：

根据传入的参数uage，来查询用户表tb_user中，所有用户年龄小于等于uage的用户姓名（name）和专业（profession），并将用户的姓名和专业插入到所创建的一张新表（id，name，profession）中。

```mysql
create procedure p11(in uage int)
begin
    -- 先声明普通变量，再声明游标，否则创建存储过程会报错
    declare uname varchar(100);
    declare upro varchar(100);

    -- 声明游标，用于存储查询的结果集
    declare u_cursor cursor for select name,profession from tb_user where age <= uage;

    -- 创建新表，用于存储从游标中取出来的数据
    drop table if exists tb_user_pro;
    create table if not exists tb_user_pro(
        id int primary key auto_increment,
        name varchar(100),
        profession varchar(100)
    );
    -- 开启游标
    open u_cursor;
    -- 获取游标记录
    while true do
            fetch u_cursor into uname,upro;
            insert into tb_user_pro(name, profession) VALUES (uname, upro);
    end while;
    -- 关闭游标
    close u_cursor;
end;

call p11(40);
```

存储过程之条件处理程序

**条件处理程序（handler）**可以用来定义在流程控制结构执行过程中遇到问题时相应的处理步骤。具体语法为：

```mysql
declare handler_action handler for condition_value [,condition_value]... statement;

handler_action
		continue; -- 继续执行当前程序
		exit; -- 终止执行当前程序
condition_value
		SQLSTATE sqlstate_value: -- 状态码，如0200
		SQLWARNING: -- 所有以01开头的SQLSTATE代码的简写
		NOT FOUND: --所有以02开头的SQLSTATE代码的简写
		SQLEXCEPTION: --所有没有被SQLWARNING 或 NOT FOUND捕获的SQLSTATE代码的简写
```

使用条件处理程序改善上一个练习

```mysql
create procedure p11(in uage int)
begin
    -- 先声明普通变量，再声明游标，否则创建存储过程会报错
    declare uname varchar(100);
    declare upro varchar(100);

    -- 声明游标，用于存储查询的结果集
    declare u_cursor cursor for select name,profession from tb_user where age <= uage;
    -- 声明一个条件处理程序，当sql状态码为02000时退出程序
    declare exit handler for sqlstate '02000' close u_cursor;

    -- 创建新表，用于存储从游标中取出来的数据
    drop table if exists tb_user_pro;
    create table if not exists tb_user_pro(
        id int primary key auto_increment,
        name varchar(100),
        profession varchar(100)
    );
    -- 开启游标
    open u_cursor;
    -- 获取游标记录
    while true do
            fetch u_cursor into uname,upro;
            insert into tb_user_pro(name, profession) VALUES (uname, upro);
    end while;
    -- 关闭游标
    close u_cursor;
end;

call p11(40);

drop procedure if exists p11;

drop table if exists tb_user_pro;


create procedure p12(in uage int)
begin
    -- 先声明普通变量，再声明游标，否则创建存储过程会报错
    declare uname varchar(100);
    declare upro varchar(100);

    -- 声明游标，用于存储查询的结果集
    declare u_cursor cursor for select name,profession from tb_user where age <= uage;
    declare exit handler for not found close u_cursor;

    -- 创建新表，用于存储从游标中取出来的数据
    drop table if exists tb_user_pro;
    create table if not exists tb_user_pro(
        id int primary key auto_increment,
        name varchar(100),
        profession varchar(100)
    );
    -- 开启游标
    open u_cursor;
    -- 获取游标记录
    while true do
            fetch u_cursor into uname,upro;
            insert into tb_user_pro(name, profession) VALUES (uname, upro);
    end while;
    -- 关闭游标
    close u_cursor;
end;

call p12(30);

drop table if exists tb_user_pro;
```



# 存储函数

存储函数是有返回值的存储过程，存储函数的参数只能是IN类型。具体语法：

```mysql
create function 存储函数名称([参数列表])
returns type [characteristic...]
begin
		-- SQL语句
		return ...;
end;

charateristic说明：
deterministic:相同的输入参数总是产生相同的结果
no sql:不包含sql语句
reads sql data:包含读取数据的语句，但不包含写入数据的语句。
```

案例：

1.计算从1累加到n的结果

```mysql
create function func1(n int)
returns int deterministic
begin
    -- 从一累加到一百
    declare total int default 0;
    while n > 0 do
        set total := total + n;
        set n := n - 1;
    end while;
    return total;
end;

select func1(100);
```



# 触发器

- 介绍

触发器是与表有关的数据库对象，指在 insert / update / delete 之前或之后，触发并执行触发器中定义的SQL语句集合。触发器的这种特性可以协助应用在数据库端确保数据的完整性，日志记录，数据校验等操作。

使用别名OLD和NEW来引用触发器中发生变化的记录内容，这与其他的数据库是相似的。现在触发器还只支持行级出发，不支持语句级出发。

| 触发器类型     | new和old                                               |
| -------------- | ------------------------------------------------------ |
| insert型触发器 | new表示将要或者已经新增的数据                          |
| update型触发器 | old表示修改之前的数据，new表示将要或者已经修改后的数据 |
| delete型触发器 | old表示将要或者已经删除的数据                          |

- 语法

创建

```mysql
create trigger trigger_name
before/after insert/update/delete
on tbl_name for each row -- 行级触发器
begin
		trigger_stmt;
end;

-- 查看
show triggers;

-- 删除
drop trigger [schema_name.]trigger_name; -- 如果没有指定schema_name, 默认为当前数据库。
```

案例：

通过触发器记录tb_user表的数据变更日志，将变更日志插入到日志表user_logs中，包含增加，修改，删除；

```mysql
create table user_logs(
	id int(11) not null auto_increment,
  operation varchar(20) not null comment '操作类型,insert/update/delete',
  operation_time datetime not null comment '操作时间',
  operate_id int(11) not null comment '操作的ID',
  operate_params varchar(500) comment '操作参数',
  primary key(`id`)
)engine=innodb default charset=utf8;


-- 创建插入型触发器
create trigger tb_user_insert_trigger
    after insert
    on tb_user
    for each row
begin
    insert into user_logs(id, operation, operation_time, operate_id, operate_params)
    VALUES (null,'insert', now(), NEW.id, concat('插入的数据内容为：id=', NEW.id, ', name=', NEW.name, ', phone=', new.phone, ', email=', NEW.email,', profession=', NEW.profession));
end;
-- 查看
show triggers ;
-- 删除
drop trigger tb_user_insert_trigger;

-- 向 tb_user 表中插入数据
insert into tb_user(id, name, phone, email, profession, age, gender, status, createtime)
VALUES (25,'二皇子','18809091212','erhuangzi@163.com','软件工程',23,'1','1',now());

-- 修改数据的触发器的创建
create trigger tb_user_update_trigger
    after update
    on tb_user
    for each row
begin
    insert into user_logs(id, operation, operation_time, operate_id, operate_params)
    VALUES (null,'update', now(), NEW.id,
            concat('更新之前的数据内容为：id=', OLD.id, ', name=', OLD.name, ', phone=', OLD.phone, ', email=', OLD.email,', profession=', OLD.profession,
                   ' | 更新之后的数据内容为：id=', NEW.id, ', name=', NEW.name, ', phone=', new.phone, ', email=', NEW.email,', profession=', NEW.profession));
end;


update tb_user set profession = '会计' where id = 23;

update tb_user set profession = '会计' where id <= 5;

-- 删除数据的触发器的创建
create trigger tb_user_delete_trigger
    after delete
    on tb_user
    for each row
begin
    insert into user_logs(id, operation, operation_time, operate_id, operate_params)
    VALUES (null,'delete', now(), OLD.id,
            concat('删除之前的数据内容为：id=', OLD.id, ', name=', OLD.name, ', phone=', OLD.phone, ', email=', OLD.email,', profession=', OLD.profession));
end;


delete from tb_user where id = 25;
```

# 视图/存储过程/触发器 总结

1、视图（view）

虚拟存在的表，不保存查询结果，只保存查询的SQL逻辑

简单、安全、数据独立

2、存储过程（procedure）

事先定义并存储在数据库中的一段SQL语句的集合。

减少网络交互，提高性能，封装重用

变量 、if、case、参数（in/out/inout）、while、repeat、loop、cursor、handler

3、存储函数（function）

存储函数是有返回值的存储过程，参数类型只能为IN类型

存储函数可以被存储过程替代

4、触发器（trigger）

可以在表数据进行insert、update、delete之前或之后触发

保证数据完整性、日志记录、数据校验

# 锁

暂时没有
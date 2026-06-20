---
title: 'SQL简单语法'
draft: false
categories: ["专业课"]
cover: "images/covers/5.jpg"
date: 2026-05-19T09:00:00+08:00
lastmod: 2026-05-19T09:00:00+08:00
---



# SQL

## postgre SQL

#### 在docker中操作

docker ps   查看容器列表
docker exec -it p1 bash

如果用postgres用户连接数据库，则不用输入密码
即psql -U postgres  即可

```postgresql
psql -U user -d db_name -h 127.0.0.1 -p 5432
```

#### 数据库操作:

\l 查看数据库列表
\c 切换数据库
create database db1  建库 
drop database db1  删除数据库
createdb -U postgres mydb  在shell直接建库

### SQL

按照功能不同可以分为以下几类

![image-20260311201204430](/note_images/others/image-20260311201204430.png)

- DDL:用于定义数据库对象，例如数据库、表、列

- DML:对数据库中的记录进行增删改

- DQL:查询数据库中的记录

- DCL:定义数据库的访问权限和安全级别

- **常用关键字如上图所示**

  

注意，MySQL对大小写不敏感，即不区分大小写，但是变量一般大写以便于区分
左上角的《新建查询》后我们可以在窗口输出各种SQL语句（就像在Mysqlshell一样）

## MYSQL

###### mysqlsh进入mySQL-shell工具

此工具的命令都是以**\**开头的

bash中输入`mysqlsh`启动,也可以在VScode中找到mysqlsh
在输入`\connect root@localhost`连接

![image-20260311181701033](/note_images/others/image-20260311181701033.png)

出现MySQL与JS代表进入成功

出现图片所示灰色部分表示连接到本地的mysql服务器了

`use name`来使用一个数据库,

**JS**表示当前语言,M-S支持JS、py、和SQL三种语言,切换语言`\dialect`

# 语法:

首先默认数据类型是列，对象是行

### 1.数据库

***注意：可以使用navicat等图形化GUI工具，这样不用手动输入指令创建数据库***

```mysql
show database; //在当前目录下查询现有数据库
CREATE DATABASE database_name; //创建数据库
drop db_name  //删除数据库
```

### 2.导入导出

导入数据:

```mysql
mysql -u root -p game < game.sql
```

root为用户名，game为输入数据库名,game.sql为输入文件名字

输入后需要输入密码，没有错误则为导入名称

### ※3.创建表

###### 定义数据类型

先选择一个数据库:`use db_name;`

```mysql
//创建一个表 括号中时字段名称和数据类型，注意逗号的使用
create table tb_name (
    id INT,
    name VARVHAR(100),  //字符串 大小100个字符
    level INT,
    exp INT, 
    god DECIMAL(10,2)  //长度为10，保留两位小数的十进制数值
)

```

整数类型:tinyint  smallint  int  bigint 反别对应1~8个字节的储存空间

浮点数:float double  对应4、8个字节不同精度的浮点数

时间:date time datetime timestamp 对应：日期  时间  日期时间   时间戳

字符串:CHAR   VARCHAR   TEXT   BLOB

**描述表的结构**:`DESC tb_name`

### 4.Alter -> 修改数据

以及Delete、Update、Drop用法

```mysql
 -- 修改数据类型
Alter table tb_name MODIFY COLUMN name VARCHAR(200); 
//表示修改表(tb_name)中的"name"列，被操作的对象是 表和列 操作方式是修改数据类型

 -- 重命名
Alter table tb_name RENAME COLUMN name to nick_name;

-- 添加数据类型
ALTER TABLE tb_name ADD COLUMN last DATETIME  ;
-- 此时last是新添加的字段名，DATETIME为数据类型

 -- 删除某个字段(列)
ALTER TABLE tb_name DROP COLUMN last ;

-- 删除整个表格
DROP TABLE tb_name ;

-- 删除特定情况的数据(行)
DELETE FROM player where gold = 0; 

-- 想要修改某个数据具体值
UPDATE tb_name set level = 1 where name = "114"	

-- 想要某个数据类型在创建时写入默认值:
ALTER TABLE player MODIFY LEVEL INT DEFAULT 1 -- 默认LEVEL认值为1
-- 除DEFAULT还可以有FULL、NOT FULL、COMMENT 'infor'，UNIQUE不能写在MODIFY COLUMN

//若是把后面的where语句去掉，则会把整个表的数据修改,使用","连接可以修改多个值
//关键字table 、 xxx COLUMN  
```

### 5.查看和插入数据

#### INSERT插入数据

插入数据，不是增添列（添加数据类型），而是增加对象（增加行），基本语法：

```sql
-- 基本语法
INSERT INTO db_name(id,name,level,exp,gold)VALUES(1,"sc",1,1,1);
-- 多条插入
INSERT INTO db_name(id,name)VALUES(2,"114")(3,"514")
-- 此时没有默认值的数据类型是NULL，需要set默认值见上

```

- **省略**：如果VALUE后的数量与表中所有数据对得上（总的数量一样），则VALUES前的括号可以省略

- **多条插入**：也可以一次性插入多条数据(行)，V后面写入多个括号即可

#### select查找数据

*则表示查询所有的列，跟特定数据类型时只会展现特定的列，数据类型用","连接

```mysql
-- select选择要查询的列 *则表示查询所有的列 from后面根要查询表的名称
select * from tb_name;
```

除此之外，还可以使用NULL  和 NOT NULL来指定该字段是否为空值,或者使用UNIQUE来指定该字段必须是唯一的,除此之外还有主键约束和外键约束,一个表的外键必须是另一个表的主键

`MODIFY COLUMN`表示修改列,后面表示需要修改的列名和数据结构 

#### UPDATE修改数据

```sql
UPDATE player set level = 1 where name = "";  -- 修改特定值，name可换成其他主键
UPDATE player set level = 1,gold = 0; -- 去掉where 修改全部
```

#### DELETE删除数据

```sql
-- 删除整个表格
DROP TABLE tb_name ;

-- 删除特定情况的数据(行)
DELETE FROM player where gold = 0; 
```

### 6.导出导入数据

不同的sql数据库有着不同的语法，仅列出Mysql和pgsql

#### Mysql

```bash
# 导出整个数据库
mysqldump -u username -p database_name > backup.sql;
# 导入数据库
mysql -u username -p database_name < backup.sql;
# 仅导出表结构，不含数据（-d 参数）
mysqldump -u username -p -d database_name > structure.sql;
```

#### pgsql

```bash
# 导出数据
bcp database_name.dbo.your_table out data.csv -c -t, -S server_name -U username -P password

# 导出查询结果
bcp "SELECT * FROM your_table" queryout data.csv -c -t, -S server_name -U username -P password
```

### 7.WHere

使用**where**子句进行查询

用于查找满足指定标准的记录

有时候需要正则表达式

```mysql
//where
-- 查找经验在特定区域的player
SELECT * FROM player WHERE level > 1
SELECT * FROM player WHERE level > 1 AND level < 5

-- 优先级NOT > AND > OR，可以用括号改变优先级，可以用IN查找特定值的数据
SELECT * FROM player WHERE (LEVEL > 1 OR exp > 1)
SELECT * FROM player WHERE level IN (1,3,5) //查找为1,3,5的玩家
SELECT * FROM player WHERE NOT BETWEEN 1 AND 10 //等级不在1 ~ 10的玩家
```

#### 匹配正则表达式：

有时要求比较复杂，我们使用正则表达式

使用 `REGEXP` 表示使用正则表达式

```sql
-- 注意 % 和 _是like中才有的，正则表达式中没有

-- 则查找姓王且为两个字的玩家
SELECT * FROM player name REGEXP '^王.$' 
-- 查找名字带王的人
SELECT * FROM player name REGEXP '王' 

```

详情如下：
<img src="/note_images/others/image-20260312193520351.png" alt="image-20260312193520351" style="zoom:50%;" />

### 8.like模糊查询

**注意**：like 后要跟两种通配符之一： %表示任何字符，_表示一个字符

```sql
//LIKE模糊查询

-- 则模糊查询可以查找到名字是“王”前有任意个字，“王”后有两个字的玩家
SELECT * FROM player NAME LIKE '%王__' 
-- 查找姓“王”的玩家
SELECT * FROM player NAME LIKE '王%' 
-- 查找名字里有“王”的玩家
SELECT * FROM player NAME LIKE '%王%' 

```

#### tips:null值相关

**NULL与任何值都不相等，包括他本身**

而另外，空字符串" "本身与NULL是不一样的

```sql

-- 查找信息为空的值 注意不能用name = NULL NULL和任何值都不相等
SELECT * FROM plaer where email = NULL; -- 会得到一个空结果集
SELECT * FROM plaer where email is NULL; -- √
SELECT * FROM plaer where email <=> NULL //its all OK

-- 有时候是空字符串，其与NULL是不一样的，空字符串使用" = "判断
SELECT * FROM player where email = '' or email is NULL //its OK
```



### 9.ORDER BY

使用Order By对查找结果进行排序

```mysql
-- 默认升序排列
SELECT * FROM player ORDER BY level; 
-- DESC 降序排列
SELECT * FROM player ORDER BY level DESC; //降序

-- 在优先按照level升降排列的情况下按照exp升序排列 
SELECT * FROM player ORDER BY level DESC,exp ASC;  
-- 且可以使用列数进行排列,假设level为第五列 则有
SELECT * FROM player ORDER BY 5 DESC,exp ASC;  
```

效果如：

| level | exp  |
| ----- | ---- |
| 99    | 4    |
| 99    | 80   |
| 99    | 93   |

### 10.聚合函数

使用聚合函数对某列（某个数据类型）进行计算

如平均值、中位数、最大最小值等

###### 对查找结果进行计算

```mysql
-- 查找玩家数量
SELECT COUNT(*) FROM  player; 
-- 查找等级平均值
SELECT AVG(LEVEL) FROM  player 
```

以下为常用聚合函数：

<img src="/note_images/others/image-20260312195555627.png" alt="image-20260312195555627" style="zoom:67%;" />

### 11.分组

##### 使用GROUP BY

```mysql
-- 按性别分组
SELECT sex,count(*) from player group by sex; 
-- 此时SELECT返回两列:s、c，from表示从player表，sex表示按照列的值进行分组
-- 得到的结果是所有 sex 相同的行会被归入同一个组，然后对每个组执行 count(*) 聚合计算。
SELECT level,count(level) from player group by level;//看每个等级各有多少个玩家
```

##### having：筛选分组后的数据

```sql
-- 查询数量大于4的各个等级的数量（不是等级比4大的玩家）
SELECT level,count(level) from player group by level having count(level)> 4 

-- 降序排列
SELECT level,count(level) from player group by level having count(level) > 4 ORDER by count(level) DESC; 
```

###### SUBSTR:

截取某个数据类型的变量长度

SUBSTR(name,1,2) -> 从name的第一个字开始截取，长度为2

#####  LIMIT：返回前几名

```mysql
//代码如having处，省略
LIMIT 3  -- 
LIMIT 3,3 -- 返回第四到第六名 第一个三表示偏移量 第二个表示往后取三个
```

### 12.重复记录处理

包括**去重、合并**、交集、差集

- 在SELECT后使用**DISTINCT**去除重复记录

- 如果想要合并两条查询记录，使用**UNION**进行合并，并且UNION会默认去除重复的记录，如果不想重复的记录被删除则使用 **UNION ALL**
- 使用**INTERSECT**查询两个结果的交集，使用位置同UNION
- 使用**EXCEPT**查找差集 A EXCEPT B  -> **A - B**

```mysql
-- 查询所有玩家的性别
SELECT DISTINCT sex from player;

SELECT * FROM player WHERE level between 1 AND 3
UNION  -- UNION合并
SELECT * FROM player WHERE exp between 1 AND 3
```

### 13.子查询

子查询就是把一个查询的结果作为另一个查询的起始条件

使用**as**对查询起一个别名

```sql
SELECT AVG(level) from player;
select * from player where level > (SELECT AVG(level) from player) 
-- 使用 as 和 round
SELECT level,ROUND((SELECT AVG(level) from player)) as average;
```

**tips**:ROUND(  ) -> 对该数据四舍五入

还可以用子查询**创建新表**

```sql
-- 创建新表
create table new_player select * from player where level < 5;
-- 插入数据
insert into new_player select * from player where level BETWEEN 6 and 10;
```

使用select exists( )判断查询是否有结果  -> 返回0没有结果  返回1有结果

### 14.表关联

### 15.索引

### 16.视图












title: 'SQL'

date: 2026-05-04T11:00:00-07:00
lastmod: 2026-05-04T11:00:00-07:00

# SQL

# postgre SQL

##  cmd

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

![image-20260311201204430](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260311201204430.png)

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

![image-20260311181701033](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260311181701033.png)

出现MySQL与JS代表进入成功

出现图片所示灰色部分表示连接到本地的mysql服务器了

`use name`来使用一个数据库,

**JS**表示当前语言,M-S支持JS、py、和SQL三种语言,切换语言`\dialect`

### 语法:

##### 1.数据库

***注意：可以使用navicat等图形化GUI工具，这样不用手动输入指令创建数据库***

```mysql
show database; //在当前目录下查询现有数据库
CREATE DATABASE database_name; //创建数据库
drop db_name  //删除数据库
```

##### 2.导入导出

导入数据:

```mysql
mysql -u root -p game < game.sql
```

root为用户名，game为输入数据库名,game.sql为输入文件名字

输入后需要输入密码，没有错误则为导入名称

##### ※3.表

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

描述表的结构:`DESC tb_name`

###### 修改数据

```mysql
Alter table tb_name MODIFY COLUMN name VARCHAR(200);  //修改数据类型
Alter table tb_name RENAME COLUMN name to nick_name; //重命名
ALTER TABLE tb_name ADD COLUMN last DATETIME  ;//添加数据类型
ALTER TABLE tb_name DROP COLUMN last ; //删除某个数据类型(列)
DROP TABLE tb_name ;//删除整个表格
DELETE FROM player where gold = 0; //删除特定情况的数据(行)
UPDATE tb_name set level = 1 where name = "114"	//想要修改某个数据具体值
//若是把后面的where语句去掉，则会把整个表的数据修改,使用","连接可以修改多个值
```

###### 查看和插入数据

```mysql
INSERT INTO db_name(id,name,level,exp,gold)VALUES(1,"sc",1,1,1);
//如果VALUE后的数量与表中所有数据对得上（总的数量一样），则VALUES前的括号可以省略
//也可以一次性插入多条数据(行)，V后面写入多个括号即可
INSERT INTO db_name(id,name)VALUES(2,"114")(3,"514")

//想要某个数据在创建时写入默认值:
ALTER TABLE player MODIFY LEVEL INT DEFAULT 1//默LEVEL认值为1

select * from tb_name;
//selec选择要查询的列 *则表示查询所有的列 from后面根要查询表的名称

```

除此之外，还可以使用NULL  和 NOT NULL来指定该字段是否为空值,或者使用UNIQUE来指定该字段必须是唯一的,除此之外还有主键约束和外键约束,一个表的外键必须是另一个表的主键



`MODIFY COLUMN`表示修改列,后晚表示需要修改的列名和数据结构 

###### 查询:where子句和like

用于查找满足指定标准的记录

有时候需要正则表达式:

![image-20260312193520351](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260312193520351.png)

```mysql
//where
SELECT * FROM player WHERE level > 1
SELECT * FROM player WHERE level > 1 AND level < 5
SELECT * FROM player WHERE (LEVEL > 1 OR exp > 1)//优先级NOT > AND > OR
SELECT * FROM player WHERE level IN (1,3,5) //查找为1,3,5的玩家
SELECT * FROM player WHERE NOT BETWEEN 1 AND 10 //等级不在1 ~ 10的玩家

//LIKE模糊查询
SELECT * FROM player NAME LIKE '%王_' //%表示任何字符，_表示一个字符
//则模糊查询可以查找到名字是“王”前有任意个字，“王”后有一个字的玩家

//正则表达式
SELECT * FROM player name REGEXP '^王.$' //则查找姓王且为两个字的玩家
SELECT * FROM player name REGEXP '王' //查找名字带王的人
//注意 % 和 _是like中才有的，正则表达式中没有

//查找信息为空的值 注意不能用name = NULL 在MYSQL中NULL和任何值都不相等
SELECT * FROM plaer where email is NULL
SELECT * FROM plaer where email <=> NULL //its all OK
//有时候是空字符串，其与NULL是不一样的
SELECT * FROM player where email = '' or email is NULL //its OK
```

###### 对查找结果进行排序

使用ORDER BY

```mysql
SELECT * FROM player ORDER BY level; //默认为升序排列
SELECT * FROM player ORDER BY level DESC; //降序
SELECT * FROM player ORDER BY 5 DESC,exp ASC;  //假设level为第五列
//在同level（降）的情况下按照exp升序排列  且可以使用列数进行排列

```

###### 对查找结果进行计算

```mysql
SELECT COUNT(*) FROM  player; //查找玩家数量
SELECT AVG(LEVEL) FROM  player //查找等级平均值
```

以下为常用聚合函数：

![image-20260312195555627](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260312195555627.png)

###### 分组

使用GROUP BY

```mysql
SELECT sex,count(*) from player group by sex; //按性别分组
SELECT level,count(level) from player group by level;//看每个等级各有多少个玩家
```

having：筛选分组后的数据 LIMIT:返回前几名

```mysql
SELECT level,count(level) from player group by level having count(level) >  4 // 查询数量大于4的等级（不是等级比4大）

SELECT level,count(level) from player group by level having count(level) > 4 ORDER by count(level) DESC; //降序排列

LIMIT 3
LIMIT 3,3//返回第四到第六名 第一个三表示偏移量 第二个表示往后取三个
```

###### 重复记录

```mysql
SELECT DISTINCT sex from player;  //查询所有玩家的性别

SELECT * FROM player WHERE level between 1 AND 3
UNION
SELECT * FROM player WHERE exp between 1 AND 3
//用两条语句用UNION合并则查询重复记录
//不想重复记录被删除则使用 UNION ALL
//想要查询交集则用 INTERSECT
//使用EXCEPT查找差集 A EXCEPT B  -> A-B
```




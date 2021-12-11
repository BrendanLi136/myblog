---
title: MySQL
date: 2021-01-23 15:31:12
tags: SQL
categories: MySQL基础
---
# 什么是数据库
- 什么是数据库
    - 数据库是长期储存在计算机内,有组织的,可共享的数据集合
- 数据库的作用
    - 储存数据,数据的仓库,带有访问权限限制不同人可以有不同的操作

## 常见数据库
  ``` 
    mysql:开源免费的适用于中小型企业的免费数据库
    
    mariadb:直接是mysql开源版本的一个分支
    
    Oracle:甲骨文公司 适用于大型电商网站
    
    db2:IBM公司, 银行系统大多采用
    
    sqlserver:Windows里面,政府网站asp.net
  ```
>NOSQL非关系型数据:key:value
mongodb
redis

### 关系型数据库:

    主要是用于描述实体与实体之间关系
    实实在在的关系,老师-学生,员工-部门
    E-R关系图
        实体:方框
        属性:椭圆
        联系:菱形

### MYSQL的SQL语句
    SQL:Structure Query Language(结构化查询语言)
    DDL:数据定义语言:定义数据库,数据表它们的结构:create(创建) drop(删除) alter(修改)
    DML:数据操纵语言:主要是用来操作数据:insert(插入) update(修改) delete(删除)
    DCL:数据控制语言:定义访问权限,取消访问权限 grant
    DQL:数据查询语言:selec(查询) from子句 where子句

# 数据库的CRUD的操作
- 首先要登陆数据库服务器:mysql -uroot -proot

## 创建数据库

```
create database 数据库的名字;
create database day06;

create database 数据库的名字 character set 字符集;
create database day06_01 character set utf8;

create database 数据库的名字 character set 字符集 collate 校对规则
create database day06_2 character set utf8 collate utf8_bin
```

## 查看数据库

```
--查看数据库定义的语句
show create database 数据库名字;
show create database 

--查看所有所有数据库
show databases;
```

## 修改数据库的操作
```
--修改数据的字符集
alter database 数据库的名字 character set 字符集;
alter database day06_01 character set gbk;
```

## 删除数据库
```
drop database 数据库名字;
drop database day06_1;
```

## 其他数据库操作
```
--切换数据库(选中数据库)
use 数据库的名字;
use day06;

--查看一下当前正在使用的数据库
select database();

```

# 表的CRUD操作

## 创建表 
```
create table 表名(
    列名 列的类型(长度)  列的约束,
    列名2 列的类型(长度) 烈的约束
);

列的类型：
Java            sql
int             int
char/String     char/varchar
                char()：固定长度
                varchar()：可变长度
                长度代表字符个数
double          double 
float           float
boolean         boolean
date            date:YYYY-MM-DD
                time:hh:mm:ss
                datetime:YYYY-MM-DD hh:mm:ss 默认值为null
                timestamp:YYYY-MM-DD hh:mm:ss 默认值为当前时间
                
                text：主要是存放文本
                blob：存放二进制

列的约束：
    主键约束    primary key
    唯一约束    unique
    非空约束    not null
    
创建表：
create table student(
    sid int primary key,
    sname varchar(10),
    sex int,
    age int
);
```

## 查看表
```
--查看所有表
show tables;
--查看表的创建过程
show create table 表名;
--查看表结构
desc 表名;
```

## 修改表
```
>添加列(add)
alter table 表名 add 列名 列的类型 列的约束
alter table student add chengji int not null;
>修改列(modify)
alter table 表名 modify 列名 列的类型 列的约束
alter table student modify sex varchar(2);
>修改列名(change)
alter table 表名 change 旧列名 新列名 列的类型 列的约束
alter table student change changji sorce int;
>删除列(drop)
alter table 表名 drop 列名;
alter table student drop score;

>修改表名(rename)
rename table 旧表名 to 新表名
rename table student to teacher;
>修改表的字符集
alter table teacher charecter set gbk;
```

## 删除表
```
drop table 表名
drop table teacher;
```

# sql对表中数据的CRUD操作

## 插入数据
```
insert into 表名(列名1,列名2,列名3) values(值1,值2,值3);
insert into student(sid,sname,sex,age) values(1,'lisi',1,10);

--简单写法：如果插入是全列名的数据，表名后面的列名可以省略
intsert into student values(值1,值2,值3,值4);
insert into student values(2,'zhangsan',0,19);

--注：插入部分列，列名不可省略
insert into 表名(列名1,列名2) values(值1,值2);
insert into student(sid,sname) values(3,'wangwu');

--批量插入
insert into student values
(4,'zhangsan',1,23),
(5,'zhangsan',1,23),
(6,'zhangsan',1,23),
(7,'zhangsan',1,23);

--查看表中数据
select * from student;
```

>命令行下插入中文问题
- 临时解决方案：set names gbk;相当于是高速mysql服务器软件，我们当前在命令行下输入的内容是GBK编码.当命令窗口关闭后，它就在输入中文就会存在问题。
- 永久解决方法：修改my.in配置文件
    - 暂停mysql的服务
    - 在mysql安装路径中找到my.in配置文件
    - 将编码改成gbk
    - 保存文件退出
    - 启动mysql

## 删除记录
```
delete from 表名 [where 条件]
delete from student where sid=4;
delete from student;如果没有指定条件会将表中数据一条一条全部删除

==delete删除数据和truncate删除数据的qubie==

delete：DML 一条一条删除表中的数据
truncate：DDL 先删除表再重建表
关于哪条执行效率高：具体要看表中的数据量
    如果数据比较少，delete比较高效
    如果数据比较多，truncate比较高效
```

## 更新表记录
```
update 表名 set 列名=值，列名2=值 [where 条件]
将sid为5的名字改成李四
update student set sname='李四' where sid=5;

update student set sname='李四',sex=0 where sid=6;
```

## 查询记录
```
select [distinct] [*] [列名,列名2] from 表名 [where 条件]

--商品分类
1.分类的ID 
2.分类名称
3.分类描述

create table category(
    cid int primary key auto_increment,
    cname varchar(10),
    cdesc varchar(20),
);

insert into category values
(null,'手机','电子产品'),
(null,'电脑','科技产品'),
(null,'鞋靴箱包','日用品'),
(null,'瓜子花生','吃喝'),
(null,'汉堡鸡腿','KFC');

select * from category;
select cid,cname from category;

--所有商品
create table product(
    pid int primary key auto_increment,
    pname varchar(10),
    price double,
    pdata timestamp,
    cno int
);

insert into product values(null,'小米',1998,null,1);
insert into product values(null,'锤子',2958,null,1);
insert into product values(null,'阿迪达斯',222,null,2);
insert into product values(null,'粗粮王',25,null,3);
insert into product values(null,'劲酒',99,null,3);
insert into product values(null,'小熊饼干',7,null,4);
insert into product values(null,'旺旺小饼',11,null,5);
insert into product values(null,'哇哈哈',21,null,6);
insert into product values(null,'卫龙辣条',13,null,6);
insert into product values(null,'杯子',87,null,7);


--简单查询:
--查询所有的商品
    select * from product;
--查询商品名称和商品价格
    select prince,pname from product;
    
--别名查询  as的关键字, as 关键字可以省略
    --表别名 select p.name,p.price from product p;(主要用于多表查询)
    select p.name,p.price from product as p;
    
    --列别名 select pname as 商品名称,prince as 商品价格 from product;
    select pname as 商品名字,price as 商品价格 from product;
    省略as关键字
    select pname 商品名字,price 商品价格 from product;
    
--去掉重复的值
    --查询商品所有的价格;
    select price from product;
    select distinct price from product;
    
--select运算查询
    select *,price*1.5 from product;
    select *,price*0.8 as 折后价 from product; 
--条件查询 [where关键字]
    指定条件,确定要操作的记录
--where后的条件写法
    --关系运算符 : > >= < <= = != <>
        <> : 不等于 : 标准SQL语法
        != : 不等于 : 非标准语法
    --查询商品价格不等于222的其他商品
    select * from product where price <> 222;
    select * from product where price != 222;
    --查询商品价格在30到100之间
    select * from product where price >30 and price<100;
    select * from product where price between 30 and 100;
    
    --逻辑运算符:and or not
    --查询商品价格小于100 或者商品价格大于900
    select * from product where price<100 or price >900; 

--查询商品价格大于60的所有商品信息
    select * from product where price > 60;
    
--like : 模糊查询
    _ :代表一个字符
    % :代表多个字符
    --查询出名字中带有小的所有商品 '%小%'
    select * from product where pname like '%小%';
    --查询第二个字带子的所有商品
    select * from product where pname    like '_子';

    --in 在某个范围中获得值
        --查询出商品分类ID在1,4,5里面的所有商品
        select * from product where cno in (1,3,4);

--排序查询:order by 关键字
    asc : ascend 升序 (默认的排序方式)
    desc : descent 降序
    
    --0.查询所有商品,按照价格进行排序
    select * from product order by price;
    --1.查询所有商品,按照价格进行降序
     select * from product order by price desc;
    --2.查询名称有小的商品,按价格升序
    select * from product where pname like '%小%';
    select * from product where pname like '%小%' order by price asc;

--聚合函数
    sum() : 求和
    avg() : 求平均值
    count() : 统计数量
    max() : 最大值
    min() : 最小值
    --1.获得所有商品价格的总和
    select sum(price) from product;
    --2.获得所有商品的平均价格
    select avg(price) from product;
    --3.获得所有商品的个数
    select count(price) from product;
    
**where条件后面不能接聚合函数** 

--分组:group by 
    --1.根据cno字段分组,分组后统计商品的个数
    select cno,count(*) from product group by cno;
    --2.根据cno字段分组,分组后统计每组商品的平均价格并且商品平均价格>60;
    select cno,avg(price) from product group by cno having avg(price) > 60;
   
    --having 关键字 可以接聚合函数的 出现在分组之后
    --where 关键字 不可以接聚合函数  出现在分组之前
    
--编写顺序
 S..F..W..G..H..O
 select..from..where..group by..having..order by

--执行顺序
 F..W..G..H..S..O
from..where..group by..having..select..order by
```



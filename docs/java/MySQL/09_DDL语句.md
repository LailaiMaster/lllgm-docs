Data Definition Language

建表-改表-删表

建议用GUI，需要SQL的话直接导出

1. 主键
  1. 自增
2. 联合主键
  1. SC表
```
create table SC
(
SNO int,
CNO int,
SCGRADE int,
primary key (SNO, CNO)
)
```
1. 数据类型
  1. [https://dev.mysql.com/doc/refman/5.7/en/data-types.html](https://dev.mysql.com/doc/refman/5.7/en/data-types.html)
  2. [https://www.runoob.com/mysql/mysql-data-types.html](https://www.runoob.com/mysql/mysql-data-types.html)
1. 非空约束
2. 唯一约束
3. 默认值
4. 外键约束

#没有加主键外键约束的表

```
create table student
(
id tinyint,
name varchar(20) not null,
sex tinyint,
age smallint,
sdate date,
grade tinyint default 1,
`class` tinyint,
email varchar(50)
);
```
#自增主键

```
drop table student;
create table student
(
id tinyint primary key auto_increment,
name varchar(20) not null,
sex tinyint,
age smallint,
sdate date,
grade tinyint default 1,
class tinyint,
email varchar(50) unique
);
```
#自增主键在insert语句中不允许插入，由数据库自动插入
insert into student values 

(null, 'zhangsan', 1, 15, curdate(), 3, 40, 'zhangsan@mashibing.com');

#指定自增初始值

```
create table student
(
id int primary key auto_increment,
name varchar(20) not null,
sex tinyint,
age smallint,
sdate date,
grade tinyint default 1,
class tinyint,
email varchar(50)
) auto_increment = 100;
```
#创建班级表

create table `class`

(

id int primary key auto_increment,

cname varchar(20)

)

#外键约束

```
drop table student;
create table student
(
id int primary key auto_increment,
name varchar(20) not null,
sex tinyint,
age smallint,
sdate date,
grade tinyint default 1,
class int,
email varchar(50),
FOREIGN KEY fk_class(class) REFERENCES `class`(id)
) auto_increment = 100;
```
#测试外键约束

insert into student values 

(null, 'zhangsan', 1, 15, curdate(), 3, 40, 'zhangsan@mashibing.com');



删除表：

	drop table XXX;

	



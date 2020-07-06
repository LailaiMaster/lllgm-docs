DML = Data Manimulation Language

增删改查四大语句 CRUD = insert select update delete

1. select语句优先级最高
2. insert语句
```
insert into XX values ()
insert into XX(C1, C2) values (XX, XX) #其他字段必须有默认值
insert into student values
(null, 'zhangsan', 1, 15, curdate(), 40, 3, 'wangwu@mashibing.com');
insert into student(name, class, email)
values ('lisi', 3, 'lisi@mashibing.com')
```

```
update语句
update XX set XX=XX, XX=XX where XX

update student set sdate = '2009-09-09', age = 19 where name = 'zhangsan'
```

1. delete语句

delete from XX where XX

delete from student where name ='lisi'

delete from student



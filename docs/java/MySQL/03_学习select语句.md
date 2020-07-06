1. 取出表里所有内容
  1. select * from emp
2. 取出名字、月薪的内容
  1. select ename, sal from emp
3. 取出名字、年薪
  1. select ename , sal * 12 from emp
4. 年薪取别名
  1. select ename , sal * 12 annual_sal from emp
  2. select ename , sal * 12 `annual sal` from emp
  3. select ename , sal * 12 as `annual sal` from emp
5. 计算总收入（处理NULL值）
  1. NULL是空值，不是0
  2. select ename , sal * 12 + (case when comm is NULL then 0 else comm end) as annual_sal from emp
6. distinct去掉重复值
  1. select distinct deptno from emp
  2. select distinct deptno, job from emp
7. where条件过滤
  1. 取出deptno=10的记录 select * from emp where deptno = 10
  2. 取出不等于10的名字和薪水 select ename, sal from emp where deptno <> 10
  3. 取出薪水位于800到1500之间的记录  where sal between 800 and 1500
  4. 同上 where sal >= 800 and sal <= 1500
  5. 在emp表中查询comm值为空值的数据 where comm is null
  6. 不为空 where comm is not null
  7. 薪水是800或者1500 where sal in (800, 1500) ===> where sal = 800 or sal = 1500
  8. 名字是SMITH或者KING where name in ('SMITH', 'KING')
  9. 名字不是SMITH或者KING where name not in ('SMITH', 'KING')
  10. 简单日期处理 where hiredate > '1981-02-20'
  11. 模糊查询
    1. 名字包含ALL where ename like '%ALL%'
    2. 名字第二个字母是L like '_L%'
    3. 名字中包含% ---- '%\%%'
8. 排序
  1. select * from dept order by deptno
  2. select * from dept order by deptno desc
  3. select empno, ename from emp where deptno <> 10 order by empno asc; 当where混合使用时，要先对数据进行过滤，然后再进行升序或者降序的排序
  4. select ename, sal, deptno from emp order by deptno asc; 在emp表中查询ename，sal，和deptno的数据，并且按照deptno的升序进行显示
  5. select ename, sal, deptno from emp order by deptno asc, ename desc; 先按照deptno的升序进行一次排列，然后对ename进行倒序排序再进行显示
9. 常用函数 [https://dev.mysql.com/doc/refman/5.7/en/func-op-summary-ref.html](https://dev.mysql.com/doc/refman/5.7/en/func-op-summary-ref.html)
  1. select lower(ename) from emp; 一个lower的函数，接收了一个ename的实例，作用是将字段中的字符串数据全部转换为小写 大写为upper()
  2. select concat('www.', 'mashibing.', 'com') as url
  3. select char_length('aaaa')
  4. select substring('goodgoodstudy', 0, 5)
  5. select ltrim/rtrim/trim('  aaa bbb    ')
  6. select abs(), ceil(), floor()
  7. select round(3.14159, 3)
  8. select truncate(3.14159, 3)
  9. select curdate()/curtime()/now()
  10. select month(hiredate) from emp
  11. select monthname(d)/week(d)/year(d)/hour(time)/minute(time)
  12. select weekday(curdate()) / dayname(d)
10. 组函数
  1. select max(sal) from emp
  2. min(sal)
  3. avg(sal)
  4. sum(sal)
  5. count(*)
  6. count(comm)

  注意组函数查询的单个查询的问题

SELECT ename,MAX(sal) FROM emp  错误

SELECT ename FROM emp WHERE sal = (SELECT MAX(sal) FROM emp)


11. group by分组
  1. select avg(sal) from emp group by deptno; 按照deptno进行分组计算emp表中的sal平均值
  2. select deptno, job, max(sal) from emp group by deptno ,job;	按照deptno和job两个字段进行分组
  3. 在所有记录中，算出最高薪资的那个人 
    1. **select ename, max(sal) from emp;这条sql语句并不能求出所有人中薪资最高的那个人，**
    2. **分析：分组函数是多行输入，但是只有一行输出，这里的ename也会有很多人，这时无法进行匹配**
    3. select ename from emp where sal = (select max(sal) from emp);这里使用子查询，解决了无法匹配的问题
  4. 求每个部门最高薪资 select deptno, max(sal) from emp group by deptno; 这条SQL语句可以求出deptno部门的最高薪资
  5. 求每个部门薪水薪水最高人的名字
    1. select deptno, max(sal) max_sal from emp group by deptno

![图片](https://uploader.shimo.im/f/dx3FnZzNLP04hdKL.png!thumbnail)


![图片](https://uploader.shimo.im/f/1mc4z094lXYkdioY.png!thumbnail)

    * select ename, e.deptno, sal from emp e **join** (select deptno, max(sal) max_sal from emp group by deptno) p where e.deptno = p.deptno and e.sal = p.max_sal
* 使用having**对分组进行限制**
  * 按照部 门编号进行分组，求分组之后每一个部门的平均薪水
    * select avg(sal), deptno from emp group by deptno;
  * 这时，我们要求显示平均薪水大于2000的部门
    * select avg(sal), deptno from emp group by deptno having avg(sal) > 2000;
* 总结：

	  select xx from 表名;

		where xx xxxx;	对数据进行过滤

		group by;		分组

		having    ;		对分组进行限制

		order by;		排序

		取数据，过滤，分组，分组限制，排序

	

	要求薪水大于1200的雇员按照部门编号进行分组，分组后的平均薪水大于1500，查询分组

	内的平均工资，按照工资的倒序进行排列

	

	select avg(sal)

		from emp

		where sal > 1200

		group by deptno

		having avg(sal) > 1500

		order by avg(sal) desc;

	


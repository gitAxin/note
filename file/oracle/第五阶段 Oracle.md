# Oracle-day01 #


##SQL:结构化查询语言


* SQL是与数据库沟通的语言,用于操作数据库,SQL得益于标准,所有的数据库管理系统都支持该标准.
	
	* DDL:数据定义语言
	* DML:数据操作语言
	* DQL:数据查询语言
	* DCL:数据控制语言
	* TCL:事务控制语言	

##1.DDL:数据定义语言


* 数据定义语言,用于维护数据库对象

* 数据 库对象包含:表,视图,索引,序列

###1)CREATE TABLE 创建表格

		CREATE TABLE employee (
		id NUMBER(4),
		name VARCHAR2(20),
		gender CHAR(1),
		birth DATE,
		salary NUMBER(6,2),
		job VARCHAR2(30),
		deptno NUMBER (2)
		)
### 补充:复制表

	CREATE TABLE 新表名 as  SELECT * FROM  旧表名

###2) DESC 查看表的结构
		DESC employee

###3) DROP TABLE 删除表格

		DROP TABLE employee

###4) DEFAULT 设置默认值
* SQL语句本身不区分大小写的,但是字符串的值是区分大小写的.

* 字符串的字面量在数据库中是使用单引号括起来的

* 数据库中所有数据类型的默认值都是NULL,可以用DEFAULT关键字为字段单独指定默认值.当向一张表插入数据时,某字段不给值时会使用默认值作为该字段的值.


		CREATE TABLE employee(
		id NUMBER(4),
		name VARCHAR2(20),
		gender CHAR(1) DEFAULT 'M',
		birth DATE,
		salary NUMBER(6,2) DEFAULT 3000,
		job VARCHAR2(30),
		deptno NUMBER(2)
		)

###5) NOT NULL 值不允许为空

		CREATE TABLE employee(
		id NUMBER(4),
		name VARCHAR2(20) NOT NULL,
		gender CHAR(1) DEFAULT 'M',
		salary NUMBER(6,2) DEFAULT (3000),
		job VARCHAR2(30),
		deptno NUMBER(2)
		) 

###6)修改表
#### 6.1) RENAME 修改表名
* 格式:old\_name TO new\_name 
 
		RENAME employee TO myemp



#### 6.2) ALTER TABLE 修改表结构
#### 6.2.1) ADD()添加新字段

	ALTER TABLE myemp
	ADD(
	hiredate DATE
	)


#### 6.2.2) MODIFY()修改现有字段


* 可以修改字段的类型,长度,添加默认值或非空约束.

* 修改字段最好在表中没有数据的时候进行,否则尽量不要修改类型,长度只增不减,否则可能导致修改失败

		ALTER TABLE myemp
		MODIFY(
		JOB VARCHAR2(40)
		)
 



#### 6.2.3) DROP()删除现有字段

	ALTER TABLE myemp
	DROP(hiredate)
	

#### 补充: 更改字段名
	
	ALTER TABLE 表名 RENAME COLUMN 旧列名 TO 新列名

## 2.DML:数据操作语言


* DML是用来增删改表中数据的

### 1)INSERT INTO 语句: 插入数据


* 值和字段顺序必须一致

		INSERT INTO myemp_hjx
		(id,name,job,deptno)
		VALUES
		(1,'JACK','CLERK',10)


		//插入时如果不指定字段则是全列插入
		INSERT INTO myemp_hjx
		VALUES
		(1,'jerry','M',1203,'CLERK',10)




* TO_DATE('2008-08-08','YYYY-MM-DD')日期格式
	 
		INSERT INTO myemp_hjx
		(id,name,gender,salary,job,deptno,hiredate)
		VALUES
		(2,'张三','M',3000,'CLERK',11,TO_DATE('2008-08-08','YYYY-MM-DD'))


### 2)UPDATE 语句: 修改数据


* 修改时通常要添加WHERE, 这样只会将表中满足WHERE条件的记录进行修改,否则是全表数据修改

		UPDATE myemp_hjx
		SET salary=8000,deptno=12
		WHERE name = 'Tmo'

		UPDATE myemp_hjx
		SET name='jack'
		WHERE name='JACK'

### 3) DELETE 删除语句


* 不添加WHERE子句是清空表操作

* TRUNCATE 语句同样有删除表数据的作用

* TRUNCATE和DELETE的区别:
	* DELETE可以有条件的删除,TRUNCATE将表数据全部删除
	* DELETE是DML语句,可以回退,TRUNCATE是DDL语句,立即生效,无法回退


* 如果是删除全部表记录,且数据量较大,DELETE语句效率比TRUNCATE语句低
 
		//删除全部
		DELETE FROM myemp
		或者
		TRUNCATE TABLE myemp

		//有条件的删除
		DELETE FROM  myemp
		WHERE name = 'JACK'


### COMMIT 提交 ROLLBACK 回滚事务


# Oracle-day02
## 3.DQL数据查询语句
- SELECT语句中必须包含至少两个子句,分别是SELECT 子句与FROM子句,SELECT子句用来指定要查询的字段,FROM子句用来指定数据来源的表.

- SELECT中除了可以指定表中具体字段外,还可以指定一个函数或表达式

### 1) SELECT * FROM emp 查询所有字段
 
	--查询指定字段	
	SELECT empno,ename,job,sal
	FROM emp

	--指定一个表达式
	SELECT ename,sal,sal*12,sal*24,sal*36
	FROM emp

	--SELECT语句中添加WHRER子句可以仅查询出满足条件的记录.
	SELECT ename,sal,sal*12
	FROM emp_hjx
	WHERE sal>2000

	--查看10号部门员工
	SELECT ename,deptno
	FROM emp_hjx
	WHERE deptno=10


### 2)字符串函数
* SQL语句本身不区分大小写的,但是字符串的值是区分大小写的.

* 字符串的字面量在数据库中是使用单引号括起来的

#### 2.1) CONCAT(char1,char2) 连接字符串

	SELECT CONCAT(ename,CONCAT(':',sal))
	FROM emp_hjx

	---使用"||"可以方便的连接多个字符串
	SELECT ename || '的工资是:' || sal || deptno
	FROM emp_hjx

	SELECT ename || '的工资是:' || sal || '所在的部门:' || deptno
	FROM emp_hjx
	WHERE ename = 'SMITH'


#### 2.2)LENGTH(char) 获取指定字符串长度
	
	-查看每个员工名字的字符个数?
	SELECT ename,LENGTH(ename)
	FROM emp_hjx
	WHERE ename='SMITH'

#### 2.3)LOWER, UPPER, INITCAP 大小写转换
- LOWER 全小写
- UPPER 全大写
- INITCAP 首字母大写
- DUAL 伪表:当查询的数据与任何表没有关系时,可以查询伪表,这样只会查询出一条记录
 
		SELECT LOWER('HELLOWORLD'), 
		UPPER('helloworld'),
		INITCAP ('hello world')
		FROM dual

		SELECT ename, sal, deptno 
		FROM emp
		WHERE UPPER(ename) = UPPER('smith')

#### 2.4) TRIM, LTRIM, RTRIM 去除字符串指定内容
	
	--去除字符串两端的指定内容
	SELECT 
	TRIM('e' FROM 'eeeliteee')
	FROM dual

	--去除字符串左边指定内容
	SELECT LTRIM('eeeliteee','e')
	FROM dual

 	--去除字符串右边指定内容
	--只要包括其中之一就会被去除
	SELECT RTRIM('EEEdadLIDFKDKDIKDKDIKDIKDKDI', 'KDI')
	FROM dual


#### 2.5)LPAD(char1,n,char2),RPAD 补位函数
- 将char1显示n位,若char1不足n位时,左(右)补充若干个char2字符已达到位数
- 若超过指定位数,则截取字符串的右边
 
		SELECT ename, sal,RPAD(sal,10,'-')
		FROM emp_hjx
		WHERE ename = 'SMITH'


#### 2.6)SUBSTR (char,m[,n]) 截取char字符串 
- 截取char字符串,从m处开始,连续截取n个字符串.
- 需要注意,数据库中下标都是从1开始的!!
- n若不指定或指定的数字超过可以截取的实际字符长度时都是截取到当前字符串末尾,
- 若m为负数,则是从倒数位置开始截取.

		SELECT SUBSTR ('THINKING IN JAVA',10,2)
		FROM dual

#### 2.7)INSTR(char1, char2[,n[,m]]) 查看指定字符串在字符串中的位置
- 查看char2在char1中的位置
- n 和m不指定默认都是1
- n表示从第几个字符开始查找,m表示查看第几次出现

		SELECT  INSTR ('thinking in java','in',-10,1 )
		FROM dual


### 3)数值函数
#### 3.1)ROUND(n[,m]) 保留小数字指定位数
- 保留n小数点后m位
- m不写默认为0,即:保留到整数位
- m若为负数,则是保留10位以上的数字

		SELECT ROUND(45.678,2) FROM dual
		SELECT ROUND(45.678,0) FROM dual
		SELECT ROUND(555.678,-3) FROM dual


#### 3.2)TRUNC(n[,m]) 截取数字
- 参数意义与ROUND一致
 
		SELECT TRUNC(45.678,2) FROM dual
		SELECT TRUNC(45.678,0) FROM dual
		SELECT TRUNC(55.678,-1) FROM dual


#### 3.3)MOD(m,n) 取余
- m除以n求余数, n若为0  则直接返回m
 
		SELECT ename, sal, MOD(sal,1000)
		FROM emp



#### 3.4)CEIL,FLOOR 取整
- CEIL 向上取整
- FLOOR 向下取整

		SELECT CEIL (2560.222)
		FROM dual

### 4)日期类型相关函数:
- 日期相关关键字
	- 1:SYSDATE:返回一个表示当前系统时间的DATE值
	- 2:SYSTIMESTAMP:返回当前时间的时间戳类型值


			SELECT SYSDATE FROM dual
			SELECT SYSTIMESTAMP FROM dual

#### 4.1)1:TO_DATE() 字符串转DATE
- 将给定字符串按照给定的日期格式解析为一个DATE值

		SELECT TO_DATE('1998-05-02 21:15:22', 'YYYY-MM-DD HH24-MI-SS')
		FROM dual

		--日期格式字符串中不是字母或字符的其他字符都需要使用双引号括起来
		SELECT TO_DATE('1998年05月02日 21时15分22秒', 'YYYY"年"MM"月"DD"日" HH24"时"MI"分"SS"秒"')
		FROM dual

#### 4.2)TO_CHAR() DATE转字符串
	SELECT  TO_CHAR(SYSDATE,'YYYY-MM-DD HH24-MI-SS')
	FROM dual

	--RR与YY都是用两位数字表示年
	--但是当使用TO_DATE函数将两位数字解析为实际日期时,RR会自行判定世纪,而YY不会

	SELECT TO_CHAR(TO_DATE('49-12-05','RR-MM-DD'),'YYYY-MM-DD')
	FROM dual


![](http://i.imgur.com/emNDETw.png)

#### 4.3)日期可以进行计算

- 1:对一个日期加减一个数字,等同于计算加减天数

- 2:两个日期相减,差为相差的天数


		--日期之间比大小,越晚的越大
		SELECT ename, SYSDATE-hiredate
		FROM emp

		SELECT SYSDATE - TO_DATE('1990-05-02', 'YYYY-MM-DD')
		FROM dual

### 5)日期函数
#### 5.1:LAST_DAY(date) 返回给定日期所在月的月底日期
	
	--查看当月月底
	SELECT LAST_DAY(SYSDATE)
	FROM dual
	--查看每个月员入职当月的最后一天是哪一天
	SELECT ename,hiredate,LAST_DAY(hiredate)
	FROM emp_hjx

#### 5.2)ADD_MONTHS(date, n)	对指定日期加上指定月
- 若n为负数 则是减去

		--查看每个员工的转正日期?
		SELECT ename, ADD_MONTHS(hiredate,3)
		FROM emp_hjx

#### 5.3)MONTHS_BETWEEN(date1,date2)	计算两个日期相差几个月 
- 计算两个日期之间相差多少个月,计算方式使用date1-date2得到的
 
		--查看每个员工入职到今天为止共多少个月
		SELECT ename, MONTHS_BETWEEN(SYSDATE,hiredate)
		FROM emp_hjx

#### 5.4)NEXT_DAY(date,i) 返回给定日期第二天开始算一周之内指定周几对应的日期
	
	SELECT NEXT_DAY(SYSDATE,4)
	FROM dual

#### 5.5)LEAST, GREATEST 最大值也最小值
- 最小值与最大值,凡是可以比较大小的数据类型都可以使用这两个函数
- 对于日期当中,最大值即最晚的日期,最小值即最早的日期

		--最小值
		SELECT LEAST(SYSDATE,TO_DATE('2008-08-08','YYYY-MM-DD'))
		FROM dual
		--最大值
		SELECT GREATEST(SYSDATE,TO_DATE('2008-08-08','YYYY-MM-DD'))
		FROM dual

#### 5.6)EXTRACT 提取一个日期中指定时间分量的值

	SELECT EXTRACT(YEAR FROM SYSDATE)
	FROM dual

	SELECT ename,sal,hiredate
	FROM emp_hjx
	WHERE EXTRACT(YEAR FROM hiredate)=1980









### 6)空值函数

- 更新NULL值
 
		UPDATE student_hjx 
		set gender=NULL
		WHERE id=1000

- 作为条件判断NULL值, 判断要使用IS NULL 和 IS NOT NULL 不能使用"="判断NULL.
 
		DELETE FROM student_hjx
		WHERE gender IS NOT NULL

- NULL的运算 
	- NULL与字符串连接等于什么也没干
	- NULL与数字运算,结果还是NULL


			SELECT ename || NULL
			FROM emp_hjx

			SELECT ename,sal,comm,sal+comm
			FROM emp


#### 6.1)NVL(a1,a2) a1为NULL时,函数返回a2,否则返回al自身

- a1为NULL时,函数返回a2,否则返回al自身,所以函数作用是将NULL值替换为指定值

		SELECT ename,sal,comm,sal+NVL(comm,0)
		FROM emp_hjx


#### 6.2)NVL2(a1,a2,a3)

- 当a1为null时,函数返回a3

- 当a1不为NULL时,函数返回a2

- 当a1为null时,函数返回a3

		-- 查看每个员工的绩效,有绩效的则显示为"有绩效",为NULL的则显示为"没有绩效"
		SELECT ename,comm,NVL2(comm,'有绩效','没有绩效')
		FROM emp

		SELECT ename,comm,NVL2(comm,comm+sal,sal)
		FROM emp
# Oracle-day03 #
###1)字段的别名:


- 当一个SELECT子句中含有函数或者表达式时,查询出来的结果集中对应该字段的名字就是这个函数或表达式,不易理解,可读性差,为此我们可以为这样的字段单独指定别名
 
		SELECT ename, sal*12 yearsal
		FROM emp_hjx

- 若希望别名区分大小写或者含有空格,那么可以使用双引号将别名括起来
 
		SELECT ename,sal*12 "year sal"
		FROM emp_hjx


### 2)AND,OR 用来连接条件

	SELECT ename,sal,job
	FROM emp_hjx
	WHERE sal > 1000 OR job = 'CLERK'

	SELECT ename,sal,job
	FROM emp_hjx
	WHERE sal>1000 AND job = 'CLERK'
- OR的优先级低于AND,所以若希望提高OR的优先级可以使用括号



		-- 查看工资高于1000, 并且是职位CKERJ 或 SALESMAN
		SELECT ename, sal, job
		FROM emp_hjx
		WHERE sal>1000 AND (job = 'SALESMAN' OR job = 'CLERK')


### 2)LIKE 用于模糊匹配字符串
- 支持两个通配符:
	- _:任意一个字符
	- %:任意个字符(0-多个)
	 
			--查看名字第二个字母是A的员工?
			SELECT ename , sal,deptno
			FROM emp_hjx
			WHERE ename LIKE '_A%'


### 3)IN(list), NOT IN(list) 判断在列表中或不在列表中
- IN常用在子查询中
 
		SELECT ename,sal,job
		FROM emp_hjx
		WHERE job IN ('MANAGER','CLERK')

		SELECT ename,sal,deptno
		FROM emp_hjx
		WHERE deptno NOT IN (10,20)

		--查看10号部门以外,且工资高于1000,职位是CLERK或SALESMAN,名字中含有A的员工信息?
		SELECT ename,sal,job,deptno
		FROM emp_hjx
		WHERE deptno NOT IN(10) 
		AND sal > 1000 
		AND job IN ('CLERK','SALESMAN')
		AND ename LIKE '%A%' 

### 4)BETWEEN...AND... 判断在一个范围内


	-- 查看工资在1500到3000之间的员工信息?
	SELECT ename,sal,deptno
	FROM emp_hjx
	WHERE sal BETWEEN 1500 AND 3000


### 5)ANY(list), ALL(list)


- 配合>, >=, <, <= <> 一个列表使用


	- >ANY(list)大于列表其一(大于最小即可)
	- >ALL(list)大于列表所有(大于最大)
	- <ANY(list)小于列表其一(小于最大即可)
	- <ALL(list)小于列表所有(小于最小)
	- ANY,ALL常用在子查询中

			SELECT ename,empno,job,sal,deptno
			FROM emp_hjx
			WHERE sal>ANY(3500,4000,4500)


### 6)查询条件中使用表达式和函数

	--使用函数
	SELECT ename,sal,job,deptno,empno
	FROM emp_hjx
	WHERE ename = UPPER('scott');

	--使用表达式
	SELECT ename, empno, sal, job,deptno
	FROM emp_hjx
	WHERE sal*12>12000



### 7)DISTINCT 关键字 去重


- 用来将结果集中指定字段值重复的记录去除
- 多字段去重时,是这些字段值组合一样的记录不会重复出现

		--查看公司有哪些职位?
		SELECT DISTINCT job
		FROM emp_hjx

### 8)ORDER BY 排序结果集
- ORDER BY 子句可以对结果集进行排序或者降序排列
- 升序:ASC 不写默认也是升序
- 降序:DESC 
- ORDER BY 只能写在DQL的最后一个子句上

		SELECT ename,sal,deptno
		FROM emp_hjx
		ORDER BY sal DESC

- 多字段排序时,按照ORDER BY 后面第一个字段先排序,当第一个字段有重复值时,才按照第二个字段的值排序这些记录,依次类推,所以多字段排序是有优先级的
 
		SELECT ename,deptno,sal
		FROM emp_hjx
		ORDER BY deptno ASC,sal DESC

- 排序含有NULL值的字段时,NULL被认作为最大值
 
		SELECT ename,sal,comm
		FROM emp_hjx
		ORDER BY sal,comm DESC


		--查看公司的所有职位,并按照职位排序
		SELECT ename,job
		FROM emp_hjx
		ORDER BY job DESC


-----------------------
### 9) MAX,MIN,AVG,SUM,COUNT聚合函数


- 聚合函数是用来统计的.可以将结果集按照指定字段值进行统计然后得到一个结果.
- 其中有四个针对值本身统计的函数:
	- 	MAX:最大值
	- 	MIN:最小值
	- 	AVG:平均数
	- 	SUM:求和
- 还有一个对结果集记录数的统计函数:
	- 	COUNT 统计个数
	
			SELECT MAX(sal),MIN(sal),AVG(sal),SUM(sal)
			FROM emp_hjx



- 聚合函数忽略 NULL值
 
		SELECT AVG(comm),SUM(comm)
		FROM emp_hjx

		SELECT AVG(NVL(comm,0))
		FROM emp_hjx

		--括号内输入*号 或任意数字查询的都是总记录数
		SELECT COUNT(*)
		FROM emp_hjx

		--查看公司一共多少人?
		SELECT COUNT(ename)
		FROM emp_hjx


		--查看20号部门的平均工资
		SELECT AVG(sal)
		FROM emp_hjx
		WHERE deptno=20




### 10)GROUP BY 子句: 分组
- GROUP BY 子句可以将结果集按照指定的字段值一样的记录看作同一组,配合聚合函数使用, 可以对每组数据进行统计并得出结果.

- 当SELECT子句中使用了聚合函数时,那么凡不在聚合函数中的单独字段都必须出现在GROUP BY子句中


		--查看每个部门的平均工资?
		SELECT AVG(sal)
		FROM emp_hjx
		GROUP BY deptno

		--每个职位的最高工资 和 最低工资
		SELECT MAX(sal),MIN(sal),job
		FROM emp_hjx
		GROUP BY job

- GROUP BY 使用多个字段分组时,是将这些字段值的组合一样的记录看作一组

		--查看每个部门每种职位各多少人?
		SELECT COUNT(*) 人数,deptno 部门, job 职位
		FROM emp_hjx
		GROUP BY deptno,job


		--查看每个部门的平均工资,但前提是该部门的平均工资要高于2000?
		SELECT AVG(sal) , deptno 部门
		FROM emp_hjx
		WHERE AVG(sal)>2000  --此句会报错
		GROUP BY deptno
		--上面的SQL语句会报错,WHERE中不能使用聚合函数作为过滤条件,
		原因在于过滤的时机不对.WHERE是在第一次检索表时,逐行过滤数据以产生查询结果集时使
		用.而聚合函数是用来统计,那么前提是有数据,所以要将所有数据查询出来才能统计,之后
		才可以进行过滤,这实际是在WHERE之后进行的

### 11)HAVING 子句 过滤条件 同WHERE  


- 若想使用聚合函数的结果进行过滤,那么需要使用HAVING子句.而HAVING必须跟在GROUP BY子句之后,作用是添加过滤条件将不满足的分组去除.而HAVING中允许使用聚合函数作为过淲条件
 
		--SELECT AVG(sal) 平均工资,deptno
		FROM emp_hjx
		GROUP BY deptno
		HAVING AVG(sal)>2000

		--查看每个部门平均工资高于2000的这些部门的最高工资是多少
		SELECT MAX(sal) 最高工资, deptno 
		FROM emp_hjx
		GROUP BY deptno
		HAVING AVG(sal)>2000



### 12)关联查询


- 联合多张表查询数据即关联查询
- 查询的结果集中的字段来自多张表.
- 关联查询的重点是连接条件,数据库是根据连接条件对表中的数据做关联然后查询内容的


		--查看每个员工的名字以及其所在部门的名字?
		SELECT ename 姓名,dname 部门,
		FROM emp_hjx,dept_hjx
		WHERE emp_hjx.deptno=dept_hjx.deptno



- 对于查询的某个字段在多张表上同时存在而再来查询时出现的未明确定义列的问题,我们可以使用表名或表别名来指定该字段来自哪张表
 
		SELECT e.ename 姓名,d.dname 部门, e.deptno
		FROM emp_hjx e,dept_hjx d
		WHERE e.deptno=d.deptno




- 关联查询中过滤条件要与连接条件同时满足.

		--查看在NEW YORK工作的员工?
		SELECT e.ename,e.deptno,d.dname,d.loc
		FROM emp_hjx e, dept_hjx d
		WHERE e.deptno=d.deptno AND d.loc = 'NEW YORK'


- 关联查询通常要加连接条件,不写连接条件会出现"笛卡尔积",笛卡尔积通常是一个无意义的结果集,笛卡尔积是将关联查询表中的数据一一连接一遍而产生的结果集,数据量为关联查询表数据量的乘积

		SELECT e.ename,e.deptno,d.dname,d.loc
		FROM emp_hjx e, dept_hjx d




### 13)内连接  JOIN


- 内连接也是用来实现关联查询

		SELECT e.ename,d.dname
		FROM emp_hjx e JOIN dept_hjx d
		ON e.deptno=d.deptno

		SELECT e.ename,e.deptno,d.dname,d.loc
		FROM emp_hjx e JOIN dept_hjx d
		ON e.deptno= d.deptno
		WHER d.loc = 'NEW YORK'
    
- 关联查询忽略不满足连接条件的记录.



### 14)外连接
#### 14.1)LEFT OUTER JOIN 左外连接 ####
#### 14.2)RIGHT OUTER JOIN 右外连接 ####
#### 14.3)FULL OUTER JOIN 全外连接 ####

- 外链接在关联查询中除了可以将满足连接条件的记录查询出来之外,还可以将不满足连接条件的记录也查询出来
- 外链接分为:
	- 左外连接: LEFT OUTER JOIN :以JOIN左侧表作为驱动表(所有数据都要显示出来),那么当该表某条记录不满足连接条件时来自右侧表中的字段值全部为空
	- 右外连接: RIGHT OUTER JOIN 
	- 全外连接: FULL OUTER JOIN 

			--右外连接:
			SELECT e.ename,e.deptno,d.dname,d.loc
			FROM emp_hjx e RIGHT OUTER JOIN dept_hjx d
			ON e.deptno = d.deptno 

			--关联查询的外链接写法(+号在哪边 NULL就在哪边):
			--不支持全外链接
			SELECT e.ename,e.deptno,d.dname,d.loc
			FROM emp_hjx e, dept_hjx d
			WHERE e.deptno(+)=d.deptno

### 15)自连接
- 当前表的记录与当前表的多条记录有对应关系 
- 自连接解决数据属性相同,但是又存在上下级关系的树状结构数据使用


		--查看每个员工的名字以及其上司的名字?
		--关联查询写法:
		SELECT e.ename,m.ename
		FROM emp_hjx e, emp_hjx m
		WHERE e.mgr=m.empno

		--内连接查询的自连接写法:
		SELECT e.ename,m.ename
		FROM emp_hjx e JOIN emp_hjx m
		ON e.mgr=m.empno


# Orcale-day04 #

### 1)子查询   !!!重点


- 子查询是嵌套在其他SQL语句当中的,目的是为嵌套的SQL提供数据,以便其执行
 
		--查看谁的工资高于CLARK?
		SELECT ename,sal
		FROM emp_hjx
		WHERE sal>(
					SELECT sal
					FROM emp_hjx
					WHERE ename = 'CLARK')

		--查看谁和FORD一个部门的
		SELECT deptno, ename
		FROM emp_hjx
		WHERE deptno=(
    				SELECT deptno
    				FROM emp_hjx
    				WHERE ename = 'FORD')

		--查看工资高于平均工资的员工?
		SELECT ename,sal
		FROM emp_hjx
		WHERE sal>(SELECT AVG(sal)
          		  FROM emp_hjx)

		-- 查看最高工资是谁?
		SELECT ename,sal
		FROM emp_hjx
		WHERE sal=(SELECT MAX(sal)
            			FROM emp_hjx
            			)
 
		-- 查看部门的平均工资,前提是该部门的平均工资要高于30号部门的平均工资
		SELECT AVG(sal),deptno
		FROM emp_hjx
		GROUP BY deptno
		HAVING AVG(sal)>(SELECT AVG(sal)
        		        FROM emp_hjx
                		WHERE deptno=30)
 
 


#### 1.1)在DDL与DML中也可以使用子查询


- 1:在DDL中使用子查询,可以将一个子查询的结果集当做表快速创建
 
		-- 创建一张表,含有 empno,ename,sal, JOB,deptno,dname,loc
		 并且数据就是现有表emp,dept 中的数据?
		CREATE TABLE employee_jx
		AS
		SELECT e.empno id, e.ename name, e.sal, e.JOB, d.deptno, d.dname, d.loc
		FROM emp_hjx e, dept_hjx d
		WHERE e.deptno = d.deptno(+)




- 2:DML中使用子查询
	 
		--将SMITH所在部门所有员工工资上浮10%
		UPDATE emp_hjx
		SET sal=sal*1.1
		WHERE deptno =(SELECT deptno
           			   FROM emp_hjx
               		   WHERE ename = 'SMITH')
 
 
#### 1.2)多行单列子查询配合IN,ANY,ALL使用

- 子查询根据查询结果不同,分为:
	- 1,单行单列子查询
	- 2,多行单列子查询
	- 3,多行多列子查询
	 
	其中单列子查询常用在过滤条件中,而多列子查询通常当做表看待

- 对于多行单列子查询在作为过滤条件进行判断时,要配合IN,ANY,ALL使用
 
 
		--查看与SALESMAN同部门的其他职位员工?
		SELECT ename,job,deptno
		FROM emp_hjx
		WHERE deptno IN (SELECT deptno
        		         FROM emp_hjx
        		         WHERE JOB = 'SALESMAN')  
		AND job <> 'SALESMAN'
			
		--查看比SALESMAN和CLERK职位工资都高的员工?
		SELECT ename,sal
		FROM emp_hjx
		WHERE sal> ALL(SELECT sal
        		      FROM emp_hjx
        		      WHERE JOB IN('SALESMAN','CLERK'))
              

#### 1.3)EXISTS 当该子查询可以查询出至少一条记录时,即判定为满足条件

- EXISTS 关键字用在过滤条件中,其后需要跟一个子查询,当该子查询可以查询出至少一条记录时,即判定为满足条件


		--查看有员工的部门?
		SELECT d.deptno,d.dname
		FROM dept_hjx d
		WHERE EXISTS(SELECT *          --不关心查出来的具体数据,只关心能不能查出数据
        		    FROM emp_hjx e
              		WHERE e.deptno=d.deptno)
              
		--查看有下属的员工?
		SELECT m.ename
		FROM emp_hjx m
		WHERE EXISTS (SELECT *         --查询没有下属的员工   NOT EXISTS 
        		      FROM emp_hjx e
              		 WHERE m.empno=e.mgr)




#### 1.4)FROM 子句中使用子查询

- 当一个子查询为多列子查询时,通常是当做一张表使用出现在FROM子句中
 
		--查看谁的工资高于自己所在部门平均工资?
		SELECT e.ename,e.sal,e.deptno
		FROM emp_hjx e, (SELECT AVG(sal) avg_sal,deptno
        		        FROM  emp_hjx
               			GROUP BY deptno) t
		WHERE e.deptno = t.deptno
		AND e.sal>t.avg_sal

		--查看每个部门最高工资的员工?
		SELECT e.ename,e.sal,e.deptno
		FROM emp_hjx e, (SELECT MAX(sal) max_sal,deptno 
                  		FROM emp_hjx
                  		GROUP BY deptno) t
		WHERE e.deptno = t.deptno
		AND e.sal=t.max_sal
                  
         
#### 1.5)查询字段中使用子查询 ####
                 
		SELECT e.ename,e.sal,
							(SELECT d.dname
							FROM dept_hjx d
							WHERE d.deptno= e.deptno) dname
		FROM emp_hjx e

### 2)分页查询(分段查询)  !!!重点
- 当一个查询语句查询的数量非常大时,需要分批分段将数据查询出来,这样做可以减少系统资源开销,提高数据库响应速度.分页由于在标准SQL中没有定义,所以不同的数据库有不同的语法,即:方言,如果使用其他数据库管理系统的话需要再去查看

#### 2.1)ROWNUM 伪列
- Oracle提供了伪列:ROWNUM来配合完成分页查询
- ROWNUM不存在于任何表中,但是所有的表都可以查询该字段,该字段的值在结果集中就是每条记录的行号.
- ROWNUM 的值是动态生成的,是伴随查询的过程中生成的.即:每当可以从表中查询出一条记录时,
ROWNUM会为该条记录生成行号,从1开始逐1递增
- 所以,ROWNUM做>1以上数字的判断,查询不出任何结果
	
		SELECT ROWNUM, ename, sal, deptno
		FROM emp_hjx
		WHERE ROWNUM >1      --可以小于一个1以上的数字或者大于0
		--以上判断查询不出任何结果
	
		--查询6号到10号的人
		SELECT *
		FROM (SELECT ROWNUM rn ,ename,deptno
      		  FROM emp_hjx)
		WHERE rn BETWEEN 6 AND 10

		--工资排名第6到第10的几个人
		SELECT *
		FROM(SELECT ROWNUM rn, t.*			--2,再对查出来的数据进行编号
	    	  FROM(SELECT ename,sal			--1,先把数据查出来
           			FROM emp_hjx
           			ORDER BY sal DESC) t)
		WHERE rn BETWEEN 6 AND 10
 
 
		--工资排名第6到10的几个人(2)		--不能做>1的判断 但是可以小于10的判断,
		所以在编号的时候可以同时取出小于10的,在最后再取大于6的,可以提高效率
		SELECT *
		FROM (SELECT ROWNUM rn, t.*
      		  FROM (SELECT ename,sal
		           FROM emp_hjx
        	       ORDER BY sal DESC) t
      			   WHERE ROWNUM <=10)  
		WHERE rn >=6
 
- 分页在Oracle中需要三步骤:
	- 1:确定查询数据,并且排序(第一次子查询)
	- 2:对数据编号(第二次子查询)
	- 3:根据行号取范围(最外层查询)
- 在MySql数据库中分页查询有一个函数:limit()
#### 2.2) 分页显示公式
-  pageSize:每页显示的条目数
-  page:页数

		例:pageSize:5 page:2
		START: (page-1)*pageSize+1
	    end:   pageSize*page
 
 
### 3)DECODE 分支 
- 基本语法:
DEOCDE (expr,search1,result1[,search2,result2...][,default])
- DECODE 用于比较参数expr的值,如果匹配到哪一个search条件,就返回对应的result结果
- 可以有多组search和result的对应关系,如果任何一个search条件都没有匹配到,则返回最后的default
- 如果没有提供default参数,则返回null

- 写法1:
 
		--职位是 'MANAGER', 'ANALYST', 'SALESMAN'的工资分别上调
	 	SELECT ename,JOB,sal,
		DECODE(JOB,              
	          'MANAGER', sal*1.2,
              'ANALYST', sal*1.1,
              'SALESMAN',sal*1.05,
               sal) bouns
		FROM emp_hjx

- 写法2:

		--此种写法必须设置默认default参数
		SELECT ename,JOB,sal,
		CASE JOB WHEN 'MANAGER' THEN sal*1.2
          		 WHEN 'ANALYST' THEN sal*1.1
           		 WHEN 'SALESMAN' THEN sal*1.05
          		 ELSE sal END 
				 bouns
		FROM emp_hjx

#### 3.1)GROUP BY 中使用DECODE
 		
		-- 查看VIP的共有多少人,其他的共有多少人?
		-- 把想当成一组的,返回成同样的值,然后对返回每组的个数进行计数
		SELECT COUNT(1),DECODE(job,'MANAGER','VIP','ANALYST','VIP','OTHER')
		FROM emp_hjx
		GROUP BY DECODE(job,'MANAGER','VIP','ANALYST','VIP','OTHER')  --以DECODE分组
#### 3.2)ORDER BY 中使用DECODE                          
		--对字段 dname 排序 (返回的值类型不一定非要与比较的值类型相同)
		SELECT deptno,dname,loc
		FROM dept_hjx
		ORDER BY DECODE(dname,'OPERATIONS',1,'ACCOUNTING',2,'SALES',3);
 

### 4) ROW\_NUMBER, RANK,DENSE_RANK 排序函数
 

- 排序函数可以对结果集按照指定的字段分组, 然后在组内按照指定的字段排序,然后生成组内的行号.
 


#### 4.1)ROW_NUMBER 生成组内连续且唯一的数字
 
		--查看每个部门的工资排名: 
	 		SELECT ename,sal,deptno,
				ROW_NUMBER () OVER(       
					PARTITION BY deptno    --以部门为单位进行分组
					ORDER BY  sal DESC	   --在部门内进行降序排序
					) rank                 --rank:别名
		FROM emp_hjx
  
#### 4.2)RANK 生成组内不连续不唯一的数字

		--查看每个部门的工资排名
		SELECT deptno,ename,sal,
		RANK() OVER(
				PARTITION BY deptno        --以部门为单位进行分组
				ORDER BY sal DESC		   --在部门内进行降序排序
				) rank 						--rank:别名
		FROM emp_hjx

#### 4.3) DENSE_RANK 生成组内连续不唯一的数字

		--查看每个部门的工资排名
		SELECT deptno,ename,sal,
		DENSE_RANK() OVER(
				PARTITION BY deptno 
				ORDER BY sal DESC
				) rank
		FROM emp_hjx

------------------------

--------------------------
### 5)UNION, UNION ALL, INTERSECT, MINUS集合操作
- 如果想使用,两个SELECT查的字段名,顺序必须一样才可以使用

#### 5.1)UNION-------并集 自动去掉合并后重复的记录(行)  对结果排序
- 如Set
- 相当于两个SELECT的过滤条件满足一一个就行, 相当于 OR 关系
		
#### 5.2) UNION ALL------并集 显示合并后的所有行,包括重复的行 对结果不排序
- 如List
- 和UNION的区别 就是,UNION不显示重复的,页UNION ALL会显示重复的行

#### 5.3)INTERSECT-------交集 同时满足,我有你也有
	
- 相当于同时满足两个SELECE 的过滤条件,相当于 AND 关系

#### 5.4)MINUS ---------差集 我有你没有 

		SELECT ename,sal,JOB
		FROM emp_hjx
		WHERE job = 'MANAGER'

		[ UNION | UNION ALL |INTERSECT |MINUS ]
 
		SELECT ename,sal,JOB
		FROM emp_hjx
		WHERE sal > 2500



### 6)高级分组函数

- ROLLUP, CUBE 和 GROUPING SETS 运算符是GROUP BY 子句的扩展,可以生成与使用UNION ALL来组合单个分组查询时相同的结果集,用来简化和高效的实现统计查询


#### 6.1)ROLLUP() 函数  参数递减组合


- GROUP BY ROLLUP(a,b,c) ,参数逐个递减分组,最后进行全表的统计
- 参数个数+1 个分组

		GROUP BY ROLLUP(a,b,c)
		等同于
		GROUP BY   a,    b,    c
		UNION ALL
		GROUP BY   a,    b,   null
		UNION ALL 
		GROUP BY   a,   null, null
		UNION ALL 
		GROUP BY  null, null, null  --全表


			SELECT year_id, month_id, day_id, SUM(sales_value)
			FROM sales_tab
			GROUP BY
    			ROLLUP(year_id,month_id,day_id)
			ORDER BY year_id,month_id,day_id



#### 6.2)CUBE() 函数 全方位组合

- GROUP BY CUBE(a,b,c)  --自动组合所有可能的组合,最后进行全表的统计
 
- CUBE()函数会将所有参数的每种组合都作为一次分组方式进行统计然后将结果合并在一起,
所以分组次数为2的参数的个数的次方

		SELECT year_id,month_id,day_id,SUM(sales_value)
		FROM sales_tab
		GROUP BY 
			CUBE(year_id,month_id,day_id)
		ORDER BY year_id,month_id,day_id

#### 6.3) GROUPING SETS()函数	自定义组合

- GROUP BY  GROUPING SETS((a),(b))  --自定义组合,不会统计全表

- 每一个参数是一种分组方式,然后将这些分组统计的结果并在一个结果集中显示.

		--查看每天与每月营业额?
		SELECT year_id,month_id,day_id,SUM(sales_value)
		FROM sales_tab
		GROUP BY 
			GROUPING SETS(
		    		(year_id,month_id,day_id),
		    		(year_id,month_id)
		  			)
		ORDER BY year_id,month_id,day_id


- 可以在参数内加入一个'null',以显示全表的统计

		SELECT year_id,month_id,day_id,SUM(sales_value)
		FROM sales_tab
		GROUP BY 
			GROUPING SETS(
		    		(year_id,month_id,day_id),
		    		(year_id,month_id),
		  			 null)                     --在参数内加入一个null
		ORDER BY year_id,month_id,day_id

# Oracle-day05 #

### 1)视图
- 视图是数据库对象之一
- 视图在SQL语句中体现的角色与表一致,但并非真实存在的表,它是对应一个SELECT语句的查询结果集.
使用视图的主要目的:


	- 1:重用子查询,简化SQL复杂度
	- 2:限制数据访问

#### 1.1)CREATE VIEW ... AS ... 创建视图

	CREATE VIEW v_emp_hjx_10
	AS
	SELECT empno,ename,sal,deptno           --或者 SELECT *
	FROM emp_hjx
	WHERE deptno = 10

- 创建视图对应的子查询若含有函数或者表达式,那么该函数或者表达式必须给别名

		CREATE VIEW v_emp_hjx_10
		AS
		SELECT ename,sal*12 sal,JOB,deptno    --sal*12字段属于表达式,必须给别名
		FROM emp_hjx
		WHERE deptno = 10

#### 1.2)SELECT * FROM view_name 查看视图

	SELECT *
	FROM V_10


#### 1.3)DROP VIEW  view_name 删除视图

	DROP VIEW v_10


#### 1.4)CREATE OR REPLACE VIEW 修改视图 


- 修改视图就是替换视图中对应的子查询
 
		CREATE OR REPLACE VIEW v_emp_hjx_10
		AS
		SELECT empno,ename,sal,JOB,deptno
		FROM emp_hjx
		where deptno = 10;



### 2)对视图进行DML操作

- 对视图进行DML操作就是对视图数据来源的基础表进行的操作

- 只能对简单视图进行DML操作,复杂视图不允许执行DML操作,并且对简单视图进行DML操作不能违反视
图数据来源的基础表的约束条件

		--在视图中插入数据
		INSERT INTO v_emp_hjx_10
		(empno,ename,sal,JOB,deptno)
		VALUES
		(1001,'JACK',3000,'CLERK',10)

--基表的字段在视图中没有被显示的,基表会使用默认值NULL插入,	如果基表设置NOT NULL, 则执行不成功



		--在视图中修改数据
		UPDATE v_emp_hjx_10
		SET sal = 5000
		WHERE empno = 1001

 
		--在视图中删除数据
		DELETE FROM v_emp_hjx_10
		WHERE empno = 1001




- 对视图的不当DML操作会污染基础表数据,所谓污染,指的是对视图进行DML操作后,视图对基础表做相应的DML操作,但是操作后视图对这些数据不可见
	- 增加和更新会污染基础视图
		INSERT INTO v_emp_hjx_10
		(empno,ename,sal,JOB,deptno)
		VALUES
		(1001,'JACK',5000,'MANAGER',20)

		UPDATE v_emp_hjx_10
		SET deptno=20
 
	- 删除是没有数据污染情况的(因为删除只能删除对基础表可见的)
	 
		DELETE v_emp_hjx_10
		WHERE deptno = 20
 

#### 2.1)WITH CHECK OPTION 给视图添加检查选项 


- 为视图添加检查选项,这样做可以避免对视图进行DML操作后对基表数据污染,因为检查选项的意义在于视图对进行DML操作后的数据要检查是否可以见,不可见则不允许相关操作
 
		CREATE OR REPLACE VIEW v_emp_hjx_10
		AS
		SELECT empno,ename,sal,JOB,deptno
		FROM emp_hjx
		WHERE deptno = 10
		WITH CHECK OPTION  	--检查语句 

- 为视图添加检查选项后再进行插入,更新,如果视图不可见的话,则会提示子句违规,删除语句是可以的


#### 2.2)WITH READ ONLY 给视图添加只读选项


- 为视图增加只读选项后,该视图不允许进行DML操作
 
		CREATE OR REPLACE VIEW  v_emp_hjx_10
		AS
		SELECT empno,ename,sal,JOB,deptno
		FROM emp_hjx
		WHERE deptno = 10
		WITH READ ONLY

- 为视图添加只读选项后,插入和更新则会提示,不允许虚拟列


### 3) 数据字典
- 数据字典也是一堆表,这些表是由数据库自行维护,作为"清单"使用,可以查看如:
- 创建过的表,视图等信息

- OBJECT_TYPE字段: VIEW,TABLE,INDEX,SEQUENCE等 
#### 3.1)USER_OBJECTS:记录用户创建的所有数据库对象

	SELECT object_name,object_type
	FROM user_objects
	WHERE object_type = 'VIEW'
	AND object_name LIKE '%HJX%'


#### 3.2)USER_VIEWS:记录用户创建的所有视图

	SELECT view_name,text
	FROM user_views


#### 3.3)USER_TABLES:记录用户创建的所有表

	SELECT table_name
	FROM user_tables


###4)复杂视图:
- 视图对应的子查询中含有函数,表达式,分组,去重,关联查询等 操作时,该视图为复杂视图
- 复杂视图不允许进行DML操作

		--测式:创建一个部门工资情况的视图,包含信息:
		部门编号,部门名称,平均,最高,最低工资,和工资总和
		CREATE OR REPLACE VIEW v_emp_all
		AS
		SELECT d.deptno deptno,d.dname dname,CEIL(avg(e.sal)) avg ,MAX(e.sal) max, MIN(e.sal) min,SUM(e.sal) sum
		FROM emp_hjx e, dept_hjx d
		WHERE e.deptno = d.deptno
		GROUP BY d.deptno,d.dname


### 5)序列
- 序列也是数据库对象之一
- 序列是用来根据指定的规律生成一系列数字使用的.
- 通常使用序列为表的主键字段生成值

#### 5.1) CREATE SEQUENCE 创建序列


	CREATE SEQUENCE sequence_name
	[START WITH i][ INCREMENT BY j]
	[MAXVALUE m | NOMAXVALUE]  --生成的最大值 
	[MINVALUE n | NOMINVALUE]  --生成的最小值
	[CYCLE|NOCYCLE] [CACHE p | NOCACHE]
	


- 序列第一个序列值 是i,步进是j,如果j是正数表示递增,如果是负数表示递减
- 序列可生成的最大值是m 最小值是n
- 如果没有设置任何可选参数,第一个值是1,步进是1
- CYCLE表示递增至最大值,或递减至最小值之后是否重用序列,若是递减并有最大值,从最大值或始,若是递增并有最小值,从最小值开始,若没有则从START WITH指定的值开始.默认是NOCYCLE
- CACHE用来指定先预取p个数据在缓存中,以提高序列的生成效率,默认是20

#### 5.1)创建序列 ####

		--创建序列
		CREATE SEQUENCE seq_emp_id_hjx
		START WITH 1000
		INCREMENT BY 1



序列提供了两个伪列:
#### 5.2)NEXTVAL:  获取序列下一个数字

- 序列最后生成的数字加步长得到

	SELECT seq_emp_no_hjx.NEXTVAL
	FROM dual
 
#### 5.3)CURRVAL: 获取序列最后生成的数字

- 需要注意,新创建的序列至少使用NEXTVAL生成一个数字以后才可以开始使用CURRVAL.


- NEXTVAL会导致序列发生步进,且序列是不能回退的,不受事务影响


		--获取序列最后生成的数字
		SELECT seq_emp_id_hjx.CURRVAL
		FROM dual


#### 5.4)使用序列对象向emp_hjx表中插入数据

		INSERT INTO emp_hjx
		(empno,ename,sal,JOB,deptno)
		VALUES
		(seq_emp_id_hjx.NEXTVAL,'ROSS','3333','CLERK',10)

		--使用此方法可以获得无重复且不断列的序列
		SELECT MAX(empno)+1
		FROM emp_hjx


#### 5.5)DROP SEQUENCE 删除序列

- 语法:
	-  DROP SEQUENCE sequence_name

			DROP SEQUENCE seq_emp_id_hjx

### 6)索引


- 索引是数据库对象之一
- 索引的意义在于加快查询效率
- 索引的实际创建与使用是数据库自行完成的,我们的操作在于是否对表创建索引.

#### 6.1)CREATE INDEX ... ON ... 创建索引 ####

		CREATE INDEX idx_emp_ename_hjx
		ON emp_hjx(ename);


- 索引经常出现在WHERE过滤条件中,ORDER BY 中或DISTINCT 中的字段可以建立索引 ,需要注意的是,对于字符串类型字段,在WHERE 中若是LIKE判断是不会使用到索引的

- 合理使用索引提升查询效率
- 小表不建议加索引
- 经常做DML操作也不建议加索引



- 复合索引:复合索引也叫多列索引,是基于多个列的索引

		CREATE INDEX idx_emp_job_sal_hjx
		on emp_hjx(job,sal)

		--当做下面检查时,会自动应用建立的索引
		SELECT empno ,ename,sal,job 
		FROM emp_hjx
		ORDER BY job,sal


 

- 基于函数的索引: 如果需要在emp表的ename列上执行大小写无关搜索,可以在此列上建立一个基于upper函数的索引:
 
		CREATE INDEX emp_ename_upper_idx_hjx
		ON emp_hjx(upper(ename));
 
		--当做下面检查时,会自动应用建立的索引
		SELECT * 
		FROM emp_hjx
		WHERE UPPER(ENAME) = 'KING'
 
 

#### 6.2)ALTER INDEX ... REBUILD 重建索引


- 语法:ALTER INDEX index_name REBUILD

		--重建索引:
		ALTER INDEX idx_emp_ename_hjx REBUILD;
		--REBUILD表示将当前索引重建

#### 6.3)DROP INDEX  删除索引

- 语法:DROP INDEX index_name

		--删除索引
		DROP INDEX idx_emp_ename_hjx


### 7)约束

#### 7.1) NOT NULL 非空约束

- 创建时添加非空约束
 
		CREATE TABLE employees_hjx(
		eid NUMBER(6),
		name VARCHAR2(30) NOT NULL,   --简写非空约束
		salary NUMBER(7,2),
		hiredate DATE CONSTRAINT employees_hiredate_nn_hjx NOT NULL  -- 全写非空约束
		);


- 修改表时添加非空约束
		可以在建表之后,通过修改表的定义,添加非空约束:

		ALTER TABLE employees_hjx
		MODIFY( eid NUMBER(6) NOT NULL)


- 取消非空约束  NULL

		--可以采用重建表和修改表的方式
		ALTER TABLE employees_hjx
		MODIFY( eid NUMBER(6) NULL)


####7.2)UNIQUE 唯一性约束

- 当表中某个字段使用了唯一性约束后,该字段的值不允许有重复的,NULL除外


- 创建表时添加唯一性约束
	- 一般在定义列的同时,加的约束(NOT NULL,NULL),我们叫列级约束

			CREATE TABLE employees1_hjx(
			eid NUMBER(6) UNIQUE,   --列级约束
			NAME VARCHAR2(30),
			email VARCHAR2(50),
			salary NUMBER (7,2),
			hiredate DATE,
			CONSTRAINT employees_email_uk_hjx UNIQUE(email)  
			)


- 在建表之后添加唯一性约束
	- 在建表之后增加唯一性约束条件:我们叫表级约束,表级约束可以更好的进行维护
	 
			ALTER TABLE employees1_hjx
			ADD CONSTRAINT employees1_name_uk_hjx UNIQUE(NAME)

- 如果表中已有数值是重复的 再添加唯一性约束时,会出现找到重复关键字,添加约束失败


#### 7.3)PRIMARY KEY 主键约束  非空且唯一


- 在建表时添加主键约束条件
 
		CREATE TABLE employees2_hjx(
		eid NUMBER(6) PRIMARY KEY,
		NAME VARCHAR2(30),
		email VARCHAR2(50),
		salary NUMBER(7,2),
		hiredate DATE
		)

- 建表后添加主键约束

		ALTER TABLE employees3
		ADD CONSTRAINT employees3_eid_pk PRIMARY KEY(eid);

#### 7.4)FOREIGN KEY 外键约束 可以为空 ####

- 外键存的值必须是主键里有的或者是NULL
- 当主线参照列的值被从表参照时,主表的该行记录不允许删除

- 在建表时添加外键约束条件

	 		--方式一:在所有字段定义后添加
			CREATE TABLE employees2_hjx(
			eid NUMBER(6),
			ename VARCHAR2(30),
			email VARCHAR2(50),
			salary NUMBER(7,2),
			hiredate DATE,
			deptno NUMBER(4),
			FOREIGN KEY (deptno) REFERENCES dept(deptno)
			)

			--方式二:直接在字段后添加
			CREATE TABLE employees2_hjx(
			eid NUMBER(6),
			ename VARCHAR2(30),
			email VARCHAR2(50),
			salary NUMBER(7,2),
			hiredate DATE,
			deptno NUMBER(4) REFERENCES dept(deptno)
			)

- 建表后添加外键约束

		--方式一:  
		ALTER TABLE employees3
		ADD CONSTRAINT employees3_eid_pk   --添加约束名
		FOREIGN KEY(deptno) REFERENCES dept(deptno) 

		--方式二:
		ALTER TABLE employees3
		ADD  
		FOREIGN KEY(deptno) REFERENCES dept(deptno) 



#### 7.5)CHECK 检查约束

- 创建表后添加检查约束
 
		ALTER TABLE employees2_hjx
		ADD CONSTRAINT employees2_salary_check_hjx
		CHECK(salary > 2000)



![](emp.png)


![](dept.png)






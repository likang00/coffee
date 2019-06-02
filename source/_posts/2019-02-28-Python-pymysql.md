---
title: Python-pymysql
date: 2019-02-28 23:01:21
tags: 数据库
---

#### pymysql
今天学习一下mysql，在python3中利用PyMySQL模块来操作。
先安装包:`pip install PyMySQL`

---

## 1.语法速览
```sql
1、语法格式
    对象名 = pymysql.connect("主机地址","用户名","密码","库名","端口号")
2、connect连接对象支持的方法
    1、cursor() 创建一个游标对象db.cursor()
    2、commit() 提交到数据库执行(表记录增删改)
    3、rollback() 回滚
    4、close() 关闭数据库连接
3、游标对象支持的方法
    1、execute("SQL命令") 执行SQL命令
    2、fetchone() 取得结果集的第一条记录
    3、fetchmany(n) 取得结果集的 n 条记录
    4、fetchall() 取得结果集的所有记录
    5、close() 关闭游标对象
```

---

## 2.下面记录一下操作
#### 建立连接(腾讯云数据库[mysql5.7])
```py
import pymysql
# 建立连接
# db = pymysql.connect(host=, user=, password=, database=, port=)
db = pymysql.connect("xxxxx.com", "root", "mysq1112", "MyDB", xxxxx)
# 创建游标对象
cur = db.cursor()
```

#### 数据库版本信息

```py 
cur.execute('SELECT VERSION()')
data = cur.fetchone()
print(f"Database version :{data}")
```

#### 建表，插入数据

###### test1

```py 
# creat table test
sql_create ='create table test(id INT(8) , name VARCHAR(20) , age INT(3) , value INT(3) )'
cur.execute(sql_create)
cur.connection.commit()
# insert table test
sql_insert = 'INSERT INTO test(id, name, age, value) VALUES (10002, "docker", 15, 87)'
cur.execute(sql_insert)
cur.connection.commit()
```

###### test2
```py 
# create table students
sql_create = '''CREATE TABLE students(
`id` bigint(20) NOT NULL AUTO_INCREMENT,
`class_id` bigint(20) NOT NULL,
`name` varchar(100) NOT NULL,
`gender` varchar(1) NOT NULL,
`score` int(11) NOT NULL,
PRIMARY KEY (`id`)
)
'''
cur.execute(sql_create)
# insert table students 
list = [(1,1,'小明','M',90),
        (2,1,'小红','F',95),
        (3,1,'小军','M',88),
        (4,1,'小米','F',73),
        (5,2,'小白','F',81),
        (6,2,'小兵','M',55),
        (7,2,'小林','M',85),
        (8,3,'小新','F',91),
        (9,3,'小王','M',89),
        (10,3,'小丽','F',88)]
for i in list:
    sql_inserts = f'INSERT INTO students(id, class_id, name, gender, score) VALUES {i}'
    print(sql_inserts)
    cur.execute(sql_inserts)
    cur.connection.commit()
```

#### 查询

##### 全表查询

```py 
sql_select = '''select * from Writers'''
cur.execute(sql_select)
result = cur.fetchall()
print(f"select * from:{result}")
```

#### 删除

```py 
cur.execute('drop database <database_name>')
cur.execute('drop table <table_name>')
```

#### 记得关闭数据库

`db.close()`

---

## 3.快速执行SQL语句

#### 快速运行SQL语句

``` py
    cur.execute('SQL语句')
    print(cur.fetchall())
```

#### SQL语句

``` sql
-- 按AND条件查询students
SELECT * FROM students WHERE score >= 80 AND gender = 'M'
-- 按OR条件查询students
SELECT * FROM students WHERE score >= 80 OR gender = 'M'
-- 按NOT条件查询students
SELECT * FROM students WHERE NOT class_id = 2
-- 按多个条件查询students
SELECT * FROM students WHERE (score < 80 OR score > 90) AND gender = 'M'
-- 排序test
SELECT id, name, gender, score FROM students ORDER BY id;
-- desc 倒序
SELECT id, name, gender, score FROM students ORDER BY id DESC;
-- ASC升序 DESC降序
SELECT id, name, gender, score FROM students ORDER BY gender, score;
```

---

## 4.查询

#### 分页查询

```py 
def fun1(pageSize, pageIndex):
    limit = pageSize
    offset = pageSize * (pageIndex - 1)
    print(f'limit:{limit},offset:{offset}')
    cur.execute(f'''
    SELECT id, name, gender, score FROM students ORDER BY id LIMIT {limit} OFFSET {offset}
    ''')
    print(cur.fetchall())
fun1(3,4)
```

#### 聚合查询

```py 
cur.execute('''
SELECT COUNT(*) boys FROM students WHERE gender = 'M'
''')
print(cur.fetchall())
```

##### 其他聚合函数

| 函数名 | 功能 |
|----|----|
|SUM|计算某一列的合计值，该列必须为数值类型|
|AVG|计算某一列的平均值，该列必须为数值类型|
|MAX|计算某一列的最大值|
|MIN|计算某一列的最小值|

##### 分组聚合

```py 
cur.execute('''
SELECT COUNT(*) boys FROM students GROUP BY class_id
''')
print(cur.fetchall())
```

```py {.line-numbers}
cur.execute('''
SELECT class_id, gender, COUNT(*) num FROM students GROUP BY class_id, gender;
''')
print(cur.fetchall())
```

#### 多表查询

###### test1

```py 
cur.execute('''
SELECT * FROM students, classes;
''')
```

###### test2

```py
cur.execute('''
SELECT
    s.id sid,
    s.name,
    s.gender,
    s.score,
    c.id cid,
    c.name cnameo
FROM students s, classes c
WHERE s.gender = 'M' AND c.id = 1;
''')
```

#### 连接查询

###### 连接查询是另一种类型的多表查询。连接查询对多个表进行JOIN运算，简单地说，就是先确定一个主表作为结果集，然后，把其他表的行有选择性地“连接”在主表结果集上。

```py
# 连接查询
cur.execute('''
SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
FROM students s
INNER JOIN classes c
ON s.class_id = c.id
''')
print(cur.fetchall())
```

###### 
    1.先确定主表，仍然使用FROM <表1>的语法；
    2.再确定需要连接的表，使用INNER JOIN <表2>的语法；
    3.然后确定连接条件，使用ON <条件...>，这里的条件是s.class_id = c.id，表示students表的class_id列与classes表的id列相同的行需要连接；
    4.可选：加上WHERE子句、ORDER BY等子句。

###### 各种连接语句

`SELECT ... FROM tableA ??? JOIN tableB ON tableA.column1 = tableB.column2;`

1.INNER JOIN

![](o_INNER_JOIN.png)

<!-- <p align="center">
	<img src="https://images.cnblogs.com/cnblogs_com/spacescp/1383720/o_INNER_JOIN.png" alt="Sample"  width="100" height="50">
	<p align="center">
		<em>图片示例2</em>
	</p>
</p> -->

###### 只返回同时存在于两张表的行数据

2.LEFT OUTER JOIN

![](o_LEFT_OUTER_JOIN.png)

###### 返回左表都存在的行

3.RIGHT OUTER JOIN

![](o_RIGHT_OUTER_JOIN.png)

###### 返回右表都存在的行

4.FULL OUTER JOIN

![](o_FULL_OUTER_JOIN.png)

###### 两张表的所有记录全部选择出来

#### -补充-

##### JOIN查询
JOIN查询需要先确定主表，然后把另一个表的数据“附加”到结果集上；
INNER JOIN是最常用的一种JOIN查询，它的语法是SELECT ... FROM <表1> INNER JOIN <表2> ON <条件...>；
JOIN查询仍然可以使用WHERE条件和ORDER BY排序。

##### 条件表达式
| 条件 | 表达式举例1 | 表达式举例2 | 说明 |
| ----- | ----- | ----- | ----- |
|使用=判断相等|score = 80|name = 'abc'|字符串需要用单引号括起来|
|使用>判断大于|score > 80|name > 'abc'|字符串比较根据ASCII码，中文字符比较根据数据库设置|
|使用>=判断大于或相等|score >= 80|name >= 'abc'|
|使用<判断小于|score < 80|name <= 'abc'|
|使用<=判断小于或相等|score <= 80|name <= 'abc'|
|使用<>判断不相等|score <> 80|name <> 'abc'|
|使用LIKE判断相似|name LIKE 'ab%'|name LIKE '%bc%'|%表示任意字符，例如'ab%'将匹配'ab'，'abc'，'abcd'|

---

## 5.修改数据

#### INSERT
#### UPDATE
#### DELETE

*---等待更新---*
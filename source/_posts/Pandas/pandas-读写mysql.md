---
title: pandas-读写mysql
date: 2020-07-03 15:38:28
tags:
categories: pandas 
---


## pandas-mysql
使用pandas可以方便快捷地读写mysql，使用sql语句直接查询，DataFrame直接存入数据库甚至不用建表结构。


## 建立连接

#### 示例
```python 
import pandas as pd
from sqlalchemy import create_engine
engine = create_engine('mysql+pymysql://root:admin@localhost:3306/test')
```

#### 参数说明
* engine = create_engine('dialect+driver://username:password@host:port/database')
* * dialect -- 数据库类型
* * driver -- 数据库驱动选择
* * username -- 数据库用户名
* * password -- 用户密码
* * host 服务器地址
* * port 端口
* * database 数据库


## 读数据 pd.read_sql
```python 
sql = 'select * from table limit 1'
df = pd.read_sql(sql, engine)
```

## 写数据 df.to_sql

#### 示例
```
data = {'state': ['Ohio', 'Ohio', 'Ohio', 'Nevada', 'Nevada', 'Nevada'],
        'year': [2000, 2001, 2002, 2001, 2002, 2003],
        'pop': [1.5, 1.7, 3.6, 2.4, 2.9, 3.2]}
df = pd.DataFrame(data)
df.to_sql(name='df1', con=engin, index=None, if_exisis='replace')
```

#### 参数说明
* chunksize可以设置一次入库的大小
* if_exists设置如果数据库中表名存在时的操作
* * replace表示将表原来数据删除放入当前数据
* * append表示追加
* * fail则表示将抛出异常，结束操作
* * 默认是fail
* index=接受boolean值，表示是否将DataFrame的index也作为表的列存储。

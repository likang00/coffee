---
title: 'pandasql:在pandas中运行sql'
date: 2020-06-15 10:15:42
tags:
categories:
---

## 介绍
pandasql是一个模拟 R 包 sqldf 的Python 库。
这是一个小而强大的库，只有358行代码。
pandasql 的想法是让 Python 运行 SQL。

## 快速使用
1.安装pandasql包
```shell
pip install pandasql
```

2.基本用法
```python
from pandasql import sqldf
import pandas as pd

def pysqldf(q):
    return sqldf(q, globals())

# 使用匿名函数也可
# pysqldf = lambda q: sqldf(q, globals())

# 获取待处理的DataFrame
# df_test = xxx

q = 'select * from df_test'

df1 = pysqldf(q)
print(df1)
```

## 自带数据集
pandasql内置了两个数据集，方便测试使用。
meat：数据集来自美国农业部，包含有关牲畜，乳制品和家禽前景和生产的指标
births：数据集来自联合国统计司，包含按月计算的活产婴儿人口统计

```python
import pandas as pd 
import matplotlib.pyplot as plt 
from pandasql import sqldf
from pandasql import load_meat, load_births


meat = load_meat()
births = load_births()

pysqldf = lambda q: sqldf(q, globals())
q = '''
select 
m.date,
m.beef,
b.births 
from meat m 
left join births b 
on m.date = b.date  
where m.date > '1974-12-32'
'''

df = pysqldf(q)
print(df)

df.births = df.births.fillna(method='backfill')

fig = plt.figure()
ax1 = fig.add_subplot(111)
ax1.plot(df['beef'].rolling(12).mean(), color='b')

ax2 = ax1.twinx()
ax2.plot(df['births'].rolling(12).mean(), color='r')
ax2.set_ylabel('babies born', color='r')
plt.title('Beef Consumption and the Birth Rate')
plt.show()
```
![](/plt1.jpg)

## locals() 与 globals()
pandasql 需要在会话/环境中访问其他变量。
虽然当执行 SQL 语句时，可以传递 locals() 给 pandasql，但是如果你运行了大量可能麻烦的查询。
为了避免一直传递给 locals，你可以将这个帮助函数添加到脚本中，来其设置 globals() 如下：

```python 
# 定义函数
def pysqldf(q):
    return sqldf(q, globals())

# 定义匿名函数
pysqldf = lambda q: sqldf(q, globals())
```
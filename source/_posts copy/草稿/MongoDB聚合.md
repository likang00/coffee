---
title: MongoDB聚合
date: 2018-12-27 22:33:04
tags: 数据库
categories: db
---

# 聚合管道功能：

* 对文档进行过滤，查询出符合条件的文档
* 对文档进行变换，改变文档的输出形式


# 使用sanic_motor模块连接数据库

``` py
from sanic import Sanic
from sanic.views import HTTPMethodView
from sanic.response import json, text, redirect, html
from sanic_motor import BaseModel

app = Sanic(__name__)

# 设置数据库和集合
settings = {'MOTOR_URI': 'mongodb://localhost:27017/XXX', 'LOGO': None}
app.config.update(settings)
app.config.RESPONSE_TIMEOUT = 5*60
BaseModel.init_app(app)

class Subjectscope(BaseModel):
    __coll__ = 'XXX'

class Subject2020(HTTPMethodView):
    async def get(self, request):
        UniversityList = await Subjectscope.find(as_raw=True)
        RucodeList = []
        for Uni in UniversityList.objects:
            Rucode = Uni.get("Rucode", "")
            if Rucode not in RucodeList:
                RucodeList.append(Rucode)
            else:
                continue
        print(RucodeList)
        return json({"Rucode": "success"})
app.add_route(Subject2020.as_view(), 'rucodelist')

class SubjectAggregate(HTTPMethodView):
    async def get(self, request):
        my_pipeline =[
            # {"$skip":33440},
            {"$match":{"Rucode":{"$gte":'10001',"$lte":'10090'}}},
            # {"$group":{"_id":"$Rucode", "count":{"$sum":1}}}
            {"$project":{
                        "_id":0,
                        "_rucode":"$Rucode",
                        "SchoolName":1,
                        "ProfessionalType":1,
                        "typeid": {"$type":"$_id"},
                        "scopeMid":"$_id"}},
            {"$sort":{"_rucode":-1}},
            {"$limit":10},
            {"$sample":{"size":4}},
            # {"$group":{"_id":"$ProfessionalType","count":{"$sum":1}}}
            ]
        Myaggregate = await Subjectscope.aggregate(pipeline=my_pipeline)
        print(len(Myaggregate))
        return json(Myaggregate)
app.add_route(SubjectAggregate.as_view(), 'aggregate')
if __name__ == '__main__':
    app.run(host="127.0.0.1", port=8003, workers=1)
```

```sql{class=line-numbers}
db.getCollection('subjectscope2020_benke').aggregate([
    {"$match":{"Rucode":{"$gt":'10001',"$lte":'10003'}}},
    //{"$project":{"_id":0, "Rucode":1, "SchoolName":1, "ProfessionalType":1}}
])
```

# 1.聚合管道

## 一、阶段操作符

1.$project
**提取** (修改文档结构、重命名、增加或删除字段)
``` js
db.getCollection('subjectscope2020_benke').aggregate([
{$project:
    {aid:{$type:"$_id"},brucode:{$type:"$Rucode"},cshcoolname:"$SchoolName"}
}
])
```
## 2.$match
**筛选**
## 3.$group
**分组**

``` js
// $sum	计算总和。	
db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$sum : "$likes"}}}])
// $avg	计算平均值	
db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$avg : "$likes"}}}])
// $min	获取集合中所有文档对应值得最小值。	
db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$min : "$likes"}}}])
// $max	获取集合中所有文档对应值得最大值。	
db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$max : "$likes"}}}])
// $push	在结果文档中插入值到一个数组中。	
db.mycol.aggregate([{$group : {_id : "$by_user", url : {$push: "$url"}}}])
// $addToSet	在结果文档中插入值到一个数组中，但不创建副本。	
db.mycol.aggregate([{$group : {_id : "$by_user", url : {$addToSet : "$url"}}}])
// $first	根据资源文档的排序获取第一个文档数据。	
db.mycol.aggregate([{$group : {_id : "$by_user", first_url : {$first : "$url"}}}])
// $last	根据资源文档的排序获取最后一个文档数据	
db.mycol.aggregate([{$group : {_id : "$by_user", last_url : {$last : "$url"}}}])
```

## 4.$sort
**排序**
## 5.$limit
**限制**
## 6.$skip
**跳过**
## 7.$unwind
**拆分**

# 二、表达式操作符

## 1.布尔管道聚合操作（Boolean Aggregation Operators）
`$and $or $not`

## 2.集合操作（Set Operators）

``` js
$setEquals 
$setIntersection 
$setUnion 
$serDifference 
$setIsSubset 
$anyElementTrue
$allElementsTrue 
```
## 3.比较聚合操作（Comparison Aggregation Operators）

``` js
$cmp 比较表达式中两个值的大小，如果第一个值小于第二个值则返回-1，相等返回0，大于返回1。用法{ $cmp: [ <expression1>, <expression2> ] }
$eq 比较表达式中两个是否相等，是则返回true，否则返回false。用法{ $eq: [ <expression1>, <expression2> ] }
$gt 比较表达式中第一个值是否大于第二个值，是则返回true，否则返回false。用法{ $gt: [ <expression1>, <expression2> ] }
$gte 大于等于
$lt 比较表达式中第一个值是否小于第二个值，是则返回true，否则返回false。用法{ $lt: [ <expression1>, <expression2> ] }
$lte 小于等于
$ne 是否不相等 not equals
```

## 4.算术聚合操作（Arithmetic Aggregation Operators）

``` js
$abs
$add
$ceil
$divide
$exp
$floor
$ln
$log
$log10
$mod
$multiply
$pow
$sqrt
$subtract
$trunc
```

## 5.字符串聚合操作（String Aggregation Operators）

``` js
$concat
$indexOfBytes
$indexOfCP
$split
$strLenBytes
$strLenCP
$strcasecmp
$substr
$substrBytes
$substrCP
$toLower
$toUpper
```

## 6.数组聚合操作（Array Aggregation Operators）

``` js
$arrayElemAt
$concatArrays
$filter
$indexOfArray
$isArray
$range
$reverseArray
$reduce
$size
$slice
$zip
$in
```

## 7.日期聚合操作（Date Aggregation Operators）

``` js
$dayOfYear
$dayOfMonth
$dayOfWeek
$year
$month
$week
$hour
$minute
$second
$millisecond
$dateToString
$isoDayOfWeek
$isoWeek
$isoWeekYear
```

## 8.条件聚合操作（Conditional Aggregation Operators）

``` js
$cond
$ifNull
$switch
```

## 9.数据类型聚合操作（Data Type Aggregation Operators）

```
$type
```

## 10.其他

``` js
//$exists 存在
db.person.find( { phone: { $exists: true } } )
```

# 2.单目的聚合操作
"SS_Class_Info":{"$exists":true}
## distinct("name":{"$exists":true})
**去重**
## count()
**返回文档个数**

----

``` py
# 求出集合 article 中 time 值大于 2017-04-09 的文档个数
db.article.count({time:{$gt:newDate('04/09/2017')}})
# 这个语句等价于
db.article.find({time:{$gt:newDate('04/09/2017')}}).count()
```

----

## 几个例子

``` js
db.getCollection('SS_SeleCourseResult').aggregate([
{"$match":{"Choose_Subject_Task_ID" : "5c106c08705deb0862d3bfa9"}},
{"$match":{"result":{"$elemMatch":{"Subject":"物理","Status":1,"SS_Class_Info":{"$exists":true},"SS_Class_Info.Class_Name":"物理选考2班"}}}},
{"$unwind":"$result"},
{"$match":{"result.Subject":"","result.Status":3}},
{"$group":{"_id":"$result.SS_Class_Info.Class_Name","studentlist":{"$push":"$UserStudent_Name"}}}
])
```

``` js
db.getCollection('sort_SubjectSCope2020').aggregate([
{"$match":{"Rucode":{"$gte":"10001","$lt":"10002"}}},
{"$unwind":"$ScopeArea"},
{"$match":{"ScopeArea.Province":"浙江省",}},
{"$unwind":"$ProfessionalName"},
{"$project":{"_id":0,"SchoolName":1,"ProfessionalType":1,"ProfessionalName":1,"Scope":1}},
{"$group":{"_id":"$Scope","count":{"$sum":1},"pfnamelist":{"$push":"$ProfessionalName"}}},
])
```
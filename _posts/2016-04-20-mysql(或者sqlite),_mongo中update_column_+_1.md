---
layout: post
title: Mysql(或者sqlite), Mongo中update Column + 1
categories: [技术博客]
tags: [mysql, mongo, column+1]
---
### Mysql(或者sqlite), Mongo中update Column + 1

有类似以下需求，在数据库表里有一个字段，记录了一个count，然后又时候需要在count的基础上加上某个数字，比如1。看到这个需求后，由于是要操作mongo数据库，发现mongo update的时候**$inc**可以实现这个结果，但是没想起Mysql或者sqlite要怎么操作，总不能query拿到count后再update吧。在stackoverflow上找到了以下的解决方案，果然是简单方便。

**update table set column = column + n**

这是里相关网址
[http://stackoverflow.com/questions/2762851/increment-a-database-field-by-1](http://stackoverflow.com/questions/2762851/increment-a-database-field-by-1)

[http://stackoverflow.com/questions/3866524/mysql-update-column-1](http://stackoverflow.com/questions/3866524/mysql-update-column-1)

```
sqlite> create table test2 (count integer, id text);
sqlite> insert into test2 values(10, 'aa');
sqlite> insert into test2 values(0, 'bb');
sqlite> select * from test2;
10|aa
0|bb
sqlite> update test2 set count = count + 1 where id = 'aa'
   ...> ;
sqlite> select * from test2;
11|aa
0|bb
sqlite> update test2 set count = count + 2 where id = 'aa';
sqlite> select * from test2;
13|aa
0|bb
sqlite>
```

对于mongo里的解决方案

```
{ $inc: { <field1>: <amount1>, <field2>: <amount2>, ... } }
```

官方文档如下
[https://docs.mongodb.org/manual/reference/operator/update/inc/](https://docs.mongodb.org/manual/reference/operator/update/inc/)

在mongoose里可以使用findOneAndUpdate.

```
var update = {$inc:{rate:1}}; //重点
UserSing.model.findOneAndUpdate({'user': userId}, update, {new: true, upsert: true}, function(err, sing){
	if(err) {
      	return next(new CommonError(ErrCode.invalid_parameter, '修改状态失败'));
      	console.error(condition + err);
	}
	console.error(sing);
    res.send(sing);
});
```

[http://stackoverflow.com/questions/8621948/doing-inc-with-mongoose](http://stackoverflow.com/questions/8621948/doing-inc-with-mongoose)




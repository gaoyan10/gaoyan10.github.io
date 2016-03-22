---
title: 编程技巧留存
layout: post
categories: [Node学习]
tags: [node, mongoose]
---

Node中mongoose返回的数据是Model instance，无法修改。<br>
两种方式:<br>

1. 查殉时加入lean，直接返回plain js object<br>
[stackoverflow的链接，第一个回答](http://stackoverflow.com/questions/14504385/why-cant-you-modify-the-data-returned-by-a-mongoose-query-ex-findbyid)<br>
2. 第二种，返回的model instance有toObject方法，instance.toObject后转化为plain js object.<br>
[stackOverflow的链接，第一个回答](http://stackoverflow.com/questions/7503450/how-do-you-turn-a-mongoose-document-into-a-plain-object)

model instance删除字段，user.wxid = undefined;
object删除字段 delete obj.name
数组 push pop
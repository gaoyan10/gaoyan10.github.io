---
layout: post
title: Android内核剖析——Context
categories: [阅读笔记]
tags: [Android, Context]
---

### Context的数量

一个Activity就是一个场景，一个Service也是，所以Application中有多少个Activity或者Service就有多少个Context。

getResources()返回的是一个全局的对象。
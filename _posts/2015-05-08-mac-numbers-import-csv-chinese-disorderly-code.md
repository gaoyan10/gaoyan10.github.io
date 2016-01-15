---
layout: post
title: mac下用numbers打开支付宝账单中文乱码处理方案
categories: [mac]
tags: [mac, numbers, csv, 中文乱码]
---

今天需要计算一下统计1下支付宝3月和4月的电子账单。	
从支付宝导出.zip文件，解压得到 .csv 格式的文件。		
如果直接用numbers打开，中文会出现乱码。用普通的文本编辑器打开就没问题。考虑可能是格式编码问题。	
google得到解决方案

>		iconv -c -f GBK -t UTF-8 xxx.csv > 1.csv
>
>	在终端用命令转换一下，然后再打开

测试一下果然可以。

附上[原文地址][macnumberscsv]

[macnumberscsv]:http://bbs.feng.com/read-htm-tid-8146612.html

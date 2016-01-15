---
layout: post
title: linux/unix 平台下virtualbox转发本地80端口到虚拟机中
categories: [调试部署]
tags: [Mac, linux, unix, virtualbox, web]
---

最近在做商派的产品，考虑到环境一致性和性能，使用virtual虚拟机虚拟debian系统使用。	
开发配置的端口转发是	

```text
127.0.0.1:8888 => 10.0.2.15:80
127.0.0.1:2222 => 10.0.2.15:22
```

不过在实际使用中发现，ecstore和erp接口部分只认host，不认端口。	
也就是说需要实现

```text
127.0.0.1:80 => 10.0.2.15:80
```

直接配置virtualbox端口映射为80=>80无效。差了一下资料得到如下关键字

	Forwarding host ports < 1024 impossible:

	On Unix-based hosts (e.g. Linux, Solaris, Mac OS X) it is not possible to bind to ports below 1024 from applications that are not run by root. As a result, if you try to configure such a port forwarding, the VM will refuse to start.

看到以后心都哇凉哇凉的。不过还好有解决方案。

```text
127.0.0.1:80 => 127.0.0.1:8888 => 10.0.2.15:80
```

实现端口转发。具体决解方案如下

1. 开启本地ssh服务器		
	mac下开启远程登陆共享
2. 执行以下命令		

		$ sudo su -			
		$ ssh watsy@localhost -L 80:localhost:8888		

3. 保留第一次virtualbox的配置		
4. 启动virtualbox		

<br>
解决方案参考如下

http://stackoverflow.com/questions/11388657/port-forwarding-from-host-port-80-to-virtualbox-port-80-doesnt-work




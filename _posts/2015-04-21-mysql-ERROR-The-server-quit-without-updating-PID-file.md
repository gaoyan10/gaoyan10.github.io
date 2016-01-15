---
layout: post
title: mysql 启动时候 [FAIL..] The server quit without updating PID file (/usr/local/mysql/var/debian.pid). ... failed 解决办法
category: mysql
tags: [php,mysql,bug]
---

工作原因，需要把1个系统中的数据导入另外1个系统。在本地调试过程中出现锁死了mysql。网上尝试```kill try_mysql_thread_id ```无效。
无奈强行把虚拟机关闭重启系统。
结果mysql无法启动了。

启动虚拟机以后连接。

	updatedb && locate *.err

得到错误信息

	/root/lnmp1.1-full/nginx-1.6.0/objs/autoconf.err
	/usr/local/mysql/var/debian.err
	/var/log/mail.err
	/var/log/news/news.err

查看 ```/usr/local/mysql/var/debian.err``` 中的错误得到

```
	150421 21:45:45 mysqld_safe Starting mysqld daemon with databases from /usr/local/mysql/var
	150421 21:45:45 InnoDB: The InnoDB memory heap is disabled
	150421 21:45:45 InnoDB: Mutexes and rw_locks use GCC atomic builtins
	150421 21:45:45 InnoDB: Compressed tables use zlib 1.2.7
	150421 21:45:45 InnoDB: Initializing buffer pool, size = 16.0M
	150421 21:45:45 InnoDB: Completed initialization of buffer pool
	150421 21:45:45 InnoDB: highest supported file format is Barracuda.
	InnoDB: Log scan progressed past the checkpoint lsn 88875903
	150421 21:45:45  InnoDB: Database was not shut down normally!
	InnoDB: Starting crash recovery.
	InnoDB: Reading tablespace information from the .ibd files...
	InnoDB: Restoring possible half-written data pages from the doublewrite
	InnoDB: buffer...
	InnoDB: Doing recovery: scanned up to log sequence number 88876119
	150421 21:45:45  InnoDB: Starting an apply batch of log records to the database...
	InnoDB: Progress in percents: 84 85 86 87 88 89 90 91 92 93 94 95 96 97 98 99 
	InnoDB: Apply batch completed
	InnoDB: Last MySQL binlog file position 0 929621, file name ./mysql-bin.000119
	150421 21:45:45  InnoDB: Waiting for the background threads to start
	150421 21:45:46 InnoDB: 5.5.37 started; log sequence number 88876119
	13:45:46 UTC - mysqld got signal 11 ;
	This could be because you hit a bug. It is also possible that this binary
	or one of the libraries it was linked against is corrupt, improperly built,
	or misconfigured. This error can also be caused by malfunctioning hardware.
	We will try our best to scrape up some info that will hopefully help
	diagnose the problem, but since we have already crashed, 
	something is definitely wrong and this may fail.

	key_buffer_size=16777216
	read_buffer_size=262144
	max_used_connections=0
	max_threads=151
	thread_count=0
	connection_count=0
	It is possible that mysqld could use up to 
	key_buffer_size + (read_buffer_size + sort_buffer_size)*max_threads = 134092 K  bytes of memory
	Hope that's ok; if not, decrease some variables in the equation.

	Thread pointer: 0x0
	Attempting backtrace. You can use the following information to find out
	where mysqld died. If you see no messages after this, something went
	terribly wrong...
	stack_bottom = 0 thread_stack 0x40000
	/usr/local/mysql/bin/mysqld(my_print_stacktrace+0x29)[0x76eb49]
	/usr/local/mysql/bin/mysqld(handle_fatal_signal+0x35c)[0x6732cc]
	/lib/x86_64-linux-gnu/libpthread.so.0(+0xf0a0)[0x7f8ead63f0a0]
	The manual page at http://dev.mysql.com/doc/mysql/en/crashing.html contains
	information that should help you find out what is causing the crash.
	150421 21:45:46 mysqld_safe mysqld from pid file /usr/local/mysql/var/debian.pid ended
	150421 21:51:38 mysqld_safe Starting mysqld daemon with databases from /usr/local/mysql/var
	150421 21:51:38 InnoDB: The InnoDB memory heap is disabled
	150421 21:51:38 InnoDB: Mutexes and rw_locks use GCC atomic builtins
	150421 21:51:38 InnoDB: Compressed tables use zlib 1.2.7
	150421 21:51:38 InnoDB: Initializing buffer pool, size = 16.0M
	150421 21:51:38 InnoDB: Completed initialization of buffer pool
	150421 21:51:38 InnoDB: highest supported file format is Barracuda.
	InnoDB: Log scan progressed past the checkpoint lsn 88875903
	150421 21:51:38  InnoDB: Database was not shut down normally!
	InnoDB: Starting crash recovery.
	InnoDB: Reading tablespace information from the .ibd files...
	InnoDB: Restoring possible half-written data pages from the doublewrite
	InnoDB: buffer...
	InnoDB: Doing recovery: scanned up to log sequence number 88876119
	150421 21:51:38  InnoDB: Starting an apply batch of log records to the database...
	InnoDB: Progress in percents: 33 34 35 36 37 38 39 40 41 42 43 44 45 46 47 48 49 50 51 52 53 54 55 56 57 58 59 60 61 62 63 64 65 66 67 68 69 70 71 72 73 74 75 76 77 78 79 80 81 82 83 84 85 86 87 88 89 90 91 92 93 94 95 96 97 98 99 
	InnoDB: Apply batch completed
	InnoDB: Last MySQL binlog file position 0 929621, file name ./mysql-bin.000119
	150421 21:51:38  InnoDB: Waiting for the background threads to start
	150421 21:51:39 InnoDB: 5.5.37 started; log sequence number 88876119
	150421 21:51:39 [Note] Server hostname (bind-address): '0.0.0.0'; port: 3306
	150421 21:51:39 [Note]   - '0.0.0.0' resolves to '0.0.0.0';
	150421 21:51:39 [Note] Server socket created on IP: '0.0.0.0'.
	150421 21:51:39 [Note] Event Scheduler: Loaded 0 events
	150421 21:51:39 [Note] /usr/local/mysql/bin/mysqld: ready for connections.
	Version: '5.5.37-log'  socket: '/tmp/mysql.sock'  port: 3306  Source distribution
```

看到```InnoDB: Last MySQL binlog file position 0 929621, file name ./mysql-bin.000119```猜测是恢复错误时候异常。
果断执行

	rm -rf rm -rf /usr/local/mysql/var/mysql-bin.*

重启ok了。


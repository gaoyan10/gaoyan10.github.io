---
layout: post
title: 在mac os x上执行gem install cassanrda提示In file included from strlcpy.c:20 错误解决办法
category: 调试部署
tags: [cassanrda, 数据库]
---

最近在和朋友测试cassandra数据库时候。
想测试远程操作。
网上找了下有ruby的库。很happy
mac下执行。

	sudo gem install cassandra

得到如下错误

	    Building native extensions.  This could take a while...
    ERROR:  Error installing cassandra:
        ERROR: Failed to build gem native extension.

        /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/bin/ruby -r ./siteconf20150222-28580-iv1cvb.rb extconf.rb
    checking for strlcpy() in string.h... yes
    creating Makefile

    make "DESTDIR=" clean

    make "DESTDIR="
    compiling binary_protocol_accelerated.c
    compiling compact_protocol.c
    compiling memory_buffer.c
    compiling protocol.c
    compiling strlcpy.c
    In file included from strlcpy.c:20:
    ./strlcpy.h:28:15: error: expected parameter declarator
    extern size_t strlcpy(char *, const char *, size_t);
                  ^
    /usr/include/secure/_string.h:105:44: note: expanded from macro 'strlcpy'
      __builtin___strlcpy_chk (dest, src, len, __darwin_obsz (dest))
                                               ^
    /usr/include/secure/_common.h:39:62: note: expanded from macro '__darwin_obsz'
    #define __darwin_obsz(object) __builtin_object_size (object, _USE_FORTIFY_LEVEL > 1 ? 1 : 0)
                                                                 ^
    /usr/include/secure/_common.h:30:32: note: expanded from macro '_USE_FORTIFY_LEVEL'
    #    define _USE_FORTIFY_LEVEL 2
                                   ^
    In file included from strlcpy.c:20:
    ./strlcpy.h:28:15: error: expected ')'
    /usr/include/secure/_string.h:105:44: note: expanded from macro 'strlcpy'
      __builtin___strlcpy_chk (dest, src, len, __darwin_obsz (dest))
                                               ^
    /usr/include/secure/_common.h:39:62: note: expanded from macro '__darwin_obsz'
    #define __darwin_obsz(object) __builtin_object_size (object, _USE_FORTIFY_LEVEL > 1 ? 1 : 0)
                                                                 ^
    /usr/include/secure/_common.h:30:32: note: expanded from macro '_USE_FORTIFY_LEVEL'
    #    define _USE_FORTIFY_LEVEL 2
                                   ^
    ./strlcpy.h:28:15: note: to match this '('
    /usr/include/secure/_string.h:105:44: note: expanded from macro 'strlcpy'
      __builtin___strlcpy_chk (dest, src, len, __darwin_obsz (dest))
                                               ^
    /usr/include/secure/_common.h:39:53: note: expanded from macro '__darwin_obsz'
    #define __darwin_obsz(object) __builtin_object_size (object, _USE_FORTIFY_LEVEL > 1 ? 1 : 0)
                                                        ^
    In file included from strlcpy.c:20:
    ./strlcpy.h:28:15: error: type specifier missing, defaults to 'int' [-Werror,-Wimplicit-int]
    extern size_t strlcpy(char *, const char *, size_t);
                  ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    /usr/include/secure/_string.h:105:44: note: expanded from macro 'strlcpy'
      __builtin___strlcpy_chk (dest, src, len, __darwin_obsz (dest))
                                               ^~~~~~~~~~~~~~~~~~~~
    /usr/include/secure/_common.h:39:31: note: expanded from macro '__darwin_obsz'
    #define __darwin_obsz(object) __builtin_object_size (object, _USE_FORTIFY_LEVEL > 1 ? 1 : 0)
                                  ^~~~~~~~~~~~~~~~~~~~~
    In file included from strlcpy.c:20:
    ./strlcpy.h:28:15: error: conflicting types for '__builtin___strlcpy_chk'
    /usr/include/secure/_string.h:105:3: note: expanded from macro 'strlcpy'
      __builtin___strlcpy_chk (dest, src, len, __darwin_obsz (dest))
      ^
    ./strlcpy.h:28:15: note: '__builtin___strlcpy_chk' is a builtin with type 'unsigned long (char *, const char *, unsigned long, unsigned long)'
    /usr/include/secure/_string.h:105:3: note: expanded from macro 'strlcpy'
      __builtin___strlcpy_chk (dest, src, len, __darwin_obsz (dest))
      ^
    4 errors generated.
    make: *** [strlcpy.o] Error 1

    make failed, exit code 2

    Gem files will remain installed in /Library/Ruby/Gems/2.0.0/gems/thrift-0.8.0 for inspection.
    Results logged to /Library/Ruby/Gems/2.0.0/extensions/universal-darwin-14/2.0.0/thrift-0.8.0/gem_make.out

网上找资料发现[Thrift gem 0.9.1 fails to build on OS X Mavericks and Ruby 1.9.3][macruby]好象相关。

从新执行

	sudo gem install cassandra -- --with-cppflags='-D_FORTIFY_SOURCE=0'
	
安装成功!


[macruby]:http://www.extendi.it/blog/2013/12/17/7-thrift-gem-0-9-1-fails-to-build-on-os-x-mavericks-and-ruby-1-9-3
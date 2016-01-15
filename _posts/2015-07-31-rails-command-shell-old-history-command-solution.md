---
layout: post
title: 在命令行下执行rails命令，执行的是历史命令解决方案
categories: [Ruby]
tags: [ruby, Rails]
---

最近Rails项目多次重构，反复在多个分支之间切换。突然发生奇怪现象。
用Rubymine项目打开的项目。
用菜单或者shell中执行的

```ruby
rake db:migrate
```

执行的是很久之前的迁移文件。反复测试多次无效。百思不得其解。

考虑可能原因

1. rvm cache
2. user ruby cache
3. project cache
4. rubymine cache

经过测试，发现1，2，3没有这样问题。最后剩下1个问题就是rubymine的cache导致的。

google一下clear rubymine history得到解决方案。

>On the main menu, choose File | Invalidate Caches/Restart. The Invalidate Caches message appears informing you that the caches will be invalidated and rebuilt on the next start. Use buttons in the dialog to invalidate caches, restart RubyMine or both.

[原文链接](https://www.jetbrains.com/ruby/help/cleaning-system-cache.html)

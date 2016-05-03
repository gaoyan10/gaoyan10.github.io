---
layout: post
title: Nodejs-Error-Cannot-find-module-'unicode/category/So'
categories: [Node学习]
tags: [npm, node, cnpm]
---
### Error: Cannot find module 'unicode/category/So'
前几天用了[keystonejs](http://keystonejs.com/)，一个node开发的不错的CMS框架，在把代码部署到一台新的阿里云机器后，出现了一些问题。

由于npm某些包被GFW墙掉，所以我在阿里云使用了[cnpm](http://npm.taobao.org/)，cnpm安装如下：

```
npm install -g cnpm --registry=https://registry.npm.taobao.org
ln -s /usr/local/node/bin/cnpm /usr/local/bin/cnpm #为cnpm建立软连接
```

使用cnpm安装package.json中的依赖包。日志如下，中间忽略部分module的安装。

```
[debug@~2.2.0] installed at node_modules/.npminstall/debug/2.2.0/debug (0 packages, use 319ms, speed 142.91kB/s, json 33.94kB, tarball 12.79kB)
......
[keystone@^0.3.16] installed at node_modules/.npminstall/keystone/0.3.17/keystone (0 packages, use 26s, speed 1.09MB/s, json 5.9MB, tarball 22.56MB)
excute post install scripts...
[spawn-sync@1.0.15] scripts.postinstall: "node postinstall" at /root/juyou_server_cms/node_modules/.npminstall/spawn-sync/1.0.15/spawn-sync
[spawn-sync@1.0.15] scripts.postinstall success, use 282ms
[node-sass@3.4.2] scripts.install: "node scripts/install.js" at /root/juyou_server_cms/node_modules/.npminstall/node-sass/3.4.2/node-sass
Binary downloaded and installed at /root/juyou_server_cms/node_modules/.npminstall/node-sass/3.4.2/node-sass/vendor/linux-x64-47/binding.node
[node-sass@3.4.2] scripts.install success, use 1s
[node-sass@3.4.2] scripts.postinstall: "node scripts/build.js" at /root/juyou_server_cms/node_modules/.npminstall/node-sass/3.4.2/node-sass
` /root/juyou_server_cms/node_modules/.npminstall/node-sass/3.4.2/node-sass/vendor/linux-x64-47/binding.node ` exists. 
 testing binary.
Binary is fine; exiting.
[node-sass@3.4.2] scripts.postinstall success, use 268ms
[buffertools@2.1.3] scripts.install: "node-gyp rebuild" at /root/juyou_server_cms/node_modules/.npminstall/buffertools/2.1.3/buffertools
sh: node-gyp: command not found
[Error: Run "sh -c node-gyp rebuild" error, exit code 127
    at ChildProcess.<anonymous> (/usr/local/node/lib/node_modules/cnpm/node_modules/runscript/index.js:67:21)
    at emitTwo (events.js:100:13)
    at ChildProcess.emit (events.js:185:7)
    at maybeClose (internal/child_process.js:850:16)
    at Process.ChildProcess._handle.onexit (internal/child_process.js:215:5)@%s] optional error: %s
All packages installed (791 packages installed from npm registry, use 28s, speed 1MB/s, json 1063(5.9MB), tarball 22.56MB)

```

**[buffertools@2.1.3] scripts.install: "node-gyp rebuild" at /root/juyou_server_cms/node_modules/.npminstall/buffertools/2.1.3/buffertools
sh: node-gyp: command not found
[Error: Run "sh -c node-gyp rebuild" error, exit code 127**

悲剧是从这里开始的。buffertools工具需要node-gyp rebuild，但是我环境里并没有安装node-gyp

启动node keystone.js后开始报错
如下

```
module.js:341
    throw err;
    ^

Error: Cannot find module 'unicode/category/So'
    at Function.Module._resolveFilename (module.js:339:15)
    at Function.Module._load (module.js:290:25)
    at Module.require (module.js:367:17)
    at require (internal/module.js:16:19)
```
缺少unicode/category/So，我以为缺少unicode包，继续用cnpm install unicode来安装。结果提示

```
[unicode@*] existed at node_modules/.npminstall/unicode/0.6.1/unicode (0 packages, use 99ms, speed 22.45kB/s, json 2.36kB, tarball 0B)
All packages installed (use 110ms, speed 21.43kB/s, json 1(2.36kB), tarball 0B)
```
但是进入node_module/unicode后发现里面并没有So等文件
使用npm install unicode安装
提示

```
npm WARN skippingAction Module is inside a symlinked module: not running remove babel-runtime@5.8.38 node_modules/keystone/node_modules/babel-core/node_modules/babel-template/node_modules/babel-runtime
.......

```
因为node_modules/.npminstall里已经有了unicode的软连接。

使用cnpm unistall unicode，结果提示如下

```
- unicode@0.6.1 node_modules/unicode
npm WARN enoent ENOENT: no such file or directory, open '/root/juyou_server_cms/node_modules/.npminstall/unicode/0.6.1/unicode/category/package.json'
npm WARN category No description
npm WARN category No repository field.
npm WARN category No README data
npm WARN category No license field.
```
因为没有被正确的安装，所以也没法卸载。

1. 手动删除了node_modules下的unicode，并删除了.npminstall下的所有软链接。
2. cnpm install -g node-gyp，安装node-gyp
3. cnpm install unicode，重新安装unicode模块

这次终于成功的安装了unicode模块，使用nohup node keystone.js &后顺利的启动了keystone.js

---
layout: post
title: Mac OS X下玩街机游戏并开启作弊模式
categories: [games]
tags: [Mac, games, kof97, cheat]
---

最近怀念和大神一起玩拳皇的日子。想来mac下应该也能玩。顺手google找到[MAME OS X][mameosx]

下载最新[MAMEOSX-0.135.dmg][mameosx-0.135]解压安装。	
ps:这里吐槽下， 0.135是2009-11-15号发布的。		

安装完以后顺利打开。找到最拳皇97。打开玩2把。被机器人给虐残了。想想自己平时也没啥其他喜欢的游戏，1个人无聊时候可以练习练手感。还是安装作弊器好了。。怎么说也可以打开单人练习模式，调整调整游戏难度。神马的。		

继续google关键字```mame os x cheat```果然有方案。

<br>

1. 打开[http://www.mamecheat.co.uk](http://www.mamecheat.co.uk)	
	下载最新XML CHeat Collection for MAME，我下的是0.156版本	
2. 解压*cheat.7z*文件		
3. 压缩解压后的*cheat*文件夹		
4. 重命名压缩后的文件为*cheat.dat.zip*文件
5. 把文件移到*~/Library/Application\ Support/MAME\ OS\ X/*目录下	
6. 启动MAME OS X程序
7. 按F6开启/关闭作弊模式，这里选择开启
8. 按tab键，会看到在"video options"和"select new game"中间出现"cheat"选项。选中回车。
9. 开启固定选项
	我这里把"Select Cartridge/NeoGeo Type"切换为"Home (NEOGEO)"选项
10. 启动游戏

附上3张截图
![mame-os-x-cheat][mame-os-x-cheat]
![mame-os-x-kof97-select-model][mame-os-x-kof97-select-model]
![mame-os-x-kof97][mame-os-x-kof97]

最后，吃水不忘打井人。感谢[sourceforge上的解决方案][sourceforge-mame-os-x-cheat]

[mameosx]:http://mameosx.sourceforge.net
[mameosx-0.135]:http://prdownloads.sourceforge.net/mameosx/MAMEOSX-0.135.dmg?download
[mame-os-x-cheat]:http://watsy0007githubio.qiniudn.com/0269287A-966C-4EB0-8333-35469BC672DA.png?imageView/2/w/640/q/90
[mame-os-x-kof97-select-model]:http://watsy0007githubio.qiniudn.com/9F38164C-F99A-4DD0-A495-0C890C875428.png?imageView/2/w/640/q/90
[mame-os-x-kof97]:http://watsy0007githubio.qiniudn.com/911C7132-919E-4673-9769-BAEC5432ADAC.png?imageView/2/w/640/q/90
[sourceforge-mame-os-x-cheat]:http://sourceforge.net/p/mameosx/discussion/608890/thread/a057ed3f/
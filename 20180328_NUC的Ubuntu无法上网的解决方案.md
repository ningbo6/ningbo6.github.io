---
title: NUC的Ubuntu无法上网的解决方案
categories: tools
tags: 
	- NUC
	- Ubuntu
	- Network
comments: true
keywords: NUC, Ubuntu, Network
description: NUC的Linux网卡驱动没有装好，解决NUC的Ubuntu无法上网的方案
date: 2018-03-28 13:00:00
---
（转载请注明作者和出处：https://ningbo6.github.io）
# 查询网卡型号
    lspci
Ethernet controller是有线网卡
Network controller是无线网卡

#有线网卡具体步骤
[参考这个回答](https://askubuntu.com/questions/755652/ethernet-not-working-on-ubuntu-14-04-lts)
1. 下载e1000e  [下载地址](https://downloadcenter.intel.com/download/15817)
2.  把e1000e放到某个文件夹下，例如/home/hit/
3. 解压缩
    cd e1000e-<x.x.x>/src/
    tar zxf e1000e-<x.x.x>.tar.gz
4. cd 到e1000e下的src目录下，进行编译安装
    sudo make install
5. 模块装载
    modprobe e1000e insmod e1000e
6. 测试是否成功

# 无线网卡具体步骤
[参考这个回答](https://askubuntu.com/questions/858546/wifi-not-working-intel-on-hp-spectre-x360-13/859263#859263)

1.下载
http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.8.14/linux-headers-4.8.14-040814-generic_4.8.14-040814.201612101431_amd64.deb
http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.8.14/linux-image-4.8.14-040814-generic_4.8.14-040814.201612101431_amd64.deb
http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.8.14/linux-headers-4.8.14-040814_4.8.14-040814.201612101431_all.deb 
2. 安装
   sudo dpkg -i *.deb 
3. 重启 好了
4. 没好的话
[参考这个回答](https://blog.csdn.net/linzhineng44/article/details/78778143)
可以通过链接https://wireless.wiki.kernel.org/en/users/Drivers/iwlwifi下载相应的驱动。解压缩之后把文件
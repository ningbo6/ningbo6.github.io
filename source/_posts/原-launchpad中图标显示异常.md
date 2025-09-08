---
title: Ubuntu中launchpad中图标显示异常
categories: tools 
tags:
    - launchpad
    - 图标异常
    - ubuntu
comments: true
keywords: luanchpad, 图标异常, icon error, Ubuntu
description: 在launchpad中图标异常，之后修复图标到正常情况
date: 2017-12-28 10:08:16
---
（转载请注明作者和出处：https://ningbo6.github.io）

系统:　Ubuntu14.04

问题描述:Ubuntu的launchpad(开始菜单)中图标显示错误，不能正常显示图标。

解决方案：

打开终端

cd /usr/share/applications/
ls

然后找到你的图标

sudo vim *.desktop

*为你的app的name

    [Desktop Entry]
    Name=Youdao Dict
    Name[zh_CN]=有道词典
    Name[zh_TW]=有道詞典
    Exec=youdao-dict %f
    Icon=/usr/share/youdao-dict/app/assets/youdao-dict.png
    Terminal=false
    X-MultipleArgs=false
    Type=Application
    Encoding=UTF-8
    Categories=Application;Utility;Dictionary;
    StartupNotify=false

把Icon后面的路径改为你的图片的路径

sudo reboot
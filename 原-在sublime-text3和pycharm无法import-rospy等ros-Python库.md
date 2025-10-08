---
title: 在sublime-text3和pycharm无法import-rospy等ros-Python库
categories: tools
tags: 
    - ros
    - gazebo
    - gym
    - python
    - Tensorflow
    - ubuntu
comments: true
keywords: ubuntu, ros, gym, gazebo, Tensorflow, gym-gazebo
description: python连接gazebo/openAI的gym/erlerobot的gym-gazebo/google的tensorflow
date: 2018-01-10 21:58:21
---
（转载请注明作者和出处：https://ningbo6.github.io）
# 背景

笔记本型号：联想Y500  

CPU　:　Intel® Core™ i7-3630QM CPU @ 2.40GHz × 8  

内存：7.7 GiB  

显卡：GeForce GT 750M/PCIe/SSE2  

系统版本：ubuntu 14.04 LTS 64bit 

软件：sublime text 3 ;pycharm

#错误情况

```
import rospy
ImportError: No module named rospy
```
----------

#问题描述
在pycharm和sublime下无法import rospy等模块，但是在Terminal下用命令行就可以正常运行，经过分析，原因为　/opt/ros/indigo/lib/python2.7/dist-packages　下的路径没有被添加到环境中


----------

#解决方案
##sublime text 3
1.Terminal下 cd /opt/sublime_text/Packages
2.sudo gedit Python.sublime-package
3.粘贴

```
{
    "shell_cmd": "python -u \"$file\"",
    "file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
    "selector": "source.python",

    "env": {"PYTHONPATH":"/opt/ros/indigo/lib/python2.7/dist-packages"},

    "variants":
    [
        {
            "name": "Syntax Check",
            "shell_cmd": "python -m py_compile \"${file}\"",
        }
    ]
}
```

４．保存退出
５．接下来，用zip命令删除C++.sublime-package中的Python.sublime-build文件：sudo zip -d Python.sublime-package Python.sublime-build
6.最后将之前的Python.sublime-build添加到此时的Python.sublime-package中：sudo zip -g Python.sublime-package Python.sublime-build
7. 重启sublime text，实验调用默认的Python build system，成功！

#pycharm
1.打开File-->Settings-->Project:XXXX-->Project Interpreter
2.在Project Interpreter : Python 2.7 /usr/bin/python (顶部的位置)点击下拉箭头
3.选择Show ALL...
4.点击右侧第四个按钮：interpreter Paths
5.点击右侧的第一个加号按钮，并在弹出的对话框中粘贴以下路径

```
/opt/ros/indigo/lib/python2.7/dist-packages
```
6.一路ok就可以了
7.然后运行一下，成功!


----------
#sublime 和pycharm的使用感受
sublime实在是太轻便快捷了，用户体验极好，但是功能不够强大
pycharm使用起来功能很多，比如看库的这种操作。但是有时候卡卡的。

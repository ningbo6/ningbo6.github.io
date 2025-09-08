---
title: SLAM with LS01C and hector
categories: ros
tags: 
    - robot
    - SLAM
    - ROS
    - hector
comments: true
keywords: ROS, gazebo, robot, hector, 镭神ls01c
description:   手持镭神LS01C使用hector_mapping方法进行建图实验
date: 2017-12-04 11:23:05
--- 
（转载请注明作者和出处：https://ningbo6.github.io）
我的代码在[ls01c](https://github.com/365220121/ls01c.git)可以拿去下载。 

本博客只是个人学习记录使用，瞎写写，内容比较粗糙。

1.  从镭神客服那里拿到驱动
2.  对驱动进行阅读，并启动节点获取雷达数据实验
在启动激光雷达的过程中可能遇到这样的问题：驱动没问题，launch文件也没问题，但是雷达不旋转，也得不到数据，这是因为激光雷达的端口ttyUSB0(也可能是别的名称，自己查一下吧)没有权限，需要在终端执行以下语句：

cd /dev
sudo chmod 777 ttyUSB


3.  下载hector源码并编译
[hector的github下载地址](https://github.com/tu-darmstadt-ros-pkg/hector_slam.git)
4.  启动launch文件就可以建图了

可能遇到的问题：

1.  手持时，可能因为移动的太快或者转动太快导致地图混乱
2.  当有物体靠近激光雷达或者雷达收到振动时，终端会出现SearchDir angle change too large

* * *

后记：建图混乱的原因致使因为这个雷达的性能不满足这个算法。我回头试试用cartographer和这个雷达试试能否建出好图。

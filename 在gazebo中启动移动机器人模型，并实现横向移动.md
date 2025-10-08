---
title: gazebo中机器人横向移动
categories: ros
tags: 
	- ROS
	- 横向移动
	- 机器人
comments: true
keywords: ROS, 横向移动, 机器人
description: 在gazebo中启动移动机器人模型，并实现横向移动
date: 2017-01-010 13:00:00
---
（转载请注明作者和出处：https://ningbo6.github.io）
本博客只是个人学习记录使用，瞎写写，内容比较粗糙。编写过程中，借鉴了yaked大神的[实战youBot](http://blog.csdn.net/yaked/article/details/51483531)和ROS-WIKI以及其它一些大神的内容。 
现在大部分的移动机器人主要差动轮进行运动，只有linear.x和angular.z两个值，但是我的模型是４个瑞典轮，能提供横向的移动速度，即linear.y。
在gazebo中的横向移动同样要修改控制方式，只不过在与rviz不同，不能直接修改，文件格式打不开，这里采用的方案是直接在xacro中加载一个平面移动的插件就可以了

    <!-- position controller -->
    <gazebo>
      <plugin name="base_controller" filename="libgazebo_ros_planar_move.so">
        <commandTopic>cmd_vel</commandTopic>
        <odometryTopic>odom</odometryTopic>
        <odometryFrame>/odom</odometryFrame>
        <odometryRate>100.0</odometryRate>
        <robotBaseFrame>base_footprint</robotBaseFrame>
        <broadcastTF>true</broadcastTF>
      </plugin>
    </gazebo>`

----------

 1. 模型xacro加载到gazebo
沿用yaked大神博客中的gazebo.launch进行修改，把自己的模型修改到他的launch文件中，就可以了。＇
 
 2. gazebo加入上面的代码
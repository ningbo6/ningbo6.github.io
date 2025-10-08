---
title: rviz闪退的解决方案
categories: ros
tags: 
    - robot
    - rviz
    - ROS
comments: true
keywords: ROS, rviz, robot, 闪退
description: 在机器人上装上了一个miniPC，在上面装上了ros indigo版本，使用远程桌面连接miniPC，然后操作机器人进行建图实验，出现闪退，并提出解决方案。
date: 2017-12-21 21:01:57
---
（转载请注明作者和出处：https://ningbo6.github.io）
背景：项目用到自己做的机器人，在机器人上装上了一个miniPC，在上面装上了ros indigo版本，使用远程桌面连接miniPC，然后操作机器人进行建图实验。

* * *

问题：在打开rviz后闪退

* * *

原因：这是由于rviz在不接显示器的时候，是不能启动的

* * *

解决方法： 

1. 使用zeroconf，通过ssh连接miniPC，进行控制，在自己的电脑上打开rviz，就能订阅miniPC上的topic 

2. 先插上显示器，打开rviz后，然后拔掉VGA或者HDMI.(这个方法是别人告诉我的不知道可不可行)

3. Rendering on a server

If you're trying to render video on a server, you'll need to connect a fake display. The easiest way to do this is by running under xvfb-run (on Ubuntu, install the xvfb package):

```
xvfb-run -s "-screen 0 1400x900x24" bash
```
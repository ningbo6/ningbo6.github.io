---
title: 移动机器人中SW2URDF
categories: ros
tags: 
  - robot
  - sw2urdf
  - ros
commetents: true
keywords: ros, urdf, solidworks, sw2urdf
description: 建立urdf模型，并转化为xacro格式，用在gazebo和rviz中。
date: 2017-10-09 09:46:33
---
（转载请注明作者和出处：https://ningbo6.github.io）
本博客只是个人学习记录使用，第一次写，瞎写写，内容比较粗糙。编写过程中，借鉴了古月居大神的smartcar以及其它一些大神的内容。 

硕士课题中的移动机器人的SolidWorks三维模型转换为ros中的urdf文件的步骤如下：

* * *

准备工作： 

    1. 安装SolidWorks，亲测2014和2016都可以，别的应该也行。 

         可从ROS-WIKI上下载，下载地址为[sw2urdf](https://bitbucket.org/brawner/sw2urdf/raw/cde91b4f1a2f2c768da55471fbec04cb34edd554/INSTALL/Output/sw2urdfSetup.exe) 

    2. 在SW2016中，在工具 - file - export as URDF

* * *

开始工作：

1.  打开模型，并执行export as URDF，稍等片刻可以看到一个选项卡
![初始状态](http://img.blog.csdn.net/20171009092034590?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW5iMTk5MzA0Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
2.  在选项卡中，你会看到link name 和 Global Origin Coordinate System，改不改都可以，我这里使用的是缺省值。Link Component 这里要选中你的小车的底盘部分（小tips：可以框框选中所有的零部件，然后在点击轮子就会反选了，一开始不知道费了很大劲。）Number of child links 这里根据你轮子的个数选择，我使用的是4，因为我有四个轮。设置完成之后如下图所示：
![设置完成](http://img.blog.csdn.net/20171009092128142?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW5iMTk5MzA0Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
3.  接下来点击最下方生成出来的Empty_Link，接下来的界面如下：
![这里写图片描述](http://img.blog.csdn.net/20171009092346570?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW5iMTk5MzA0Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
4.  这里只修改Link name、joint name和Link Components。Link name 和 joint name只修改了名字，Link Components选择了轮子，完成图如下
![这里写图片描述](http://img.blog.csdn.net/20171009092756321?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW5iMTk5MzA0Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
5.  第4步的步骤重复3遍，就可以完成其它3个轮子了。
![这里写图片描述](http://img.blog.csdn.net/20171009092855462?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW5iMTk5MzA0Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

6.  以上步骤都完成之后，如下图所示
![这里写图片描述](http://img.blog.csdn.net/20171009093015299?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW5iMTk5MzA0Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

7.  然后点击 Preview and Export..稍等片刻之后，如下图所示
![这里写图片描述](http://img.blog.csdn.net/20171009093334301?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW5iMTk5MzA0Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](http://img.blog.csdn.net/20171009094000560?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW5iMTk5MzA0Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
在这里可以把Joint Type 选为continous，我也不知道具体有啥用，知道的可以给我说一下。Origin中z轴位置要稍微改一下，因为我的轮子直径是8cm，我填写的为0.04，这样在rviz中显示的时候他就在地面上了。相应的base_link的visual中的z值也要相应的进行调整。
8.  然后点击next，finish，选择好路径，还有名字我这里沿用的古月大神的smartcar_description，不过要把后缀去掉，因为这里生成的是文件夹。
9.  这样就算完成了，放到ros中把代码进行修改，并转化为xacro格式就可以launch起来了。

* * *
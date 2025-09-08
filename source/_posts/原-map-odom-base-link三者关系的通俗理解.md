---
title: map odom base_link三者关系的通俗理解
categories: ros
tags: 
    - ros
    - transform
    - links
comments: true
keywords: ros, tf, map, odom, base_link
description: 这篇博客用通俗易懂的方法详细讲解了map odom base_link这三者的关系。
date: 2017-12-22 20:38:01
---
（转载请注明作者和出处：https://ningbo6.github.io）

    为了更好的集成和重用软件模块，驱动，模型以及库的开发人员需要一个坐标系的共享公约。该坐标系的共享规约让开发者为移动基座创建驱动和模型提供了一个规范。同样，开发人员用与本规范兼容的一些移动机器人基座软件能很容易的创建相应的库和应用。
    `</pre>

    * * *

1.  世界坐标(map)    
    该map坐标系是一个世界固定坐标系，其Z轴指向上方。相对于map坐标系的移动平台的姿态，不应该随时间显著移动。map坐标是不连续的，这意味着在map坐标系中移动平台的姿态可以随时发生离散的跳变。    
    典型的设置中，定位模块基于传感器的监测，不断的重新计算世界坐标中机器人的位姿，从而消除偏差，但是当新的传感器信息到达时可能会跳变。    
    map坐标系作为长期的全局参考是很有用的，但是跳变使得对于本地传感和执行器来说，其实是一个不好的参考坐标。
2.  里程计坐标系(odom)    
     odom 坐标系是一个世界固定坐标系。在odom 坐标系中移动平台的位姿可以任意移动，没有任何界限。这种移动使得odom 坐标系不能作为长期的全局参考。然而，在odom 坐标系中的机器人的姿态能够保证是连续的，这意味着在odom 坐标系中的移动平台的姿态总是平滑变化，没有跳变。    
    在一个典型设置中，odom 坐标系是基于测距源来计算的，如车轮里程计，视觉里程计或惯性测量单元。    
    odom 坐标系作为一种精确，作为短期的本地参考是很有用的，但偏移使得它不能作为长期参考
3.  基座标(base_link)    
    该base_link坐标刚性地连接到移动机器人基座。base_link可以安装在基座中的任意方位；对于每个硬件平台，在基座上的不同地方都会提供一个明显的参考点。

    * * *

    坐标之间的关系

    <pre>`在机器人系统中，我们使用一棵树来来关联所有坐标系，因此每个坐标系都有一个父坐标系和任意子坐标系，如下：

    map --&gt; odom --&gt; base_link

    世界坐标系是odom坐标系的父，odom坐标系是base_link的父。虽然直观来说，map和odom应连接到base_link，这是不允许的，因为每坐标系只能有一个父类。
    `</pre>

    * * *

    坐标系权限

    <pre>`odom到base_link的转换是由里程计源计算和发布的。然而，定位模块不发布map到base_link的转换(transform)。相反，定位模块先接收odom到base_link的 transform，并使用这个信息发布map到odom的transform。

* * *

通俗理解 

odom和map坐标系在机器人运动开始是重合的。但是，随着时间的推移是不重合的，而出现的偏差就是里程计的累积误差。那map–&gt;odom的tf就是在一些校正传感器合作校正的package比如gmapping会给出一个位置估计（localization），这可以得到map–&gt;base_link的tf，所以估计位置和里程计位置的偏差也就是odom与map的坐标系偏差。所以，如果你的odom计算没有错误，那么map–&gt;odom的tf就是0\. 

![这里写图片描述](http://img.blog.csdn.net/20171222203520897?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW5iMTk5MzA0Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/NorthWest)

---
title: python连接gazebo/openAI的gym/erlerobot的gym-gazebo/google的tensorflow
categories: 
    - reinforcement learning
tags: 
    - ros
    - gazebo
    - gym
    - python
    - Tensorflow
    - ubuntu
comments: true
keywords: ubuntu, ros, gym, gazebo, Tensorflow，gym-gazebo
description: python连接gazebo/openAI的gym/erlerobot的gym-gazebo/google的tensorflow
date: 2018-01-10 23:15:48
---
（转载请注明作者和出处：https://ningbo6.github.io）
笔记本型号：联想Y500 
CPU　:　Intel® Core™ i7-3630QM CPU @ 2.40GHz × 8 
内存：7.7 GiB 
显卡：GeForce GT 750M/PCIe/SSE2 
系统版本：ubuntu 14.04 LTS 64bit
ROS版本：indigo
gazebo版本：gazebo7
tensorflow：1.4.1-gpu
python：2.7


----------
中间一些令我吐血的地方：
1.必须python2.7，不然要解决的问题太多了，我尝试了好久，边擦干净屏幕上的血，边把所有的东西改为了python2.7
2.百度不是解决问题的方法，谷歌才是，科学上网才是第一生产力
3.遇到无法import rospy等ros的包的问题，请移步我的另一篇博客。
[在sublime text3和pycharm无法import rospy等ros Python库](http://blog.csdn.net/ynb19930428/article/details/79028807)
4.中间用到的一些工具，请移步我的另一篇博客
[ubuntu上我的装机必备小工具](ubuntu%E4%B8%8A%E6%88%91%E7%9A%84%E8%A3%85%E6%9C%BA%E5%BF%85%E5%A4%87%E5%B0%8F%E5%B7%A5%E5%85%B7)


----------
#安装步骤
##1.先安装gazebo7
我用的是新立得软件包管理装的，很方便，当然你可以选择别的方式
##2.安装ros
请移步ROSwiki
[roswiki](http://wiki.ros.org/cn/indigo/Installation/Ubuntu)
注意：这里必须安装ros-indigo-desktop版本 不要装full
##3.安装gazebo7和ros的control package
在新立得软件包管理一搜就有了，关键词为：ros-indigo-gazebo7-ros 
把其中的都装了就好了
##4.安装gym
[openAI-gym的github](https://github.com/openai/gym)
按照github中的步骤来就好
首先装依赖

```
apt-get install -y python-numpy python-dev cmake zlib1g-dev libjpeg-dev xvfb libav-tools xorg-dev python-opengl libboost-all-dev libsdl2-dev swig
```

注意git clone之后要装all版本
`git clone https://github.com/openai/gym.git
cd gym
pip install -e '.[all]'`
在他的安装说明提到了Rendering on a server，这个是我之前一直在找的东西。可以在你不查屏幕的时候运行gazebo和rviz，这在我之前的博文中提到了
Rendering on a server
If you're trying to render video on a server, you'll need to connect a fake display. The easiest way to do this is by running under xvfb-run (on Ubuntu, install the xvfb package):

```
xvfb-run -s "-screen 0 1400x900x24" bash
```
##5.gym-gazebo
这个很难装，很繁琐，现在我还是比较懵逼的。不过我总结了一下经。
先去看一下他的安装文档，然后不要着急。我们之前的有些已经装过了，那我们采用step-by-step的方式，安装过程中要看着setup.bash来，把不用的步骤省去
参照此人的博客：[理解gym-gazebo](http://blog.csdn.net/zhangdadadawei/article/details/78956156)
注意：
    1.安装过程中可以打开gazebo.repos把系统中已经有的可以屏蔽掉。
    2.如果CPU性能不是特别好，不建议那么多线程，make -j 1就好，慢点就慢点吧，不然会编译冲突错误。
    3.路径的设置要根据自己安装的路径来。
##6.tensorflow
因为ros不兼容python3，所以我只能把原来的Python3的tensorflow废弃掉了。
我编译安装tensorflow没成功，这里，我用pip安装的。
安装过程中，不知道是网还是我的电脑的问题很多包在pip的时候会下载不下来，导致安装失败，我的应对方法是：从python官网把需要安装的whl包最新版下载下来用pip安装。

##pip安装whl的方法如下：
1.cd到whl包所在的目录
2.pip install xxxx.whl（也可以加sudo）




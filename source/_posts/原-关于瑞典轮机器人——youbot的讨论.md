---
title: 关于瑞典轮机器人——youbot的讨论
categories: ros
tags: 
    - ROS
    - 横向移动
    - 机器人
    - youbot
comments: true
keywords: ROS, 横向移动, 机器人, youbot
description: 这篇博客讨论的如何在gazebo中实现横向移动。
date: 2017-10-17 11:29:43
---
（转载请注明作者和出处：https://ningbo6.github.io）
一个老外给出了一个观点：把他当做平面移动处理，比较贴合我的想法，整体的运动与轮无关，这样复杂的问题简单化。 

他的原话： 

Just a thought, for this robot it may be easier to model the motion of the entire robot instead of modeling the individual wheel. One path could be to use a plugin that accepts twist messages and applies them to the robot base. An example of this is the Planar Move Plugin.

In two dimension (i.e. stage) this might have been called the holonomic plugin. 

网址为： 

[How to simulate a mecanum wheel in Gazebo](http://answers.gazebosim.org/question/4078/how-to-simulate-a-mecanum-wheel-in-gazebo/)

* * *

也有人提出这样的观点：通过添加一个转动关节轴进行角度改变来模拟瑞典轮 

原话如下： 

new to all this but thought I could simulate a mecanum wheel by adding a rotational joint about z-axis for each wheel that is restricted to 0, 45 and 90 degrees. this way the base can go forward/backward/45 degree forward/backward and left/right, sideway left/right and rotate left/right. the simulated moves would need to translate into rotational direction sets for the physical movements of the 4 wheel motors? however - how can I have a restricted set of angels for a joint and what needs to be in place to translate it into the physical wheel rotation command? of course the simulation would only provide a logical wheel view and it will also need a special collision handling as the wheels do not really rotate on the z-axis. but in total an easy representation of the possible moves? 

网址如下： 

[Mecanum wheels ok in one direction not the other in Gazebo](http://answers.gazebosim.org/question/7084/mecanum-wheels-ok-in-one-direction-not-the-other-in-gazebo/)

* * *

还有一个人提出消除掉轮与地面之间的摩擦，然后按照twist来结算每个轮的速度，只是让轮旋转，机器人的移动依赖于twist。而不依赖于每个轮的旋转。我觉得这个思路很好。 

原话：

> It’s a really interesting simulation issue.

First of all, let’s a look at the wheels. They are not standard wheels, they are mecanum wheels. This kind of wheels help the robot to move and rotate easily in any directions, even in congested area. The movement come from the resulting rotation speeds of the wheels, which are independent. I have found I nice explanation of the relationship between the wheels rotation and the resulting movement on this lego page. On the videos, the youBot looks like gliding on the ground.

From a design point of view, this is convenient, because it is similar to a planar joint + a revolute joint between the base and the ground. Very easy to control.

From a simulation point of view, it is a nightmare.

I doubt that the repo from youBot or WPI-RAIL did the job, because they involve casters. And there is no casters on the real KUKA youBot. If you have a look at their teleop program, you will see that this program align the wheels before the rotation, which is not the design of a mecanum wheel at all. With the mesh of the wheels, you will see it. This not natural (this is also why the wheels solid shape is a sphere). And even worst, the the rotation is therefore not supported. So for me, this model is really poor.

I raised the question on Gazebo answers. Someone suggested to use this planar controller. I considered it as a second choice, but finally, this is very close to the behavior of the real mecanum wheels, as you define the x/y velocity and rotation, which can be directly linked to the wheel individual speeds. I have remove the friction between the wheels and the ground.

On the real robot, this x/y speed and rotation translation to individual wheels speeds would also be very convenient.

A visually better solution would be to add the mesh of the wheels, and rotate them according to the planar messages. No time to work on this.

Finally, if you are looking for a very physical solution, I have found this link, where it has been done in another simulator, by mixing kinematics and dynamics solutions.

That would be really great to have more realistic solution. For my requirement, the planar controller is enough.

网址： 

[Move the youbot using its wheels](https://github.com/micpalmia/youbot_ros_tools/issues/5)

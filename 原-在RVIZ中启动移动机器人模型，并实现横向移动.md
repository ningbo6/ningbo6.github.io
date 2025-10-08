---
title: 在RVIZ中启动移动机器人模型，并实现横向移动
categories: ros
tags: 
  - ros
  - 横向移动
  - 机器人
  - rviz
comments: true
keywords: ROS, 横向移动, 机器人, rviz, gazebo
description: 在gazebo中启动移动机器人模型，并实现横向移动
date: 2017-10-09 15:59:54
---
（转载请注明作者和出处：https://ningbo6.github.io）
本博客只是个人学习记录使用，瞎写写，内容比较粗糙。编写过程中，借鉴了古月居大神的smartcar和ROS-WIKI以及其它一些大神的内容。  

引入古月居大神的链接[使用smartcar进行仿真](http://www.guyuehome.com/248)和[smartcar源码上传](http://www.guyuehome.com/251)

## 下面我针对自己的机器人编写的代码和文件。

launch文件

```
<launch>  

    <param name="/use_sim_time" value="false" />  

 

    <!-- Load the URDF/Xacro model of our robot -->  

    <arg name="urdf_file" default="$(find xacro)/xacro.py '$(find smartcar_description)/urdf/smartcar.urdf.xacro'" />  

    <arg name="gui" default="true" />  
    <param name="robot_description" command="$(arg urdf_file)" />  
    <param name="use_gui" value="$(arg gui)"/>  
    <node name="arbotix" pkg="arbotix_python" type="arbotix_driver" output="screen">  
        <rosparam file="$(find smartcar_description)/config/smartcar_arbotix.yaml" command="load" /> 
        <param name="sim" value="true"/>  
    </node>  

    <node name="joint_state_publisher" pkg="joint_state_publisher" type="joint_state_publisher" >  

    </node>  

    <node name="robot_state_publisher" pkg="robot_state_publisher" type="state_publisher">  

        <param name="publish_frequency" type="double" value="20.0" />  

    </node>  

 

     <!-- We need a static transforms for the wheels -->  

     <node pkg="tf" type="static_transform_publisher" name="odom_left_wheel_broadcaster" args="0 0 0 0 0 0 /base_link /left_front_link 100" />  

     <node pkg="tf" type="static_transform_publisher" name="odom_right_wheel_broadcaster" args="0 0 0 0 0 0 /base_link /right_front_link 100" />  
 

    <node name="rviz" pkg="rviz" type="rviz" args="-d $(find smartcar_description)/urdf.rviz" />  

</launch>
```


----------
xacro文件包括三部分：

 1. smartcar_body
 

```
<?xml version="1.0"?>  
<robot name="smartcar" xmlns:xacro="http://ros.org/wiki/xacro">  
  <xacro:property name="M_PI" value="3.14159"/>    
  <!-- Macro for SmartCar body. Including Gazebo extensions, but does not include Kinect -->  
  <xacro:include filename="$(find smartcar_description)/urdf/gazebo.urdf.xacro"/>  
  <xacro:property name="base_x" value="0.33" />  
  <xacro:property name="base_y" value="0.33" />  
  <xacro:macro name="smartcar_body">  
    <link name="base_footprint">
      <inertial>
        <mass value="0.0001" />
        <origin xyz="0 0 0" />
        <inertia ixx="0.0001" ixy="0.0" ixz="0.0"
            iyy="0.0001" iyz="0.0" 
            izz="0.0001" />
      </inertial>
      <visual>
          <origin xyz="0 0 0" rpy="0 0 0" />
          <geometry>
              <box size="0.001 0.001 0.001" />
          </geometry>
      </visual>
    </link>


    <joint name="base_footprint_joint" type="fixed">
      <origin xyz="0 0 0" rpy="0 0 0" />
      <parent link="base_footprint"/>
      <child link="base_link" />
    </joint>


    <link
    name="base_link">
    <inertial>
      <origin
        xyz="0.0032273 -0.060593 1.3595E-05"
        rpy="0 0 0" />
      <mass
        value="4.2623" />
      <inertia
        ixx="0.04568"
        ixy="-0.00094545"
        ixz="-8.9814E-06"
        iyy="0.11402"
        iyz="5.3399E-06"
        izz="0.11035" />
    </inertial>
    <visual>
      <origin
        xyz="0 0 0.23"
        rpy="0 0 0" />
      <geometry>
        <mesh
          filename="package://smartcar_description/meshes/base_link.STL" />
      </geometry>
      <material
        name="">
        <color
          rgba="1 1 1 1" />
      </material>
    </visual>
    <collision>
      <origin
        xyz="0 0 0.23"
        rpy="0 0 0" />
      <geometry>
        <mesh
          filename="package://smartcar_description/meshes/base_link.STL" />
      </geometry>
    </collision>
  </link>
  <link
    name="left_front_wheel">
    <inertial>
      <origin
        xyz="0 -1.98217916108356E-05 0"
        rpy="0 0 0" />
      <mass
        value="0.176438810476987" />
      <inertia
        ixx="8.8965933887671E-05"
        ixy="-1.27636183461175E-23"
        ixz="8.0831821149659E-23"
        iyy="8.89898462049031E-05"
        iyz="2.16519665002917E-23"
        izz="0.000130905430632044" />
    </inertial>
    <visual>
      <origin
        xyz="0 0 0"
        rpy="0 0 0" />
      <geometry>
        <mesh
          filename="package://smartcar_description/meshes/left_front_wheel.STL" />
      </geometry>
      <material
        name="">
        <color
          rgba="1 1 0 1" />
      </material>
    </visual>
    <collision>
      <origin
        xyz="0 0 0"
        rpy="0 0 0" />
      <geometry>
        <mesh
          filename="package://smartcar_description/meshes/left_front_wheel.STL" />
      </geometry>
    </collision>
  </link>
  <joint
    name="left_front_wheel_joint"
    type="continuous">
    <origin
      xyz="0.12875 0.1504 0.04"
      rpy="1.5708 0 0" />
    <parent
      link="base_link" />
    <child
      link="left_front_wheel" />
    <axis
      xyz="0 0 1" />
  </joint>
  <link
    name="right_front_wheel">
    <inertial>
      <origin
        xyz="0 -1.98217916108079E-05 0"
        rpy="0 0 0" />
      <mass
        value="0.176438810476987" />
      <inertia
        ixx="8.8965933887671E-05"
        ixy="-1.27636183461175E-23"
        ixz="8.08318211496588E-23"
        iyy="8.89898462049032E-05"
        iyz="2.18596653030412E-23"
        izz="0.000130905430632044" />
    </inertial>
    <visual>
      <origin
        xyz="0 0 0"
        rpy="0 0 0" />
      <geometry>
        <mesh
          filename="package://smartcar_description/meshes/right_front_wheel.STL" />
      </geometry>
      <material
        name="">
        <color
          rgba="1 1 0 1" />
      </material>
    </visual>
    <collision>
      <origin
        xyz="0 0 0"
        rpy="0 0 0" />
      <geometry>
        <mesh
          filename="package://smartcar_description/meshes/right_front_wheel.STL" />
      </geometry>
    </collision>
  </link>
  <joint
    name="right_front_wheel_joint"
    type="continuous">
    <origin
      xyz="0.12875 -0.1504 0.04"
      rpy="1.5708 0 0" />
    <parent
      link="base_link" />
    <child
      link="right_front_wheel" />
    <axis
      xyz="0 0 1" />
  </joint>
  <link
    name="left_back_wheel">
    <inertial>
      <origin
        xyz="0 -1.98217916108356E-05 0"
        rpy="0 0 0" />
      <mass
        value="0.176438810476987" />
      <inertia
        ixx="8.8965933887671E-05"
        ixy="-1.27300145019925E-23"
        ixz="8.0831821149659E-23"
        iyy="8.89898462049032E-05"
        iyz="2.16519665002917E-23"
        izz="0.000130905430632044" />
    </inertial>
    <visual>
      <origin
        xyz="0 0 0"
        rpy="0 0 0" />
      <geometry>
        <mesh
          filename="package://smartcar_description/meshes/left_back_wheel.STL" />
      </geometry>
      <material
        name="">
        <color
          rgba="1 1 0 1" />
      </material>
    </visual>
    <collision>
      <origin
        xyz="0 0 0"
        rpy="0 0 0" />
      <geometry>
        <mesh
          filename="package://smartcar_description/meshes/left_back_wheel.STL" />
      </geometry>
    </collision>
  </link>
  <joint
    name="left_back_wheel_joint"
    type="continuous">
    <origin
      xyz="-0.12875 0.1504 0.04"
      rpy="1.5708 0 0" />
    <parent
      link="base_link" />
    <child
      link="left_back_wheel" />
    <axis
      xyz="0 0 1" />
  </joint>
  <link
    name="right_back_wheel">
    <inertial>
      <origin
        xyz="2.77555756156289E-17 -1.98217916108079E-05 0"
        rpy="0 0 0" />
      <mass
        value="0.176438810476987" />
      <inertia
        ixx="8.8965933887671E-05"
        ixy="-4.17259369791981E-24"
        ixz="2.75455648073554E-21"
        iyy="8.89898462049032E-05"
        iyz="5.21854052435048E-20"
        izz="0.000130905430632044" />
    </inertial>
    <visual>
      <origin
        xyz="0 0 0"
        rpy="0 0 0" />
      <geometry>
        <mesh
          filename="package://smartcar_description/meshes/right_back_wheel.STL" />
      </geometry>
      <material
        name="">
        <color
          rgba="1 1 0 1" />
      </material>
    </visual>
    <collision>
      <origin
        xyz="0 0 0"
        rpy="0 0 0" />
      <geometry>
        <mesh
          filename="package://smartcar_description/meshes/right_back_wheel.STL" />
      </geometry>
    </collision>
  </link>
  <joint
    name="right_back_wheel_joint"
    type="continuous">
    <origin
      xyz="-0.12875 -0.1504 0.04"
      rpy="1.5708 0 3.1416" />
    <parent
      link="base_link" />
    <child
      link="right_back_wheel" />
    <axis
      xyz="0 0 1" />
  </joint>

  </xacro:macro>  

 

</robot>
```

2. gazebo.urdf.xacro

```
<?xml version="1.0"?>  
<robot xmlns:controller="http://playerstage.sourceforge.net/gazebo/xmlschema/#controller"   

    xmlns:interface="http://playerstage.sourceforge.net/gazebo/xmlschema/#interface"   

    xmlns:sensor="http://playerstage.sourceforge.net/gazebo/xmlschema/#sensor"   

    xmlns:xacro="http://ros.org/wiki/xacro"   

    name="smartcar_gazebo">  

 <!-- ASUS Xtion PRO camera for simulation -->  

<!-- gazebo_ros_wge100 plugin is in kt2_gazebo_plugins package -->  

<xacro:macro name="smartcar_sim">  


    <gazebo reference="base_footprint">
       <material>Gazebo/Blue</material>  
       <turnGravityOff>false</turnGravityOff>
    </gazebo>

    <gazebo reference="base_link">  

        <material>Gazebo/Blue</material>  

    </gazebo>  

 

    <gazebo reference="right_front_wheel">  

        <material>Gazebo/FlatBlack</material>  

    </gazebo>  

    <transmission name="right_front_wheel_joint_trans">
      <type>transmission_interface/SimpleTransmission</type>
      <joint name="right_front_wheel_joint" />
      <actuator name="right_front_wheel_joint_motor">
        <hardwareInterface>EffortJointInterface</hardwareInterface>
        <mechanicalReduction>1</mechanicalReduction>
      </actuator>
    </transmission>
 

    <gazebo reference="right_back_wheel">  

        <material>Gazebo/FlatBlack</material>  

    </gazebo>  

   
    <transmission name="right_back_wheel_joint_trans">
      <type>transmission_interface/SimpleTransmission</type>
      <joint name="right_back_wheel_joint" />
      <actuator name="right_back_wheel_joint_motor">
        <hardwareInterface>EffortJointInterface</hardwareInterface>
        <mechanicalReduction>1</mechanicalReduction>
      </actuator>
    </transmission>
 

    <gazebo reference="left_front_wheel">  

        <material>Gazebo/FlatBlack</material>  

    </gazebo>  
  
    <transmission name="left_front_wheel_joint_trans">
      <type>transmission_interface/SimpleTransmission</type>
      <joint name="left_front_wheel_joint" />
      <actuator name="left_front_wheel_joint_motor">
        <hardwareInterface>EffortJointInterface</hardwareInterface>
        <mechanicalReduction>1</mechanicalReduction>
      </actuator>
    </transmission>
 

    <gazebo reference="left_back_wheel">  

        <material>Gazebo/FlatBlack</material>  

    </gazebo> 

    <transmission name="left_back_wheel_joint_trans">
      <type>transmission_interface/SimpleTransmission</type>
      <joint name="left_back_wheel_joint" />
      <actuator name="left_back_wheel_joint_motor">
        <hardwareInterface>EffortJointInterface</hardwareInterface>
        <mechanicalReduction>1</mechanicalReduction>
      </actuator>
    </transmission>
 
    <!-- position controller -->
    <gazebo>
      <plugin name="base_controller" filename="libgazebo_ros_planar_move.so">
        <commandTopic>cmd_vel</commandTopic>
        <odometryTopic>odom</odometryTopic>
        <odometryFrame>odom</odometryFrame>
        <odometryRate>100.0</odometryRate>
        <robotBaseFrame>base_footprint</robotBaseFrame>
      </plugin>
    </gazebo>



</xacro:macro>  

 

</robot>
```

3. smartcar.urdf.xacro

```
<?xml version="1.0"?>  
<robot name="smartcar"    

    xmlns:xi="http://www.w3.org/2001/XInclude"  

    xmlns:gazebo="http://playerstage.sourceforge.net/gazebo/xmlschema/#gz"  

    xmlns:model="http://playerstage.sourceforge.net/gazebo/xmlschema/#model"  

    xmlns:sensor="http://playerstage.sourceforge.net/gazebo/xmlschema/#sensor"  

    xmlns:body="http://playerstage.sourceforge.net/gazebo/xmlschema/#body"  

    xmlns:geom="http://playerstage.sourceforge.net/gazebo/xmlschema/#geom"  

    xmlns:joint="http://playerstage.sourceforge.net/gazebo/xmlschema/#joint"  

    xmlns:controller="http://playerstage.sourceforge.net/gazebo/xmlschema/#controller"  

    xmlns:interface="http://playerstage.sourceforge.net/gazebo/xmlschema/#interface"  

    xmlns:rendering="http://playerstage.sourceforge.net/gazebo/xmlschema/#rendering"  

    xmlns:renderable="http://playerstage.sourceforge.net/gazebo/xmlschema/#renderable"  

    xmlns:physics="http://playerstage.sourceforge.net/gazebo/xmlschema/#physics"  

    xmlns:xacro="http://ros.org/wiki/xacro">  

 

  <xacro:include filename="$(find smartcar_description)/urdf/smartcar_body.urdf.xacro" />  

 

  <!-- Body of SmartCar, with plates, standoffs and Create (including sim sensors) -->  

  <smartcar_body/>  

 

  <smartcar_sim/>  
</robot>
```


----------
然后按照古月大神的方法启动并控制就可以了。下面我要说的就是我比较独特的部分了。
现在大部分的移动机器人主要差动轮进行运动，只有linear.x和angular.z两个值，但是我的模型是４个瑞典轮，能提供横向的移动速度，即linear.y。这里对diff_controller进行修改让他能够接受linear.y。
diff_controller.py在/opt/ros/indigo/lib/python2.7/dist-packages下。（可以用sudo vim进行修改），修改都已经注释过了（add by yang）。代码如下：

```
#!/usr/bin/env python

"""
  diff_controller.py - controller for a differential drive
  Copyright (c) 2010-2011 Vanadium Labs LLC.  All right reserved.

  Redistribution and use in source and binary forms, with or without
  modification, are permitted provided that the following conditions are met:
      * Redistributions of source code must retain the above copyright
        notice, this list of conditions and the following disclaimer.
      * Redistributions in binary form must reproduce the above copyright
        notice, this list of conditions and the following disclaimer in the
        documentation and/or other materials provided with the distribution.
      * Neither the name of Vanadium Labs LLC nor the names of its 
        contributors may be used to endorse or promote products derived 
        from this software without specific prior written permission.
  
  THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
  ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
  WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
  DISCLAIMED. IN NO EVENT SHALL VANADIUM LABS BE LIABLE FOR ANY DIRECT, INDIRECT,
  INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
  LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA,
  OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF
  LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE
  OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF
  ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
"""

import rospy

from math import sin,cos,pi

from geometry_msgs.msg import Quaternion
from geometry_msgs.msg import Twist
from nav_msgs.msg import Odometry
from diagnostic_msgs.msg import *
from tf.broadcaster import TransformBroadcaster

from ax12 import *
from controllers import *
from struct import unpack

class DiffController(Controller):
    """ Controller to handle movement & odometry feedback for a differential 
            drive mobile base. """
    def __init__(self, device, name):
        Controller.__init__(self, device, name)
        self.pause = True
        self.last_cmd = rospy.Time.now()

        # parameters: rates and geometry
        self.rate = rospy.get_param('~controllers/'+name+'/rate',10.0)
        self.timeout = rospy.get_param('~controllers/'+name+'/timeout',1.0)
        self.t_delta = rospy.Duration(1.0/self.rate)
        self.t_next = rospy.Time.now() + self.t_delta
        self.ticks_meter = float(rospy.get_param('~controllers/'+name+'/ticks_meter'))
        self.base_width = float(rospy.get_param('~controllers/'+name+'/base_width'))

        self.base_frame_id = rospy.get_param('~controllers/'+name+'/base_frame_id', 'base_link')
        self.odom_frame_id = rospy.get_param('~controllers/'+name+'/odom_frame_id', 'odom')

        # parameters: PID
        self.Kp = rospy.get_param('~controllers/'+name+'/Kp', 5)
        self.Kd = rospy.get_param('~controllers/'+name+'/Kd', 1)
        self.Ki = rospy.get_param('~controllers/'+name+'/Ki', 0)
        self.Ko = rospy.get_param('~controllers/'+name+'/Ko', 50)

        # parameters: acceleration
        self.accel_limit = rospy.get_param('~controllers/'+name+'/accel_limit', 0.1)
        self.max_accel = int(self.accel_limit*self.ticks_meter/self.rate)

        # output for joint states publisher
        self.joint_names = ["base_l_wheel_joint","base_r_wheel_joint"]
        self.joint_positions = [0,0]
        self.joint_velocities = [0,0]

        # internal data            
        self.v_left = 0                 # current setpoint velocity
        self.v_right = 0
        self.v_des_left = 0             # cmd_vel setpoint
        self.v_des_right = 0
        self.enc_left = None            # encoder readings
        self.enc_right = None
        self.x = 0                      # position in xy plane
        self.y = 0
        self.th = 0
        self.dx = 0                     # speeds in x/rotation
        self.dy = 0                     # add by yang
  self.dr = 0
        self.then = rospy.Time.now()    # time for determining dx/dy

        # subscriptions
        rospy.Subscriber("cmd_vel", Twist, self.cmdVelCb)
        self.odomPub = rospy.Publisher("odom", Odometry, queue_size=5)
        self.odomBroadcaster = TransformBroadcaster()
    
        rospy.loginfo("Started yang DiffController ("+name+"). Geometry: " + str(self.base_width) + "m wide, " + str(self.ticks_meter) + " ticks/m.")

    def startup(self):
        if not self.fake:
            self.setup(self.Kp,self.Kd,self.Ki,self.Ko) 
    
    def update(self):
        now = rospy.Time.now()
        if now > self.t_next:
            elapsed = now - self.then
            self.then = now
            elapsed = elapsed.to_sec()

            if self.fake:
                x = cos(self.th)*self.dx*elapsed
                y = -sin(self.th)*self.dx*elapsed
    #self.x and self.y has changed for the effort of dy
                self.x += cos(self.th)*self.dx*elapsed + sin(self.th)*self.dy*elapsed
                self.y += sin(self.th)*self.dx*elapsed + cos(self.th)*self.dy*elapsed
                self.th += self.dr*elapsed
            else:
                # read encoders
                try:
                    left, right = self.status()
                except Exception as e:
                    rospy.logerr("Could not update encoders: " + str(e))
                    return
                rospy.logdebug("Encoders: " + str(left) +","+ str(right))

                # calculate odometry
                if self.enc_left == None:
                    d_left = 0
                    d_right = 0
                else:
                    d_left = (left - self.enc_left)/self.ticks_meter
                    d_right = (right - self.enc_right)/self.ticks_meter
                self.enc_left = left
                self.enc_right = right

                d = (d_left+d_right)/2
                th = (d_right-d_left)/self.base_width
                self.dx = d / elapsed
                self.dr = th / elapsed

                if (d != 0):
                    x = cos(th)*d
                    y = -sin(th)*d
                    self.x = self.x + (cos(self.th)*x - sin(self.th)*y)
                    self.y = self.y + (sin(self.th)*x + cos(self.th)*y)
                if (th != 0):
                    self.th = self.th + th

            # publish or perish
            quaternion = Quaternion()
            quaternion.x = 0.0 
            quaternion.y = 0.0
            quaternion.z = sin(self.th/2)
            quaternion.w = cos(self.th/2)
            self.odomBroadcaster.sendTransform(
                (self.x, self.y, 0), 
                (quaternion.x, quaternion.y, quaternion.z, quaternion.w),
                rospy.Time.now(),
                self.base_frame_id,
                self.odom_frame_id
                )

            odom = Odometry()
            odom.header.stamp = now
            odom.header.frame_id = self.odom_frame_id
            odom.pose.pose.position.x = self.x
            odom.pose.pose.position.y = self.y
            odom.pose.pose.position.z = 0
            odom.pose.pose.orientation = quaternion
            odom.child_frame_id = self.base_frame_id
            odom.twist.twist.linear.x = self.dx
            odom.twist.twist.linear.y = self.dy  #it was 0 before changed
            odom.twist.twist.angular.z = self.dr
            self.odomPub.publish(odom)

            if now > (self.last_cmd + rospy.Duration(self.timeout)):
                self.v_des_left = 0
                self.v_des_right = 0

            # update motors
            if not self.fake:
                if self.v_left < self.v_des_left:
                    self.v_left += self.max_accel
                    if self.v_left > self.v_des_left:
                        self.v_left = self.v_des_left
                else:
                    self.v_left -= self.max_accel
                    if self.v_left < self.v_des_left:
                        self.v_left = self.v_des_left
                
                if self.v_right < self.v_des_right:
                    self.v_right += self.max_accel
                    if self.v_right > self.v_des_right:
                        self.v_right = self.v_des_right
                else:
                    self.v_right -= self.max_accel
                    if self.v_right < self.v_des_right:
                        self.v_right = self.v_des_right
                self.write(self.v_left, self.v_right)

            self.t_next = now + self.t_delta
 
    def shutdown(self):
        if not self.fake:
            self.write(0,0)

    def cmdVelCb(self,req):
        """ Handle movement requests. """
        self.last_cmd = rospy.Time.now()
        if self.fake:
            self.dx = req.linear.x        # m/s
            self.dy = req.linear.y        # m/s add by yang
            self.dr = req.angular.z       # rad/s
        else:
            # set motor speeds in ticks per 1/30s
            self.v_des_left = int( ((req.linear.x - (req.angular.z * self.base_width/2.0)) * self.ticks_meter) / 30.0)
            self.v_des_right = int( ((req.linear.x + (req.angular.z * self.base_width/2.0)) * self.ticks_meter) / 30.0)

    def getDiagnostics(self):
        """ Get a diagnostics status. """
        msg = DiagnosticStatus()
        msg.name = self.name
        msg.level = DiagnosticStatus.OK
        msg.message = "OK"
        if not self.fake:
            msg.values.append(KeyValue("Left", str(self.enc_left)))
            msg.values.append(KeyValue("Right", str(self.enc_right)))
        msg.values.append(KeyValue("dX", str(self.dx)))
        msg.values.append(KeyValue("dR", str(self.dr)))
        return msg

    ###
    ### Controller Specification: 
    ###
    ###  setup: Kp, Kd, Ki, Ko (all unsigned char)
    ###
    ###  write: left_speed, right_speed (2-byte signed, ticks per frame)
    ###
    ###  status: left_enc, right_enc (4-byte signed)
    ### 
    
    def setup(self, kp, kd, ki, ko):
        success = self.device.execute(253, AX_CONTROL_SETUP, [10, kp, kd, ki, ko])

    def write(self, left, right):
        """ Send a closed-loop speed. Base PID loop runs at 30Hz, these values
                are therefore in ticks per 1/30 second. """
        left = left&0xffff
        right = right&0xffff
        success = self.device.execute(253, AX_CONTROL_WRITE, [10, left%256, left>>8, right%256, right>>8])

    def status(self):
        """ read 32-bit (signed) encoder values. """
        values = self.device.execute(253, AX_CONTROL_STAT, [10])
        left_values = "".join([chr(k) for k in values[0:4] ])        
        right_values = "".join([chr(k) for k in values[4:] ])
        try:
            left = unpack('=l',left_values)[0]
            right = unpack('=l',right_values)[0]
            return [left, right]
        except:
            return None
```
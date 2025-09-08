---
title: ROS用python读写串口数据
categories: ROS
tags: [ROS, python, serial, struct]
comments: true
keywords: ROS, python, serial, struct
description: 在ROS环境下，使用python读取串口数据，控制小车，并读取传感器数据并解析。
date: 2018-05-30 13:00:00
---
（转载请注明作者和出处：https://ningbo6.github.io）
##程序：
    
    import serial
    import struct
    
    ser = serial.Serial('/dev/ttyUSB0','115200')
    #write
    data1 = 1
    data2 = 2
    data3 = 3
    data = struct.pack('>BHB', data1, data2, data3)
    ser.write(data)
    #read
    data = ''
    data_temp = ser.read(4)
    data = struct.unpack('>BHB', data_temp)

##流程：
###首先在Ubuntu下安装pyserial

       pip install serial
###查看串口名称，并给予权限

       demsg | grep ttyUSB
       sudo chmod a+x ttyUSBn(n为你看到的数)
###进行读写实验
1. 在读写使用中用struct中的pack对数据进行转换再发送到串口，才能被STM32正常识别。同时，再读取时进行读取也能读出正常格式的数据。
其中B代表无符号的字节占一位，H占两位。
2. 读取数据时，单片机发送多少位，读取多少位，可以完整的读取数据。

##读与写的内容

###读
读出来的是16位byte类型的数据，先unpack之后，然后处理，前6位分别对应roll pitch yaw，第7、8位对应angular_z，第9到16位读出来是
每个轮的转速。奇数位是方向位，偶数位是速度位。

###写
先把255 254 mode v_x v_y a_z flag(方向标志位)按照BBBHHHB的顺序pack，然后再写到串口
---
title: 笔记本(GT 750M)Ubuntu14.04安装[CUDA8.0][cuDDN5.1][Tensorflow]
categories: tools
tags: 
    - ubuntu
    - CUDA
    - cuDDN
    - Tensorflow
comments: true
keywords: ubuntu, CUDA, cuDDN, Tensorflow
description: 安装TensorFlow的教程
date: 2017-12-22 10:39:02
---
（转载请注明作者和出处：https://ningbo6.github.io）

笔记本型号：联想Y500 

CPU　:　Intel® Core™ i7-3630QM CPU @ 2.40GHz × 8 

内存：7.7 GiB 

显卡：GeForce GT 750M/PCIe/SSE2 

系统版本：ubuntu 14.04 LTS 64bit

* * *

我的安装流程如下所示。

1.准备

首先确保你的系统是ubuntu14.04,而且一切都是正常的，如果有问题就重装吧，长痛不如短痛。

需要准备以下几个安装包： 

    (1)NVIDIA显卡驱动包 

    我用的是375版本的，我试了一下382，是通过图形界面的系统设置安装的，会进入循环登录，进不去桌面，所以学乖了，用命令行安装官网上下载好的NVIDIA-375驱动包。 

    (2)CUDA8.0驱动包 

    [CUDA下载地址](https://developer.nvidia.com/cuda-downloads)  

    进入到下载的网页之后，你可能找不到CUDA8.0,这时候你按着下面的步骤走就可以了 

    打开网页之后，点绿色的Release Notes,点右上角的蓝色的older,然后点绿色的CUDA Toolkit 8.0 GA2[Feb 2017]，然后选择适合你的选项，最后的Installer Type建议选择runfile[local] 

    (3)cuDDN5.1安装包 

    [cuDDN下载地址](https://developer.nvidia.com/rdp/cudnn-download) 

    打开网页需要你注册一个NVIDIA developer的账号，登录之后，同意他们的声明就可以下载，下载cuDNN v5.1 (Jan 20, 2017), for CUDA 8.0就可以了。 

2.安装 

    (1)安装NVIDIA显卡驱动 

        [安装NVIDIA驱动的博客](http://blog.csdn.net/cosmoshua/article/details/76644029) 

        [安装NVIDIA驱动的博客](https://www.cnblogs.com/alexanderkun/p/6905512.html)

        按Ctrl + Alt +F1切换到控制台，关闭图形环境

        ```
        卸载原有NVIDIA驱动
        sudo apt-get remove --purge nvidia*

        sudo init 3
        sudo rm -r /tmp/.X* 
        sudo service lightdm stop
        sudo sh 你的显卡驱动包.run
        sudo service lightdm start
        查看是否安装正确：
        cat /proc/driver/nvidia/version
        ```
    (2)安装CUDA8.0
        ```
        sudo chmod 777 cuda_8.0.44_linux.run
        sudo sh cuda_8.0.44_linux.run
        ```
        然后根据提示就可以安装了，安装驱动的那一行可以不用安装了
        之后加入配置

        ```
        sudo vim /etc/profile 
        添加以下内容(cuda-8.0可能不对，我的是需要改为cuda)
        export CUDA_HOME=/usr/local/cuda-8.0  
        export PATH=$CUDA_HOME/bin:$PATH    
        export LD_LIBRARY_PATH=$CUDA_HOME/lib64:$LD_LIBRARY_PATH
        使环境变量生效
        source /etc/profile

        测试：在新终端打开
        nvidia-smi
        ```
    (3)把GCC版本降到5.0以下，一般这里不用更改
    (4)安装cuDDN5.1
        首先解压cudnn-8.0-linux-x64-v5.1.tgz
        ```
        sudo tar xvzf cudnn-8.0-linux-x64-v5.1.tgz
        sudo cp cuda/include/cudnn.h /usr/local/cuda-8.0/include
        sudo cp cuda/lib64/libcudnn* /usr/local/cuda-8.0/lib64
        sudo chmod a+r /usr/local/cuda-8.0/include/cudnn.h /usr/local/cuda-8.0/lib64/libcudnn*
        ```
        中间根据自己的路径进行修改
    (5)安装TensorFlow
        我的python是3.4版本，所以下载对应的whl文件然后安装
        `sudo pip3 install tensorflow_gpu-1.2.1-cp34-cp34m-linux_x86_64.whl`
    (6)检查是否安装好
        ```
        import tensorflow as tf
        hello=tf.constant('Hello, TensorFlow')
        sess=tf.Session()
        print(sess.run(hello))
        ```

* * *

可能出现的问题及解决方案： 

以后补充 

遇到这个问题 

 CUDA Device Query (Runtime API) version (CUDART static linking) 

cudaGetDeviceCount returned 30 

-&gt; unknown error 

Result = FAIL 

执行以下命令 

sudo update-alternatives –config x86_64-linux-gnu_gl_conf 

然后选　/usr/lib/x86_64-linux-gnu/mesa/ld.so.conf　

[这个教程也不错，从中参考了很多](http://haiy.github.io/2016/07/17/ubuntu16.04%E4%B8%8AGTX1080%E7%9A%84CUDA%E5%AE%89%E8%A3%85.html)

* * *

版权声明：本文为博主原创文章，未经博主允许不得转载。
<div>作者：ynb19930428发表于2017/12/22 10:39:02[ 原文链接 ](http://blog.csdn.net/ynb19930428/article/details/78870667)</div><div> 阅读：118评论：0[查看评论 ](http://blog.csdn.net/ynb19930428/article/details/78870667#commentstarget =) </div>
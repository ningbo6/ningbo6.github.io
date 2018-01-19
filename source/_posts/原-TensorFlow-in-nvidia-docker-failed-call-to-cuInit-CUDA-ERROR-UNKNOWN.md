---
title: 'TensorFlow in nvidia-docker: failed call to cuInit: CUDA_ERROR_UNKNOWN'
categories: reinforcement learning
tags: 
    - TensorFlow
    - nvidia
    - CUDA_ERROR_UNKNOWN
comments: true
keywords: TensorFlow, CUDA_ERROR_UNKNOWN, cuInit, failed
description: 解决failed call to cuInit CUDA_ERROR_UNKNOWN的问题
date: 2017-12-28 20:28:01
---

背景： 

Ubuntu 

TensorFlow

* * *

问题描述： 

出现错误： 
'''
E TensorFlow in nvidia-docker: failed call to cuInit: CUDA_ERROR_UNKNOWN
'''

然后就用CPU在跑

* * *

解决方法：

    sudo update<span class="hljs-attribute">-alternatives</span> <span class="hljs-subst">--</span>config x86_64<span class="hljs-attribute">-linux</span><span class="hljs-attribute">-gnu_gl_conf</span>`</pre>

    ![解决之前](http://img.blog.csdn.net/20171228234716622?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW5iMTk5MzA0Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

    选择 /usr/lib/x86_64-linux-gnu/mesa/ld.so.conf  

    就好了 

    作为一个强迫症，你以为这样就完了吗，不！ 

    我要把其他三个删掉 

    这个命令是用管理员的权限打开文件浏览器，然后删除那些不用的三个选项。这三个选项是我之前安装NVIDIA驱动后并卸载的残留

    <pre class="prettyprint">`<span class="hljs-built_in">sudo</span> nautilus 

![解决之后的图片](http://img.blog.csdn.net/20171228234629685?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW5iMTk5MzA0Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

在/usr/lib/下　ctrl+f 输入nvidia　然后就能找到那些不用的选项删除就OK了。
<div>作者：ynb19930428发表于2017/12/28 20:28:01[ 原文链接 ](http://blog.csdn.net/ynb19930428/article/details/78926001)</div><div> 阅读：102评论：0[查看评论 ](http://blog.csdn.net/ynb19930428/article/details/78926001#commentstarget =) </div>
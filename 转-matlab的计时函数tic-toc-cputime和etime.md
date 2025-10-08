---
title: 转|matlab的计时函数tic toc cputime和etime
categories: tools
tags:
  - matlab
  - tic
  - toc
comments: true
keywords: matlab, tic, toc
description: matlab里面的计时函数：Matlab7的计时函数主要有tic,toc,cputime和etime等，计时函数可以定量的计算完成制定程序所消耗的时间资源，因为可以作为比较程序优劣的一个重要标准。 
date: 2017-11-09 23:54:28
---
（转载请注明作者和出处：https://ningbo6.github.io）
matlab里面的计时函数：Matlab7的计时函数主要有tic,toc,cputime和etime等，计时函数可以定量的计算完成制定程序所消耗的时间资源，因为可以作为比较程序优劣的一个重要标准。 

  a、tic和toc函数 

     这两个函数一般配合使用，tic表示计时的开始，toc表示计时的结束。 

      格式如： 

           tic 

               任意表达式 

           toc 

           t=toc 

  b、cputime函数 

     cputime函数返回从调用该函数起所用的总的Cpu时间，单位以秒计算。 

      格式如： 

            t=cputime; 

            任意表达式或者程序 

            e=cputime-t; 

   c、etime函数 

      e=etime(t2,t1)命令返回向量t1和t2之间的时间段，t1和t2必须含有由clock函数返回的6个元素，即[Year Month Day Hour Minute Second]。 

      举例说明：

> > x=rand(2048,1); 
> > 
> >     t=clock; 
> > 
> >     fift(x); 
> > 
> >     etime(clock,t); 
> > 
> >                  ans= 
> > 
> >                                14.53<div>作者：ynb19930428发表于2017/11/9 23:54:28[ 原文链接 ](http://blog.csdn.net/ynb19930428/article/details/78495475)</div><div> 阅读：53评论：0[查看评论 ](http://blog.csdn.net/ynb19930428/article/details/78495475#commentstarget =) </div>
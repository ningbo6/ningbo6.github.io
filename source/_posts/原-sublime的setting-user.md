---
title: setting-user of sublime
categories: tools
tags: 
    - ubuntu
    - sublime-text3
    - setting
comments: true
keywords: setting, sublime
description: sublime的setting-user
date: 2017-12-08 23:45:05
---
（转载请注明作者和出处：https://ningbo6.github.io）
刚开始用sublime,因为感觉很好用，所以想折腾一下。 

系统:ubuntu 14.04 

sublime版本:3126

作为一个渣渣，遇到的问题如下：

1.  注册的问题
 百度一搜一大堆，关键词为：sublime3126注册码，举个栗子：[用力戳这里](http://blog.csdn.net/javaexploreroooo/article/details/77989993)
2.  配置文件不能修改的问题
    Perferences-setting
打开之后，有两块左右各一，一开始很傻，一直在左边Default修改，发现怎么改也改不了，一度怀疑人生，后来才发现右边也有一个框，把你想要修改的部分复制到右边，然后再修改就好了，会优先加载user中的内容
'''
{
    "color_scheme": "Packages/Color Scheme - Default/Monokai.tmTheme",
    "font_face": "Courier New",
    "font_size": 18,
    "ignored_packages":
    [
        "Vintage"
    ],
    "line_padding_bottom": 1,
    "line_padding_top": 1,
    "match_brackets": true,
    "theme": "Default.sublime-theme",
    "tranlate_tabs_to_spaces": true,
    "wrap_width": 100
}
'''

我的是这样的，我喜欢Courier New的字体，你也可以试试，看起来很舒服。因为想要用python，所以把tab转换为space，这样省了很多事情。

---
layout: post
tags: [tensorflow,opencv]
title: win10安装tensroflow & opencv by anaconda
---

[官网](https://www.anaconda.com/download/)上下载anaconda(Python3.x)并安装

next steps:

1. open cmd 

2. 输入命令 "conda creat --name tensorflow python==3.6"
   (这个3.6是我电脑安装的python版本,根据自己电脑安装的python版本进行更改)
   该命令的意思是创建tensorflow环境

3. activate tensorflow(下面的4,5,6,7steps在tensorflow环境下进行)
   该命令的意思是激活tensorflow环境

4. 升级pip(先输入pip install tensorflow,会失败,之后会提示怎么升级pip)
   python -m pip install --upgrade pip
   也许会失败,如果失败了,那我们就再进行暴力升级
   python -m pip install -U --force-reinstall pip

5. pip install --upgrade setuptools

6. conda install --channel https://conda.anaconda.org/menpo opencv3
(关键:要先安装opencv, 再安装tensorflow,否则会出现一些错误,目前我经历的是这样)

7. pip install tensorflow(会自动下载最新版本)


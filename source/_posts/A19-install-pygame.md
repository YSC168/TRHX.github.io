---
title: Windows 系统中 Pygame 的安装
categories: 
  - Python3 学习笔记
  - 学习经验
tags: 
  - Pygame
  - Python
description: Windows 系统中 Pygame 的安装笔记
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/PythonCode.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---

> Pygame是跨平台Python模块，专为电子游戏设计，可用于管理图形、动画乃至声音，建立在SDL基础上，允许实时电子游戏研发而无需被低级语言（如机器语言和汇编语言）束缚，通过使用Pygame来处理在屏幕上绘制图像等任务，你不用考虑众多繁琐而艰难的编码工作，而是将重点放在程序的高级逻辑上。

你可以从以下三个地址查找与你运行的Python版本相匹配的Windows安装程序：

- https://bitbucket.org/pygame/pygame/downloads/  （Pygame项目托管在代码分享网站Bitbucket中）

- http://www.pygame.org/download.shtml （Pygame官网）

- https://www.lfd.uci.edu/~gohlke/pythonlibs/#pygame （如果以上两个地址找不到合适的安装程序，推荐去这个）

![01](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A19/01.png)

如果下载的是.exe文件，直接运行它，如果下载的是.whl文件，就需要打开命令窗口，切换到该文件所在的目录，使用pip来运行它：

首先检查电脑是否安装了pip，打开终端窗口，执行如下命令：
```
>python -m pip --version
```
如果输出版本信息则已安装：
```
>pip 18.1 from E:\Python\lib\site-packages\pip (python 3.6)
```

否则请安装pip，访问 https://bootstrap.pypa.io/get-pip.py ，如果出现对话框请直接保存文件，如果出现的是get-pip.py的源代码，则需要新建一个get-pip.py文件，将该代码复制粘贴到其中，使用下面的命令运行get-pip.py：
```
>python get-pip.py
```
安装完成后可再次使用`python -m pip --version`命令检查是否成功安装了pip，成功安装pip后，使用以下命令来安装Pygame：<font color=#FF0000>（注意要先cd到你下载的文件的目录）</font>
```
>python -m pip install --user 下载的.whl文件名
```
出现以下信息则表示安装成功：
```
>Successfully installed 你安装的Pygame版本
```
比如我的Python版本是3.6.5，64位的，则需要下载pygame‑1.9.4‑cp36‑cp36m‑win_amd64.whl，该文件保存到了桌面，使用下面的命令安装Pygame:
```
    C:\Users\Lenovo>cd desktop
    
    C:\Users\Lenovo\Desktop>python -m pip install --user pygame‑1.9.4‑cp36‑cp36m‑win_amd64.whl
    Processing c:\users\lenovo\desktop\pygame‑1.9.4‑cp36‑cp36m‑win_amd64.whl
    Installing collected packages: pygame
    Successfully installed pygame‑1.9.4
    
    C:\Users\Lenovo>Desktop>
```
检查是否成功安装Pygame：在Python的IDLE里输入`import pygame`，如果不报错，则安装成功，再输入`pygame.ver`就能看到版本号：

![02](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A19/02.png)
)

可能出现的问题：
报错：`xxxxxxxxxxxxxxxxxxxxxx.whl is not a supported wheel on this platform.`
原因：Python版本与Pygame版本不对应
解决方法：Pygame文件名中的cp**表示Python对应的版本，另外并不是你电脑64位则下载64位，要看你安装的Python是否为64位，注意下载对应的版本！

报错：`You are using pip version x.x.x, however version x.x.x is available.You should consider upgrading via the 'python -m pip install --upgrade pip' command.`
原因：版本需要更新
解决方法：输入`python -m pip install --upgrade pip`命令进行更新即可

Pygame安装完成后我们就可以使用Python来开发游戏啦！
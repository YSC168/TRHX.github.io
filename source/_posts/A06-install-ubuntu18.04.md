---
title: VMware Pro 14 安装 Ubuntu 18.04 详细教程
categories: Linux
tags:  
 - VMware
 - Ubuntu
description: VMware Pro 14 安装 Ubuntu 18.04 的详细教程以及一些在安装过程中可能遇到的问题
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/ubuntu.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
 # <font color=#FF0000>1.下载安装 VMware Workstation Pro 14</font>
 进入 [VMware 官网](https://www.vmware.com/products/workstation-pro/workstation-pro-evaluation.html)或者在软件商店下载最新版VMware虚拟机并安装
 
 ![01](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/01.jpg
)
 
  # <font color=#FF0000>2.下载 Ubuntu 18.04 系统</font>
  进入 [Ubuntu 官网](https://www.ubuntu.com/)，下载最新版 Ubuntu 系统镜像
  
![02](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/02.jpg
)
![03](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/03.jpg
)

# <font color=#FF0000>3.在 VMware 中创建虚拟机</font>

打开安装好的 VMware Workstation Pro 14，选择创建新的虚拟机
![04](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/04.jpg
)

在新建虚拟机向导中选择自定义（高级）
![05](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/05.jpg
)

默认直接下一步，直到出现下图，再选择稍后安装操作系统
![06](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/06.png
)

选择客户机操作系统为 Linux ，如果你电脑是32位就选择 Ubuntu 版本，64位就选择 Ubuntu 64 位版本
![07](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/07.png)

更改虚拟机名称及存放位置
![08](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/08.png)

为虚拟机指定处理器数量，默认即可
![09](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/09.png)

为虚拟机分配内存，太大了可能会导致卡顿，太小了也不好，推荐内存大小即可
![10](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/10.png)

以下均选择默认即可
![11](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/11.png)
![13](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/13.png)
![14](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/14.png)

选择创建新虚拟磁盘
![15](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/15.png)

选择将虚拟磁盘储存为单个文件
![16](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/16.png)

默认下一步
![17](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/17.png)

点击完成
![18](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/18.png)

此时我们就可以在虚拟机左侧“我的计算机”下面看到刚刚创建的虚拟机 Ubuntu 64 位，单击 Ubuntu 64 位，选择“编辑虚拟机设置”， 再选择“CD/DVD(SATA)”，选择“使用ISO映像文件”，点击“浏览”，找到先前我们下载好的 Ubuntu 64 位镜像文件，点击“确定”
![19](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/19.jpg)
![20](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/20.png)

# <font color=#FF0000>4.在虚拟机上安装 Ubuntu 系统</font>

单击 Ubuntu 64 位，选择“开启此虚拟机”
![21](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/21.jpg)

来到欢迎界面，选择好语言，点击“安装 Ubuntu”
![22](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/22.jpg)

选择键盘布局为“汉语”
![23](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/23.jpg)

更新和其他软件默认选择即可
![24](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/24.jpg)

安装类型选择“清除整个磁盘并安装 Ubuntu”，PS: 因为我们是新安装的系统，且在虚拟机中，所以可以选择清除整个磁盘，这个操作不会清除你原来电脑里面的东西
![25](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/25.jpg)
![26](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/26.jpg)

地区随便，在中国就行，默认即可
![27](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/27.jpg)

之后设置计算机名，密码
![28](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/28.jpg)

点击继续稍等一会就安装完成啦
![29](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/29.jpg)

----------

# <center><font color=#FF0000 size=7>安装过程中可能会出现的一些问题</font></center>

----------

# <font color=#FF0000> 1.在虚拟机上安装 Ubuntu 系统的过程中卡死不动</font>

 解决方法：关闭网络，重新安装即可

----------

# <font color=#FF0000> 2.Ubuntu 不能全屏显示</font>
解决方法：
方法①：安装 open-vm-tools：

``` vim
sudo apt-get install open-vm-tools 
```
然后执行：

``` vim
sudo apt-get install open-vm* 
```
重启即可全屏显示

方法②：在终端输入`xrandr`，并回车，我们就可以看到很多可以修改的分辨率，选择好分辨率后，比如我们要修改分辨率为 1920x1440 ，则在终端输入 `xrandr -s 1920x1440`，回车即可，注意 1920x1440 中间是<font color=#FF0000>小写字母 x</font>，本人亲测此方法并不是很完美，不能完全适应屏幕
![30](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A06/30.jpg)

方法③：安装 VMware Tools：
1、进入 Ubuntu 系统后，点击虚拟机上的【虚拟机】—＞【安装 VMware Tools】，回到桌面即可看到一个 VMware Tools 的 图标
2、复制 VMwareTools-10.0.10-4301679.tar.gz（版本根据自己的实际情况而定）到 home 目录下， 用命令 `tar -xzvf  VMwareTools-10.0.10-4301679.tar.gz` 进行解压
3、解压后 cd vmware_tools_distrib，打开终端
4、输入“sudo ./vmware-install.pl”，输入用户密码后开始安装
5、接下来会有很多地方需要你按 Enter或者 Yes
6、当你看到出现 `—the vmware team` 的字样后就可以关闭窗口了，此时窗口就会自动全屏了，如果没有全屏，重启过后就可以了
7、若还没有全屏显示，则将虚拟机的【查看】—>【自动调整大小】—>【自适应客户机】，都选上，即可实现全屏
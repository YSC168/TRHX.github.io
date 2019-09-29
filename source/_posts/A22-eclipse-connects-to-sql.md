---
title: Eclipse 通过 JDBC 连接 SQL Server
tags: 
  - JDBC
  - SQL Server 2012
  - Elicpse
categories: Java
description: Eclipse 与 SQL Server 建立连接
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/SQLServer.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
本文用到的软件版本以及相关环境：

> Eclipse Photon Release (4.8.0)
> JDK-10.0.2
> SQL Server 2012

 # <font color=#FF0000>1.配置 SQL Server 2012</font>
 
打开 SQL Server Management Studio，使用 <font color=#FF0000>SQL Server 身份验证</font> 登录：

![001.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A22/001.png)

如果在安装 SQL Server 2012 时选用了Windows身份验证登录方式，则需要重新设置，设置方法参考：[《SQL Server 登录更换【Windows身份验证】为【SQL Server 身份验证】》](https://blog.csdn.net/qq_36759224/article/details/90204243)

登录成功后，打开 SQL Server 配置管理器：

![002.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A22/002.png)

在左边找到 SQL Server 网络配置，点击【你的数据库名】的协议，将右边栏的 <font color=#FF0000>Shared Memory、Named Pipes、TCP/IP</font> 全部右键选择<font color=#FF0000>启用</font>：

![003.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A22/003.png)
![004.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A22/004.png)

双击 <font color=#FF0000>TCP/IP</font>（或者右键选择属性），选择【<font color=#FF0000>IP地址</font>】，将【<font color=#FF0000>IP1</font>】和【<font color=#FF0000>IP10</font>】的【<font color=#FF0000>IP地址</font>】设为 <font color=#FF0000>127.0.0.1</font>

![005.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A22/005.png)

将所有【<font color=#FF0000>IPx</font>】（<font color=#FF0000>IP1、IP10、IP11、IP12</font>等）的【<font color=#FF0000>已启用</font>】设为<font color=#FF0000>是</font>

![006.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A22/006.png)

下拉到窗口底部，将 【<font color=#FF0000>IPAll</font>】 中的【<font color=#FF0000>TCP端口</font>】设成 <font color=#FF0000>1433</font>，其余不变

![007.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A22/007.png)

 # <font color=#FF0000>2.开启 Telnet 服务</font>
打开【<font color=#FF0000>控制面板</font>】，选择【<font color=#FF0000>程序</font>】，点击【<font color=#FF0000>启用或关闭 Windows 功能</font>】，找到【<font color=#FF0000>Telnet Client</font>】勾选并保存，Windows 7 或者以下的版本则勾选【<font color=#FF0000>Telnet 服务器</font>】和【<font color=#FF0000>Telnet 客户端</font>】

![008.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A22/008.png)
![009.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A22/009.png)

# <font color=#FF0000>3.测试1433端口是否打开</font>
 运行cmd，输入 telnet 127.0.0.1 1433，若提示连接失败，则说明1433端口没有打开，需要重新进行以上配置，若连接成功，则显示如下：
 
 ![010.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A22/010.png)
 
# <font color=#FF0000>4.下载JDBC</font>
 [点击此处](https://docs.microsoft.com/zh-cn/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017)下载各个版本JDBC，不同版本的JDBC驱动程序适用的JAR不同，与不同版本的SQL兼容性也不同，具体参考[《Microsoft SQL Server JDBC 驱动程序支持矩阵》](https://docs.microsoft.com/zh-cn/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server-support-matrix?view=sql-server-2017)，比如使用 SQL Server 2012 我们可以下载6.0的版本，下载<font color=#FF0000>sqljdbc_6.0.8112.200_chs.tar.gz</font>文件，解压后可以找到<font color=#FF0000>sqljdbc41.jar</font>与<font color=#FF0000>sqljdbc42.jar</font>文件，使用时要注意自己JDK是哪个版本的，1.80以上的则对应 <font color=#FF0000>sqljdbc42.jar</font> 类库
 
# <font color=#FF0000>5.Eclipse 连接 SQL Server</font>
将 sqljdbc41.jar 或者 sqljdbc42.jar 放到一个文件夹下，打开 Eclipse，在需要连接数据库的项目里，右键【<font color=#FF0000>src</font>】，选择【<font color=#FF0000>Build Path</font>】、【<font color=#FF0000>Configure Build Path...</font>】，在弹出的窗口选择【<font color=#FF0000>Libraries</font>】，选择【<font color=#FF0000>Modulepath</font>】，单击【<font color=#FF0000>Add External JARs...</font>】，找到下载的 sqljdbc41.jar 或者 sqljdbc42.jar 文件并打开，然后【<font color=#FF0000>Apply and Close</font>】保存

![011.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A22/011.png)
![012.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A22/012.png)
![013.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A22/013.png)

# <font color=#FF0000>6.测试连接</font>
打开 SQL Server 2012，在其中新建数据库 test

![014.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A22/014.png)

Eclipse中，在项目下新建一个package，再新建一个class，用于测试数据库的连接：
```java
package test;
import java.sql.*;
public class Main {
	public static void main(String [] args)
	{
		String driverName="com.microsoft.sqlserver.jdbc.SQLServerDriver";
		String dbURL="jdbc:sqlserver://localhost:1433;DatabaseName=test";  //要连接的数据库名
		String userName="sa";  //数据库用户名
		String userPwd="000000";  //数据库密码
		try
		{
			Class.forName(driverName);
			Connection dbConn=DriverManager.getConnection(dbURL,userName,userPwd);
			System.out.println("连接数据库成功");
			}
		catch(Exception e)
		{
			e.printStackTrace();
			System.out.print("连接失败");
			}
		}
	}
```
如果以上所有操作正确，就能成功连接数据库了：

![015.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A22/015.png)
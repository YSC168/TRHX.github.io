---
title: Hexo 双线部署到 Coding Pages 和 GitHub Pages 并实现全站 HPPTS
categories: Hexo
tags:
 - Hexo
 - Coding Pages
 - GitHub Pages
description: Hexo 双线部署到 Coding Pages 和 GitHub Pages 并实现全站 HPPTS
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/hexo.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
icons: [fas fa-heading]
---
> 部署到 Coding Pages 的好处：国内访问速度更快，可以提交百度收录（GitHub 禁止了百度的爬取）

> 部署到 Coding Pages 的坏处：就今年来说，Coding 不太稳定，随时有宕机的可能，群里的朋友已经经历过几次了，不过相信以后会越来越稳定的

> 部署过程中常见的问题：无法实现全站 HTTPS，Coding 申请 SSL 证书失败，浏览器可能会提示不是安全链接

> 本文前提：你已经将 Hexo 成功部署到了 GitHub Pages，如果还没有，请参考：[《使用Github Pages和Hexo搭建自己的独立博客【超级详细的小白教程】》](https://blog.csdn.net/qq_36759224/article/details/82121420)

> 本文将全面讲述如何成功双线部署到 Coding Pages 和 GitHub Pages 并实现全站 HPPTS，同时解决一些常见的问题！

---

# <font color=#ff0000>1.创建项目</font>

进入 [Coding 官网](https://coding.net/)，点击个人版登陆，没有账号就注册一个并登录，由于 Coding 已经被腾讯收购了，所以登录就会来到腾讯云开发者平台，点击创建项目

<fancybox>
![01](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A47/01.jpg)
</fancybox>

项目名称建议和你的用户名一致，这样做的好处是：到时候可以直接通过 `user_name.coding.me` 访问你的博客，如果项目名与用户名不一致，则需要通过 `user_name.coding.me/project_name` 才能访问，项目描述可以随便写

<fancybox>
![02](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A47/02.jpg)
</fancybox>

---

# <font color=#ff0000>2.配置公钥</font>

配置 SSH 公钥方法与 GitHub Pages 的方式差不多，点击你的头像，依次选择【个人设置】-【SSH公钥】-【新增公钥】

<fancybox>
![03](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A47/03.jpg)
</fancybox>

前面部署到 GitHub Pages 的时候就已经有了一对公钥，我们直接将该公钥粘贴进去就行，公钥名称可以随便写，选中永久有效选项

PS：公钥储存位置一般在 <font color=#ff0000>C:\Users\用户名\\.ssh</font> 目录下的 <font color=#ff0000>id_rsa.pub</font> 文件里，用记事本打开复制其内容即可

<fancybox>
![04](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A47/04.jpg)
</fancybox>

添加公钥后，我们可以右键 `Get Bash`，输入以下命令来检查是否配置成功：

```bash
ssh -T git@git.coding.net
```

若出现以下提示，则证明配置成功：

```bash
Coding 提示: Hello XXX, You've connected to Coding.net via SSH. This is a personal key.
XXX，你好，你已经通过 SSH 协议认证 Coding.net 服务，这是一个个人公钥
```

---

# <font color=#ff0000>3.配置 _config.yml</font>
进入你的项目，在右下角有选择连接方式，选择 SSH 方式（HTTPS 方式也可以，但是这种方式有时候可能连接不上，SSH 连接不容易出问题），一键复制，然后打开你本地博客根目录的 `_config.yml` 文件，找到 `deploy` 关键字，添加 coding 地址：`coding: git@git.dev.tencent.com:user_name/user_name.git`，也就是刚刚复制的 SSH 地址

<fancybox>
![05](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A47/05.jpg)
</fancybox>

<fancybox>
![06](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A47/06.jpg)
</fancybox>

添加完成后先执行命令 `hexo clean` 清理一下缓存，然后执行命令 `hexo g -d` 将博客双线部署到 Coding Pages 和 GitHub Pages，如下图所示表示部署成功：

<fancybox>
![13](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A47/13.jpg)
</fancybox>

# <font color=#ff0000>4.开启 Coding Pages</font>
进入你的项目，在代码栏下选择 Pages 服务，一键开启 Coding Pages，等待几秒后刷新网页即可看到已经开启的 Coding Pages，到目前为止，你就可以通过 xxxx.coding.me（比如我的是 trhx.coding.me）访问你的 Coding Pages 页面了

<fancybox>
![07](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A47/07.jpg)
</fancybox>

<fancybox>
![08](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A47/08.jpg)
</fancybox>

# <font color=#ff0000>5.绑定域名并开启 HPPTS</font>
首先在你的域名 DNS 设置中添加一条 `CNAME` 记录指向 `xxxx.coding.me`，解析路线选择 `默认`，将 GitHub 的解析路线改为 `境外`，这样境外访问就会走 GitHub，境内就会走 Coding，也有人说阿里云是智能解析，自动分配路线，如果解析路线都是默认，境外访问同样会智能选择走 GitHub，境内走 Coding，我没有验证过，有兴趣的可以自己试试，我的解析如下图所示：

<fancybox>
![09](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A47/09.jpg)
</fancybox>

然后点击静态 Pages 应用右上角的设置，进入设置页面，这里要注意，如果你之前已经部署到了 GitHub Pages 并开启了 HTTPS，那么直接在设置页面绑定你自己的域名，SSL/TLS 安全证书就会显示申请错误，如下图所示，没有申请到 SSL 证书，当你访问你的网站时，浏览器就会提示不是安全连接

<fancybox>
![10](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A47/10.jpg)
</fancybox>

申请错误原因是：在验证域名所有权时会定位到 Github Pages 的主机上导致 SSL 证书申请失败

正确的做法是：<font color=#ff0000>先去域名 DNS 把 GitHub 的解析暂停掉，然后再重新申请 SSL 证书</font>，大约十秒左右就能申请成功，然后开启强制 HTTPS 访问

这里也建议同时绑定有 www 前缀和没有 www 前缀的，如果要绑定没有 www 前缀的，首先要去域名 DNS 添加一个 `A` 记录，主机记录为 `@`，记录值为你博客 IP 地址，IP 地址可以在 cmd 命令行 ping 一下得到，然后在 Coding Pages 中设置其中一个为【首选】，另一个设置【跳转至首选】，这样不管用户是否输入 www 前缀都会跳到有 www 前缀的了

在博客资源引用的时候也要注意所有资源的 URL 必须是以 https:// 开头，不然浏览器依旧会提示不安全！

<fancybox>
![13](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A47/13.png)
</fancybox>

<fancybox>
![11_1](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A47/11_1.png)
</fancybox>

至此，我们的 Hexo 博客就成功双线部署到 Coding Pages 和 GitHub Pages 了，并且也实现了全站 HPPTS，最后来一张 GitHub Pages 和 Coding Pages 在国内的速度对比图，可以明显看到速度的提升

<fancybox>
![12](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A47/12.jpg)
</fancybox>

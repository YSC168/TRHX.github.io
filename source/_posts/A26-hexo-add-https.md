---
title: 利用Cloudflare为基于GitHub Pages的Hexo博客添加HTTPS支持
categories: Hexo
tags:
 - HTTPS
 - Hexo
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/hexo.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> 利用Cloudflare为基于GitHub Pages的Hexo博客添加HTTPS支持

<!--more-->

---
> HTTP（超文本传输协议），是一个基于请求与响应，无状态的，应用层的协议，常基于TCP/IP协议传输数据，互联网上应用最为广泛的一种网络协议，所有的WWW文件都必须遵守这个标准。设计HTTP的初衷是为了提供一种发布和接收HTML页面的方法。

> HTTPS（超文本传输安全协议），是以安全为目标的HTTP通道，简单讲是HTTP的安全版。即HTTP下加入SSL层，HTTPS的安全基础是SSL，因此加密的详细内容就需要SSL。它是一个URI scheme（抽象标识符体系），句法类同http:体系。用于安全的HTTP数据传输。

---

# <font color=#FF000>-- 前言</font>
GitHub Pages 自带的域名（xxx.github.io）支持开启 https 服务，可以在仓库的【Settings】- 【GitHub Pages】下勾选【Enforce HTTPS】即可，但是如果你设置了自定义域名的话，就比较复杂了，因为 hexo 博客是托管在 GitHub 上的，没有自己的服务器，因此也不支持上传 SSL 证书，从2018年5月1日起，GitHub官方也支持自定义域名开启https了，实现方法可参考我的文章：[《利用官方支持为基于GitHub Pages的Hexo博客启用HTTPS》](https://www.itrhx.com/2019/08/11/A28-hexo-add-https/)，另外一种方法就是利用 Cloudflare 的 CDN 中转来启用 HTTPS，这种方法的弊端就是国内访问速度可能会变慢，本文主要讲述这种方法

Cloudflare 是一家美国的跨国科技企业，以向客户提供网站安全管理、性能优化及相关的技术支持为主要业务，它提供了免费的 https 服务，注意不是应用SSL证书，实现原理：用户到CDN服务器的连接为 https 方式，而CDN服务器到 GithubPages 服务器的连接为 http 方式，在CDN服务器那里加上反向代理

# <font color=#FF000>-- 注册 Cloudflare</font>
到 [Cloudflare官网](https://dash.cloudflare.com/sign-up) 注册账号
<fancybox>
![01.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A26/01.png)
</fancybox>

# <font color=#FF000>-- 添加站点</font>
添加你的站点，一直下一步即可
<fancybox>
![02.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A26/02.png)
</fancybox>
<fancybox>
![03.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A26/03.png)
</fancybox>
如果你已经在域名服务商那里解析过域名的话，之后就会出现你域名的解析列表，如果还没有解析过，可以参考[《为hexo博客配置个性域名》](https://blog.csdn.net/qq_36759224/article/details/82121420#font_colorFF000__Hexo_font_270)
<fancybox>
![04.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A26/04.png)
</fancybox>

# <font color=#FF000>--修改DNS</font>
点击下一步 Cloudflare 会提供给你两个 DNS 地址
<fancybox>
![05.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A26/05.png)
</fancybox>
到域名服务商那里修改DNS，以阿里云为例，依次选择【控制台】-【域名】，选择你的域名，点击【管理】-【修改DNS】，将上面 Cloudflare 提供的两个 DNS 地址填进去，会过几分钟才生效
<fancybox>
![08.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A26/08.png)
</fancybox>
<fancybox>
![09.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A26/09.png)
</fancybox>
<fancybox>
![10.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A26/10.png)
</fancybox>

# <font color=#FF000>--开启 HTTPS</font>
在 Cloudflare 管理页面，点击【Crypto】选项，选择 SSL 的模式为【full】，注意：在CloudFlare 上激活站点后，可能需要24小时才能颁发新证书，耐心等待即可
<fancybox>
![07.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A26/07.png)
</fancybox>

关于三种模式 Flexible、Full、Full (Strict) 的区别：

 - Flexible：访客与 Cloudflare 之间是加密的，Cloudflare 到站点服务器是不加密的

 - Full：访客到 Cloudflare、Cloudflare 到站点服务器都是加密的，它不会验证你服务器上的证书是否合法，因此你可以在你服务器上安装任何证书，包括自签名证书

 - Full (strict)：访客到 Cloudflare、Cloudflare 到站点服务器都是加密的，它会验证你服务器上的证书是否合法，你必须在你的服务器上安装有可信赖的CA证书，并且这个证书必须是未过期，包含有域名等信息的

至此，我们的域名就支持 https 访问了，但是当用户输入 http://xxxxxx 访问时，浏览器依旧会以 http 协议来访问，并不会跳转到 https，这时候就需要利用重定向来解决了

# <font color=#FF000>--重定向强制 HTTPS</font>
Cloudflare 提供了一个名叫 Page Rules 的页面规则的功能，我们可以利用此功能对 URL 做一些处理，当用户访问是 HTTP 的时候重定向到 HTTPS，点击【Page Rules】选项，点击【Create Page Rules】，新建如下规则并保存即可

<fancybox>
![06.png](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A26/06.png)
</fancybox>

现在我们的 Hexo 博客就实现了全站 HTTPS！
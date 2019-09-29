---
title: 利用官方支持为基于GitHub Pages的Hexo博客启用HTTPS
top: true
categories: Hexo
tags:
 - HTTPS
 - Hexo
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/hexo.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> 利用官方支持为基于GitHub Pages的Hexo博客启用HTTPS

<!--more-->

---
> HTTP（超文本传输协议），是一个基于请求与响应，无状态的，应用层的协议，常基于TCP/IP协议传输数据，互联网上应用最为广泛的一种网络协议，所有的WWW文件都必须遵守这个标准。设计HTTP的初衷是为了提供一种发布和接收HTML页面的方法。

> HTTPS（超文本传输安全协议），是以安全为目标的HTTP通道，简单讲是HTTP的安全版。即HTTP下加入SSL层，HTTPS的安全基础是SSL，因此加密的详细内容就需要SSL。它是一个URI scheme（抽象标识符体系），句法类同http:体系。用于安全的HTTP数据传输。

---
目前大多数基于 GitHub Pages 的 Hexo 博客都是利用 CloudFlare 的 CDN 中转来启用 HTTPS 的，实现方法可以参考我的文章：[《利用Cloudflare为基于GitHub Pages的Hexo博客添加HTTPS支持》](https://www.itrhx.com/2019/07/31/A26-hexo-add-https/)，这样的做法确实可以起到开启HTTPS的目的，但是这样做也有弊端，你会发现 CDN 中转，国外访问的话，可以起到加速的作用，但是国内访问反而速度降低了，还不如直接连接GitHub呢

其实 GitHub 官方是支持自定义域名开启 HTTPS 的，之前我和大多数人一样，以为只有 GitHub Pages 自带的域名（xxx.github.io）才能开启 HTTPS，直到有一天我发现了官方在2018年5月1日发表的博客：[《Custom domains on GitHub Pages gain support for HTTPS》](https://github.blog/2018-05-01-github-pages-custom-domains-https/)，大概讲的意思就是从8月份开始， GitHub Pages 上的自定义域名也能开启 HTTPS 了，下面就具体介绍一下如何实现

如果你以前域名的记录类型是 CNAME 方式，那么就不需要做任何更改
如果你以前域名的记录类型是 A 方式，那么就需要把记录值指向以下IP地址：

 - 185.199.108.153
 - 185.199.109.153
 - 185.199.110.153
 - 185.199.111.153
 
修改好记录值后，我们需要再次来到你博客的 GitHub 仓库，在仓库的【Settings】- 【GitHub Pages】下勾选【Enforce HTTPS】，注意，如果此时你不能勾选，请删除【Custom domain】里面你的域名并点击【Save】保存，刷新网页后就可以勾选了，然后在把域名填进去并保存即可，短时间可能会出现不安全的提示，这是因为加密证书大概一个小时左右才会生效，等一会儿就好了
<fancybox>
![01](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A28/01.png)
</fancybox>
最后贴一个我的域名解析，可作为参考：

<fancybox>
![02](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A28/02.png)
</fancybox>
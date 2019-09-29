---
title: 一个 JS 脚本实现网站预加载，提升页面加载速度
tags: 
  - instant.page
  - JS 预加载
categories: WEB前端
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/JavaScript.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---

[instant.page](https://instant.page/) 使用即时预加载技术，在用户点击之前预先加载页面。当用户的鼠标悬停在一个链接上超过 65 毫秒时，浏览器会对此页面进行预加载，当用户点击链接后，就从预加载的缓存中直接读取页面内容，从而达到缩短页面加载时间的目的。

<!-- more -->

<fancybox>
![演示](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A24/01.gif)
</fancybox>

以我博客为例，使用了这项技术后，当鼠标在一个链接停留超过 65 毫秒时，Network 里可以看见相关文章已经预加载出来了，而停留时间过短就不会预加载（红色部分，状态为 <font color=#FF0000>canceled</font>）

使用方法：
将以下HTML代码放在</ body> 之前即可：
```html
<script src="//instant.page/1.2.2" type="module" integrity="sha384-2xV8M5griQmzyiY3CDqh1dn4z3llDVqZDqzjzcY+jCBCk/a5fXJmuZ/40JJAPeoU"></script>
```
但是此脚本是官方的，储存在国外服务器，对国内访问不太友好，可以将该JS脚本储存到自己的服务器上，[点此获取](https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.0.2/js/instantclick-1.2.2.js)该JS脚本，然后再根据以下格式在</ body> 之前引用：
```html
<script src="`存放路径`/instantclick-1.2.2.js" type="module"></script>
```
也可以直接使用我的，使用 jsDeliver CDN 加速，速度还可以：
```html
<script src="https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.0.2/js/instantclick-1.2.2.js" type="module"></script>
```
参考资料：[《网站预加载 JS 脚本 instant.page》](https://www.zrahh.com/archives/399.html)——by 左岸  ；[instant.page官网](https://instant.page/)
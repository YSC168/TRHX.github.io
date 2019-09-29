---
title: 免费CDN：jsDelivr + Github
tags: 
  - jsDelivr
  - CDN
categories: CDN
description: 放在 Github 的资源在国内加载速度比较慢，因此需要使用 CDN 加速来优化网站打开速度，jsDelivr + Github 便是免费且好用的 CDN，非常适合博客网站使用。
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/cdn.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---

#### <center><font color=#FF0000>本文有参考</font>[《jsDelivr+github使用教程，免费好用的cdn》](https://www.hojun.cn/2019/01/18/jsDeliver-github%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B%EF%BC%8C%E5%85%8D%E8%B4%B9%E5%A5%BD%E7%94%A8%E7%9A%84cdn/)—— By hojun</center>
---
CDN的全称是Content Delivery Network，即内容分发网络。CDN是构建在网络之上的内容分发网络，依靠部署在各地的边缘服务器，通过中心平台的负载均衡、内容分发、调度等功能模块，使用户就近获取所需内容，降低网络拥塞，提高用户访问响应速度和命中率。CDN的关键技术主要有内容存储和分发技术。——百度百科

放在Github的资源在国内加载速度比较慢，因此需要使用CDN加速来优化网站打开速度，jsDelivr + Github便是免费且好用的CDN，非常适合博客网站使用。

---
# <font color=#FF0000>1、新建Github仓库 </font>
![01](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A18/01.png)

# <font color=#FF0000>2、克隆Github仓库到本地 </font>
  点击 Clone or download，一键复制仓库地址
![04](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A18/04.png)

 在本地目录右键 Git Bash Here，执行以下命令：
 ```git
 git clone 一键复制的仓库地址
```

# <font color=#FF0000>3、上传资源 </font>
  复制需要上传的资源到本地git仓库（注：jsDelivr不支持加载超过20M的资源），在本地git仓库目录下右键 Git Bash Here，执行以下命令：
```git
git status                    //查看状态
git add .                     //添加所有文件到暂存区
git commit -m '第一次提交'      //把文件提交到仓库
git push                      //推送至远程仓库
```

# <font color=#FF0000>4、发布仓库 </font>
  点击release发布
![02](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A18/02.png)

  自定义发布版本号
  ![03](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A18/03.png)

# <font color=#FF0000>5、通过jsDelivr引用资源 </font>
使用方法：https://cdn.jsdelivr.net/gh/你的用户名/你的仓库名@发布的版本号/文件路径
例如：https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@1.0/images/trhx.png
&nbsp;&nbsp;&nbsp;&nbsp;https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.0.1/css/style.css
&nbsp;&nbsp;&nbsp;&nbsp;https://cdn.jsdelivr.net/gh/moezx/cdn@3.1.3//The%20Pet%20Girl%20of%20Sakurasou.mp4

注意：版本号不是必需的，是为了区分新旧资源，如果不使用版本号，将会直接引用最新资源，除此之外还可以使用某个范围内的版本，查看所有资源等，具体使用方法如下：

- // 加载任何Github发布、提交或分支
 https://cdn.jsdelivr.net/gh/user/repo@version/file

- // 加载 jQuery v3.2.1
 https://cdn.jsdelivr.net/gh/jquery/jquery@3.2.1/dist/jquery.min.js

- // 使用版本范围而不是特定版本
  https://cdn.jsdelivr.net/gh/jquery/jquery@3.2/dist/jquery.min.js
  https://cdn.jsdelivr.net/gh/jquery/jquery@3/dist/jquery.min.js

- // 完全省略该版本以获取最新版本
https://cdn.jsdelivr.net/gh/jquery/jquery/dist/jquery.min.js

- // 将“.min”添加到任何JS/CSS文件中以获取缩小版本，如果不存在，将为会自动生成
https://cdn.jsdelivr.net/gh/jquery/jquery@3.2.1/src/core.min.js

- // 在末尾添加 / 以获取资源目录列表
https://cdn.jsdelivr.net/gh/jquery/jquery/
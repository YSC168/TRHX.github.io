---
title: Markdown 语法&技巧总结
categories: Markdown
tags: 
 - Markdown
 - 技巧
description: 在写博客的时候遇到的 Markdown 各种方法技巧或者语法记录下来，方便查阅【持续更新】
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/markdown.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---

**<font color=#4094C7>在写博客的时候，我们不希望都是千篇一律的没有色彩，多了解一些 Markdown 语法技巧有利于丰富我们的博客，看起来更有 feel ！</font>**

----------
# <font color=#FF0000> -- 插入图片 </font>

![车](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A03/01.jpg)

如果你使用 MarkdownPad 的话就比较方便，可以直接选择插入本地图片或者是网络图片，实质是通过以下代码实现的，小括号里面就是你的图片地址，中括号里面是图片的替代文字，比如上面的图片代码如下：
``` markdown
![车](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A03/01.jpg)
```

# <font color=#FF0000> -- 插入音乐 </font>

打开网页版[网易云音乐](https://music.163.com/)，选择你准备插入的音乐，点击生成外链播放器，前提是要有版权，不然是无法生成外链播放器的，选择好尺寸后，复制底下的HTML代码

然后将此HTML代码粘贴到你想要放的地方，可自行调节播放器的大小，其中 <font color=#FF0000>auto=1</font> 表示打开网页自动播放音乐，<font color=#FF0000>auto=0</font> 表示关闭自动播放音乐，比如See You Again (中英文版) - 罗艺恒这首歌曲代码如下：

``` html
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=32405683&auto=1&height=66"></iframe>
```
![02](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A03/02.png)
![03](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A03/03.png)

# <font color=#FF0000> -- 插入视频 </font>

高考毕业了我们为下一届的学弟学妹们录制高考加油视频，我担任后期制作，在这里就以该视频为例٩(๑❛ᴗ❛๑)۶，在腾讯视频播放页面找到分享按钮，复制该视频的通用代码（其他视频播放平台也一样）,粘贴到文章中对应位置即可，可根据情况调整视频播放器的大小
![04](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A03/04.png)

``` html
<iframe frameborder="0" width="840"  height="500" src="https://v.qq.com/txp/iframe/player.html?vid=x0643zvgtf7" allowFullScreen="true"></iframe>
```
<iframe frameborder="0" width="840"  height="500" src="https://v.qq.com/txp/iframe/player.html?vid=x0643zvgtf7" allowFullScreen="true"></iframe>

----------

# <center><font color=#FF0000>未完待续...... </font></center>

----------

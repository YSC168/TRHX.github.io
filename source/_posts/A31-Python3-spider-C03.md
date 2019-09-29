---
title: Python3 爬虫学习笔记 C03
tags:
  - 爬虫
  - Ajax
categories: 
  - Python3 学习笔记
  - 爬虫学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/spider.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 爬虫学习笔记第三章 ——【Ajax 数据爬取】</font></center>

<!--more-->

# <font color=#FF0000> 【3.1】Ajax 简介</font>
Ajax — Asynchronous Javascript And XML（异步 JavaScript 和 XML），是指一种创建交互式网页应用的网页开发技术。可以在不重新加载整个网页的情况下，对网页的某部分进行更新。

# <font color=#FF0000> 【3.2】解析真实地址提取</font>
以豆瓣电影动作片排行榜为例，地址为：https://movie.douban.com/typerank?type_name=%E5%8A%A8%E4%BD%9C&type=5&interval_id=100:90&action= ，首先使用常用方法来爬取电影信息：
```python
import requests

url = 'https://movie.douban.com/typerank?type_name=%E5%8A%A8%E4%BD%9C&type=5&interval_id=100:90&action='

headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.36 SE 2.X MetaSr 1.0"}
response = requests.get(url, headers=headers)

print(response.text)
```
得到的数据里面我们并没有找到电影相关信息：
![1](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A31/1.png)
再次分析页面，发现鼠标下滑的时候，页面不刷新，URL 也不变，但是会加载新数据，那么此处就运用了 Ajax，可以使用抓包工具或者浏览器控制台来捕获 Ajax 接口，获取其真实地址，XHR 是 Ajax 特殊的请求类型，返回的是 json 数据，利用浏览器控制台过滤 XHR，随便点击一条请求，可以看到其 Request URL，也就是真实地址，点击 Preview 就可以看到返回的 json 数据。
![2](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A31/2.png)
![3](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A31/3.png)
同样，我们可以使用 Fiddler 抓包软件抓取 Ajax 接口：
![4](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A31/4.png)
分析其真实地址为：https://movie.douban.com/j/chart/top_list?type=5&interval_id=100%3A90&action=&start=20&limit=20 ，多下滑几次，只有 start 参数发生了改变，观察变化可知：每一次页面将多出20个电影信息，start 为从第几个电影开始，由此就不难进行数据抓取了

代码：
```python
import requests

url = 'https://movie.douban.com/j/chart/top_list?type=5&interval_id=100%3A90&action=&'

page = int(input('请输入想要第几页的数据：'))
data = {
	'start': (page - 1)*20,
	'limit': '20',
}
headers = {
	'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.86 Safari/537.36',
}
response = requests.get(url, params=data, headers=headers)
print(response.text)
```
运行代码即可得到电影排行信息：
![5](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A31/5.png)

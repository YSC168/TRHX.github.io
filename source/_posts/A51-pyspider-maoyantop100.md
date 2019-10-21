---
title: Python3 爬虫实战 — 猫眼电影TOP100
tags:
  - 爬虫
  - 猫眼电影
categories: 
  - Python3 学习笔记
  - 爬虫实战
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/combat.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---

> 爬取时间：2019-09-23
> 爬取难度：★☆☆☆☆☆
> 请求链接：https://maoyan.com/board/4
> 爬取目标：猫眼 TOP100 的电影名称、排名、主演、上映时间、评分、封面图地址，数据保存为 CSV 文件
> 涉及知识：请求库 requests、解析库 lxml、Xpath 语法、CSV 文件储存
> 完整代码：https://github.com/TRHX/Python3-Spider-Practice/tree/master/maoyan-top100
> 其他爬虫实战代码合集（持续更新）：https://github.com/TRHX/Python3-Spider-Practice
> 爬虫实战专栏（持续更新）：https://itrhx.blog.csdn.net/article/category/9351278

---

<!--more-->

# <font color=#FF0000>【1x00】循环爬取网页模块</font>

观察猫眼电影TOP100榜，请求地址为：https://maoyan.com/board/4

每页展示10条电影信息，翻页观察 url 变化：

第一页：https://maoyan.com/board/4

第二页：https://maoyan.com/board/4?offset=10

第三页：https://maoyan.com/board/4?offset=20

一共有10页，利用一个 for 循环，从 0 到 100 每隔 10 取一个值拼接到 url，实现循环爬取每一页

```python
def index_page(number):
    url = 'https://maoyan.com/board/4?offset=%s' % number
    response = requests.get(url=url, headers=headers)
    return response.text

if __name__ == '__main__':
    for i in range(0, 100, 10):
        index = index_page(i)
```

---

# <font color=#FF0000>【2x00】解析模块</font>

定义一个页面解析函数 `parse_page()`，使用 lxml 解析库的 Xpath 方法依次提取电影排名（ranking）、电影名称（movie_name）、主演（performer）、上映时间（releasetime）、评分（score）、电影封面图 url（movie_img）

通过对主演部分的提取发现有多余的空格符和换行符，循环 performer 列表，使用 `strip()` 方法去除字符串头尾空格和换行符

电影评分分为整数部分和小数部分，依次提取两部分，循环遍历组成一个完整的评分

最后使用 `zip()` 函数，将所有提取的对象作为参数，将对象中对应的元素打包成一个个元组，然后返回由这些元组组成的列表

```python
def parse_page(content):
    tree = etree.HTML(content)
    # 电影排名
    ranking = tree.xpath("//dd/i/text()")
    # 电影名称
    movie_name = tree.xpath('//p[@class="name"]/a/text()')
    # 主演
    performer = tree.xpath("//p[@class='star']/text()")
    performer = [p.strip() for p in performer]
    # 上映时间
    releasetime = tree.xpath('//p[@class="releasetime"]/text()')
    # 评分
    score1 = tree.xpath('//p[@class="score"]/i[@class="integer"]/text()')
    score2 = tree.xpath('//p[@class="score"]/i[@class="fraction"]/text()')
    score = [score1[i] + score2[i] for i in range(min(len(score1), len(score2)))]
    # 电影封面图
    movie_img = tree.xpath('//img[@class="board-img"]/@data-src')
    return zip(ranking, movie_name, performer, releasetime, score, movie_img)
```

---

# <font color=#FF0000>【3x00】数据储存模块</font>

定义一个 `save_results()` 函数，将所有数据保存到 `maoyan.csv` 文件

```python
def save_results(result):
    with open('maoyan.csv', 'a') as fp:
        writer = csv.writer(fp)
        writer.writerow(result)
```

---

# <font color=#FF0000>【4x00】完整代码</font>

```python
# =============================================
# --*-- coding: utf-8 --*--
# @Time    : 2019-09-23
# @Author  : TRHX
# @Blog    : www.itrhx.com
# @CSDN    : https://blog.csdn.net/qq_36759224
# @FileName: maoyan.py
# @Software: PyCharm
# =============================================

import requests
from lxml import etree
import csv

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.142 Safari/537.36'
}


def index_page(number):
    url = 'https://maoyan.com/board/4?offset=%s' % number
    response = requests.get(url=url, headers=headers)
    return response.text


def parse_page(content):
    tree = etree.HTML(content)
    # 电影排名
    ranking = tree.xpath("//dd/i/text()")
    # 电影名称
    movie_name = tree.xpath('//p[@class="name"]/a/text()')
    # 主演
    performer = tree.xpath("//p[@class='star']/text()")
    performer = [p.strip() for p in performer]
    # 上映时间
    releasetime = tree.xpath('//p[@class="releasetime"]/text()')
    # 评分
    score1 = tree.xpath('//p[@class="score"]/i[@class="integer"]/text()')
    score2 = tree.xpath('//p[@class="score"]/i[@class="fraction"]/text()')
    score = [score1[i] + score2[i] for i in range(min(len(score1), len(score2)))]
    # 电影封面图
    movie_img = tree.xpath('//img[@class="board-img"]/@data-src')
    return zip(ranking, movie_name, performer, releasetime, score, movie_img)


def save_results(result):
    with open('maoyan.csv', 'a') as fp:
        writer = csv.writer(fp)
        writer.writerow(result)


if __name__ == '__main__':
	print('开始爬取数据...')
    for i in range(0, 100, 10):
        index = index_page(i)
        results = parse_page(index)
        for i in results:
            save_results(i)
	print('数据爬取完毕！')

```

---

# <font color=#FF0000>【4x00】数据截图</font>

<fancybox>
![01](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A51/01.png)
</fancybox>

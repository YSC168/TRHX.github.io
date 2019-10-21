---
title: Python3 爬虫实战 — 虎扑论坛步行街
tags:
  - 爬虫
  - 虎扑论坛
categories: 
  - Python3 学习笔记
  - 爬虫实战
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/combat.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---

> 爬取时间：2019-10-12
> 爬取难度：★★☆☆☆☆
> 请求链接：https://bbs.hupu.com/bxj
> 爬取目标：爬取虎扑论坛步行街的帖子，包含主题，作者，发布时间等，数据保存到 MongoDB 数据库
> 涉及知识：请求库 requests、解析库 Beautiful Soup、数据库 MongoDB 的操作
> 完整代码：https://github.com/TRHX/Python3-Spider-Practice/tree/master/hupu
> 其他爬虫实战代码合集（持续更新）：https://github.com/TRHX/Python3-Spider-Practice
> 爬虫实战专栏（持续更新）：https://itrhx.blog.csdn.net/article/category/9351278

---

<!--more-->

# <font color=#FF0000>【1x00】循环爬取网页模块</font>

观察虎扑论坛步行街分区，请求地址为：https://bbs.hupu.com/bxj

第一页：https://bbs.hupu.com/bxj

第二页：https://bbs.hupu.com/bxj-2

第三页：https://bbs.hupu.com/bxj-3

不难发现，每增加一页，只需要添加 `-页数` 参数即可，最后一页是第 50 页，因此可以利用 for 循环依次爬取，定义一个 `get_pages()` 函数，返回初始化 Beautiful Soup 的对象 page_soup，方便后面的解析函数调用

虽然一共有 50 页，但是当用户访问第 10 页以后的页面的时候，会要求登录虎扑，不然就没法查看，而且登录时会出现智能验证，所以程序只爬取前 10 页的数据

```python
def get_pages(page_url):
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.142 Safari/537.36'
    }
    response = requests.get(url=page_url, headers=headers)
    page_soup = BeautifulSoup(response.text, 'lxml')
    return page_soup

if __name__ == '__main__':
    for i in range(1, 11):
        url = 'https://bbs.hupu.com/bxj-' + str(i)
        soup = get_pages(url)

```

---

# <font color=#FF0000>【2x00】解析模块</font>

使用 Beautiful Soup 对网页各个信息进行提取，最后将这些信息放进一个列表里，然后调用列表的 `.append()` 方法，再将每条帖子的列表依次加到另一个新列表里，最终返回的是类似于如下形式的列表：

```python
[['帖子1', '作者1'], ['帖子2', '作者2'], ['帖子3', '作者3']]
```

这样做的目的是：方便 MongoDB 依次储存每一条帖子的信息

```python
def parse_pages(page_soup):
    data_list = []
    all_list = page_soup.find('ul', class_='for-list')
    post_list = all_list.find_all('li')
    # print(result_list)
    for post in post_list:
        # 帖子名称
        post_title = post.find('a', class_='truetit').text
        # print(post_title)
        # 帖子链接
        post_url = 'https://bbs.hupu.com' + post.find('a', class_='truetit')['href']
        # print(post_url)
        # 作者
        author = post.select('.author > a')[0].text
        # print(author)
        # 作者主页
        author_url = post.select('.author > a')[0]['href']
        # print(author_url)
        # 发布日期
        post_date = post.select('.author > a')[1].text
        # print(post_date)
        reply_view = post.find('span', class_='ansour').text
        # 回复数
        post_reply = reply_view.split('/')[0].strip()
        # print(post_reply)
        # 浏览量
        post_view = reply_view.split('/')[1].strip()
        # print(post_view)
        # 最后回复时间
        last_data = post.select('.endreply > a')[0].text
        # print(last_data)
        # 最后回复用户
        last_user = post.select('.endreply > span')[0].text
        # print(last_user)

        data_list.append([post_title, post_url, author, author_url, post_date, post_reply, post_view, last_data, last_user])

    # print(data_list)
    return data_list

```

---

# <font color=#FF0000>【3x00】MongoDB 数据储存模块</font>

首先使用 `MongoClient()` 方法，向其传入地址参数 host 和 端口参数 port，指定数据库为 `hupu`，集合为 `bxj`

将解析函数返回的列表传入到储存函数，依次循环该列表，对每一条帖子的信息进行提取并储存

```python
def mongodb(data_list):
    client = MongoClient('localhost', 27017)
    db = client.hupu
    collection = db.bxj
    for data in data_list:
        bxj = {
            '帖子名称': data[0],
            '帖子链接': data[1],
            '作者': data[2],
            '作者主页': data[3],
            '发布日期': str(data[4]),
            '回复数': data[5],
            '浏览量': data[6],
            '最后回复时间': str(data[7]),
            '最后回复用户': data[8]
        }
        collection.insert_one(bxj)

```

---

# <font color=#FF0000>【4x00】完整代码</font>

```python
# =============================================
# --*-- coding: utf-8 --*--
# @Time    : 2019-10-12
# @Author  : TRHX
# @Blog    : www.itrhx.com
# @CSDN    : https://blog.csdn.net/qq_36759224
# @FileName: hupu.py
# @Software: PyCharm
# =============================================

import requests
import time
import random
from pymongo import MongoClient
from bs4 import BeautifulSoup


def get_pages(page_url):
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.142 Safari/537.36'
    }
    response = requests.get(url=page_url, headers=headers)
    page_soup = BeautifulSoup(response.text, 'lxml')
    return page_soup


def parse_pages(page_soup):
    data_list = []
    all_list = page_soup.find('ul', class_='for-list')
    post_list = all_list.find_all('li')
    # print(result_list)
    for post in post_list:
        # 帖子名称
        post_title = post.find('a', class_='truetit').text
        # print(post_title)
        # 帖子链接
        post_url = 'https://bbs.hupu.com' + post.find('a', class_='truetit')['href']
        # print(post_url)
        # 作者
        author = post.select('.author > a')[0].text
        # print(author)
        # 作者主页
        author_url = post.select('.author > a')[0]['href']
        # print(author_url)
        # 发布日期
        post_date = post.select('.author > a')[1].text
        # print(post_date)
        reply_view = post.find('span', class_='ansour').text
        # 回复数
        post_reply = reply_view.split('/')[0].strip()
        # print(post_reply)
        # 浏览量
        post_view = reply_view.split('/')[1].strip()
        # print(post_view)
        # 最后回复时间
        last_data = post.select('.endreply > a')[0].text
        # print(last_data)
        # 最后回复用户
        last_user = post.select('.endreply > span')[0].text
        # print(last_user)

        data_list.append([post_title, post_url, author, author_url, post_date, post_reply, post_view, last_data, last_user])

    # print(data_list)
    return data_list


def mongodb(data_list):
    client = MongoClient('localhost', 27017)
    db = client.hupu
    collection = db.bxj
    for data in data_list:
        bxj = {
            '帖子名称': data[0],
            '帖子链接': data[1],
            '作者': data[2],
            '作者主页': data[3],
            '发布日期': str(data[4]),
            '回复数': data[5],
            '浏览量': data[6],
            '最后回复时间': str(data[7]),
            '最后回复用户': data[8]
        }
        collection.insert_one(bxj)


if __name__ == '__main__':
    for i in range(1, 11):
        url = 'https://bbs.hupu.com/bxj-' + str(i)
        soup = get_pages(url)
        result_list = parse_pages(soup)
        mongodb(result_list)
        print('第', i, '页数据爬取完毕！')
        time.sleep(random.randint(3, 10))
    print('前10页所有数据爬取完毕！')

```

---

# <font color=#FF0000>【5x00】数据截图</font>

一共爬取到 1180 条数据：

<fancybox>
![01](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A55/01.png)
</fancybox>

# <font color=#FF0000>【6x00】程序不足的地方</font>

程序只能爬取前 10 页的数据，因为虎扑论坛要求从第 11 页开始，必须登录账号才能查看，并且登录时会有智能验证，可以使用自动化测试工具 Selenium 模拟登录账号后再进行爬取。

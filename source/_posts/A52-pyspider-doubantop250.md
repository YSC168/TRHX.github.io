---
title: Python3 爬虫实战 — 豆瓣电影TOP250
tags:
  - 爬虫
  - 豆瓣电影
categories: 
  - Python3 学习笔记
  - 爬虫实战
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/combat.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---

> 爬取时间：2019-09-27
> 爬取难度：★★☆☆☆☆
> 请求链接：https://movie.douban.com/top250 以及每部电影详情页
> 爬取目标：爬取榜单上每一部电影详情页的数据，保存为 CSV 文件；下载所有电影海报到本地
> 涉及知识：请求库 requests、解析库 lxml、Xpath 语法、正则表达式、CSV 和二进制数据储存、列表操作
> 完整代码：https://github.com/TRHX/Python3-Spider-Practice/tree/master/douban-top250
> 其他爬虫实战代码合集（持续更新）：https://github.com/TRHX/Python3-Spider-Practice
> 爬虫实战专栏（持续更新）：https://itrhx.blog.csdn.net/article/category/9351278

---

<!--more-->

# <font color=#FF0000>【1x00】循环爬取网页模块</font>

观察豆瓣电影 Top 250，请求地址为：https://movie.douban.com/top250

每页展示25条电影信息，照例翻页观察 url 的变化：

第一页：https://movie.douban.com/top250

第二页：https://movie.douban.com/top250?start=25&filter=

第三页：https://movie.douban.com/top250?start=50&filter=

一共有10页，每次改变的是 start 的值，利用一个 for 循环，从 0 到 250 每隔 25 取一个值拼接到 url，实现循环爬取每一页，由于我们的目标是进入每一部电影的详情页，然后爬取详情页的内容，所以我们可以使用 Xpath 提取每一页每部电影详情页的 URL，将其赋值给 `m_urls`，并返回 `m_urls`，`m_urls` 是一个列表，列表元素就是电影详情页的 URL

```python
def index_pages(number):
    url = 'https://movie.douban.com/top250?start=%s&filter=' % number
    index_response = requests.get(url=url, headers=headers)
    tree = etree.HTML(index_response.text)
    m_urls = tree.xpath("//li/div/div/a/@href")
    return m_urls

if __name__ == '__main__':
    for i in range(0, 250, 25):
        movie_urls = index_pages(i)
```

---

# <font color=#FF0000>【2x00】解析模块</font>

定义一个解析函数 `parse_pages()`，利用 for 循环，依次提取 `index_pages()` 函数返回的列表中的元素，也就是每部电影详情页的 URL，将其传给解析函数进行解析

```python
def index_pages(number):
	expressions

def parse_pages(url):
	expressions

if __name__ == '__main__':
    for i in range(0, 250, 25):
        movie_urls = index_pages(i)
        for movie_url in movie_urls:
            results = parse_pages(movie_url)
```

详细看一下解析函数 `parse_pages()`，首先要对接收到的详情页 URL 发送请求，获取响应内容，然后再使用 Xpath 提取相关信息

```python
def parse_pages(url):
    movie_pages = requests.get(url=url, headers=headers)
    parse_movie = etree.HTML(movie_pages.text)
```

---

## <font color=#1BC3FB>【2x01】Xpath 解析排名、电影名、评分信息</font>

其中排名、电影名和评分信息是最容易匹配到的，直接使用 Xpath 语法就可以轻松解决：

```python
# 排名
ranking = parse_movie.xpath("//span[@class='top250-no']/text()")

# 电影名
name = parse_movie.xpath("//h1/span[1]/text()")

# 评分
score = parse_movie.xpath("//div[@class='rating_self clearfix']/strong/text()")
```

---

## <font color=#1BC3FB>【2x02】Xpath 解析参评人数</font>

接下来准备爬取有多少人参与了评价，分析一下页面：

<fancybox>
![01](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A52/01.png)
</fancybox>

如果只爬取这个 `<span>` 标签下的数字的话，没有任何提示信息，别人看了不知道是啥东西，所以把 `人评价` 这三个字也爬下来的话就比较好了，但是可以看到数字和文字不在同一个元素标签下，而且文字部分还有空格，要爬取的话就要把 `class="rating_people"` 的 `a` 标签下所有的 `text` 提取出来，然后再去掉空格：

```python
# 参评人数
# 匹配a节点
value = parse_movie.xpath("//a[@class='rating_people']")
# 提取a节点下所有文本
string = [value[0].xpath('string(.)')]
# 去除多余空格
number = [a.strip() for a in string]

# 此时 number = ['1617307人评价']
```

这样做太麻烦了，我们可以直接提取数字，得到一个列表，然后使用另一个带有提示信息的列表，将两个列表的元素合并，组成一个新列表，这个新列表的元素就是提示信息+人数
```python
# 参评人数
value = parse_movie.xpath("//span[@property='v:votes']/text()")
# 合并元素
number = [" ".join(['参评人数：'] + value)]

# 此时 number = ['参评人数：1617307']
```

---

## <font color=#1BC3FB>【2x03】正则表达式解析制片国家、语言</font>

接下来尝试爬取制片国家/地区、语言等信息：

<fancybox>
![02](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A52/02.png)
</fancybox>

分析页面可以观察到，制片国家/地区和语言结构比较特殊，没有特别的 class 或者 id 属性，所包含的层次关系也太复杂，所以这里为了简便，直接采用正则表达式来匹配信息，就没有那么复杂了：

```python
# 制片国家/地区
value = re.findall('<span class="pl">制片国家/地区:</span>(.*?)<br/>', movie_pages.text)
country = [" ".join(['制片国家:'] + value)]

# 语言
value = re.findall('<span class="pl">语言:</span>(.*?)<br/>', movie_pages.text)
language = [" ".join(['语言:'] + value)]
```

---

# <font color=#FF0000>【3x00】返回解析数据</font>

其他剩下的信息皆可利用以上方法进行提取，所有信息提取完毕，最后使用 `zip()` 函数，将所有提取的对象作为参数，将对象中对应的元素打包成一个个元组，然后返回由这些元组组成的列表

```python
return zip(ranking, name, score, number, types, country, language, date, time, other_name, director, screenwriter, performer, m_url, imdb_url)
```

---

# <font color=#FF0000>【4x00】数据储存模块</font>

定义一个数据保存函数 `save_results()`

```python
def save_results(data):
    with open('douban.csv', 'a', encoding="utf-8-sig") as fp:
        writer = csv.writer(fp)
        writer.writerow(data)
```

注意：编码方式要设置为 `utf-8-sig`，如果设置为 `utf-8`，则文件会乱码，不设置编码，则可能会报一下类似错误：

```python
UnicodeEncodeError: 'gbk' codec can't encode character '\ub3c4' in position 9: illegal multibyte sequence
```

可以看到错误出现在 `\ub3c4` 上，将该 Unicode 编码转换为中文为 `도`，发现正是排名第 19 的电影：熔炉 도가니，因为标题有韩文，所以在储存为 CSV 文件时会报编码错误，而将编码设置为 `utf-8-sig` 就不会报错，具体原因参见：[《Python 中文日文汉字乱码处理utf-8-sig》](https://www.jianshu.com/p/d9b25fb0a098)

接下来是保存电影的海报到本地：

```python
# 保存电影海报
poster = parse_movie.xpath("//div[@id='mainpic']/a/img/@src")
response = requests.get(poster[0])
name2 = re.sub(r'[A-Za-z\:\s]', '', name[0])
poster_name = str(ranking[0]) + ' - ' + name2 + '.jpg'
dir_name = 'douban_poster'
if not os.path.exists(dir_name):
    os.mkdir(dir_name)
poster_path = dir_name + '/' + poster_name
with open(poster_path, "wb")as f:
    f.write(response.content)
```

解析电影详情页，使用 Xpath 提取海报的 URL，向该 URL 发送请求

图片以 `排名+电影名.jpg` 的方式命名，但是由于提取的电影名部分含有特殊字符，比如排名第 10 的电影：忠犬八公的故事 Hachi: A Dog's Tale，其中有个冒号，而 Windows 文件命名是不能包含这些字符的，所以我们直接去除电影名包含的英文字符、空白字符、特殊字符，只留下中文，代码实现： `name2 = re.sub(r'[A-Za-z\:\s]', '', name[0])`

定义一个文件夹名称 `douban_poster`，利用 `os` 模块判断当前是否存在该文件夹，若不存在就创建一个

最后以二进制形式保存海报到当前目录的 douban_poster 文件夹下

---

# <font color=#FF0000>【5x00】完整代码</font>

```python
# =============================================
# --*-- coding: utf-8 --*--
# @Time    : 2019-09-27
# @Author  : TRHX
# @Blog    : www.itrhx.com
# @CSDN    : https://blog.csdn.net/qq_36759224
# @FileName: douban.py
# @Software: PyCharm
# =============================================

import requests
from lxml import etree
import csv
import re
import time
import os

headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.142 Safari/537.36'}


def index_pages(number):
    url = 'https://movie.douban.com/top250?start=%s&filter=' % number
    index_response = requests.get(url=url, headers=headers)
    tree = etree.HTML(index_response.text)
    m_urls = tree.xpath("//li/div/div/a/@href")
    return m_urls


def parse_pages(url):
    movie_pages = requests.get(url=url, headers=headers)
    parse_movie = etree.HTML(movie_pages.text)

    # 排名
    ranking = parse_movie.xpath("//span[@class='top250-no']/text()")

    # 电影名
    name = parse_movie.xpath("//h1/span[1]/text()")

    # 评分
    score = parse_movie.xpath("//div[@class='rating_self clearfix']/strong/text()")

    # 参评人数
    value = parse_movie.xpath("//span[@property='v:votes']/text()")
    number = [" ".join(['参评人数：'] + value)]
    # value = parse_movie.xpath("//a[@class='rating_people']")
    # string = [value[0].xpath('string(.)')]
    # number = [a.strip() for a in string]
    # print(number)

    # 类型
    value = parse_movie.xpath("//span[@property='v:genre']/text()")
    types = [" ".join(['类型：'] + value)]

    # 制片国家/地区
    value = re.findall('<span class="pl">制片国家/地区:</span>(.*?)<br/>', movie_pages.text)
    country = [" ".join(['制片国家:'] + value)]

    # 语言
    value = re.findall('<span class="pl">语言:</span>(.*?)<br/>', movie_pages.text)
    language = [" ".join(['语言:'] + value)]

    # 上映时期
    value = parse_movie.xpath("//span[@property='v:initialReleaseDate']/text()")
    date = [" ".join(['上映日期：'] + value)]

    # 片长
    value = parse_movie.xpath("//span[@property='v:runtime']/text()")
    time = [" ".join(['片长：'] + value)]

    # 又名
    value = re.findall('<span class="pl">又名:</span>(.*?)<br/>', movie_pages.text)
    other_name = [" ".join(['又名:'] + value)]

    # 导演
    value = parse_movie.xpath("//div[@id='info']/span[1]/span[@class='attrs']/a/text()")
    director = [" ".join(['导演:'] + value)]

    # 编剧
    value = parse_movie.xpath("//div[@id='info']/span[2]/span[@class='attrs']/a/text()")
    screenwriter = [" ".join(['编剧:'] + value)]

    # 主演
    value = parse_movie.xpath("//div[@id='info']/span[3]")
    performer = [value[0].xpath('string(.)')]

    # URL
    m_url = ['豆瓣链接：' + movie_url]

    # IMDb链接
    value = parse_movie.xpath("//div[@id='info']/a/@href")
    imdb_url = [" ".join(['IMDb链接：'] + value)]

    # 保存电影海报
    poster = parse_movie.xpath("//div[@id='mainpic']/a/img/@src")
    response = requests.get(poster[0])
    name2 = re.sub(r'[A-Za-z\:\s]', '', name[0])
    poster_name = str(ranking[0]) + ' - ' + name2 + '.jpg'
    dir_name = 'douban_poster'
    if not os.path.exists(dir_name):
        os.mkdir(dir_name)
    poster_path = dir_name + '/' + poster_name
    with open(poster_path, "wb")as f:
        f.write(response.content)

    return zip(ranking, name, score, number, types, country, language, date, time, other_name, director, screenwriter, performer, m_url, imdb_url)


def save_results(data):
    with open('douban.csv', 'a', encoding="utf-8-sig") as fp:
        writer = csv.writer(fp)
        writer.writerow(data)


if __name__ == '__main__':
    num = 0
    for i in range(0, 250, 25):
        movie_urls = index_pages(i)
        for movie_url in movie_urls:
            results = parse_pages(movie_url)
            for result in results:
                num += 1
                save_results(result)
                print('第' + str(num) + '条电影信息保存完毕！')
                time.sleep(3)

```

---

# <font color=#FF0000>【6x00】数据截图</font>

<fancybox>
![03](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A52/03.png)
</fancybox>

<fancybox>
![04](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A52/04.png)
</fancybox>

---

# <font color=#FF0000>【7x00】程序不足的地方</font>

程序不足的地方：豆瓣电影有反爬机制，当程序爬取到大约 150 条数据的时候，IP 就会被封掉，第二天 IP 才会解封，可以考虑综合使用多个代理、多个 User-Agent、随机时间暂停等方法进行爬取

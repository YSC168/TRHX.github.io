---
title: Python3 爬虫实战 — 58同城武汉出租房【加密字体对抗】
tags:
  - 爬虫
  - 58同城
categories: 
  - Python3 学习笔记
  - 爬虫实战
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/combat.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---

> 爬取时间：2019-10-21
> 爬取难度：★★★☆☆☆
> 请求链接：https://wh.58.com/chuzu/
> 爬取目标：58同城武汉出租房的所有信息
> 涉及知识：网站加密字体的攻克、请求库 requests、解析库 Beautiful Soup、数据库 MySQL 的操作
> 完整代码：https://github.com/TRHX/Python3-Spider-Practice/tree/master/58tongcheng
> 其他爬虫实战代码合集（持续更新）：https://github.com/TRHX/Python3-Spider-Practice
> 爬虫实战专栏（持续更新）：https://itrhx.blog.csdn.net/article/category/9351278

---

<!--more-->

# <font color=#ff0000>【1x00】加密字体攻克思路</font>

F12 打开调试模板，通过页面分析，可以观察到，网站里面凡是涉及到有数字的地方，都是显示为乱码，这种情况就是字体加密了，那么是通过什么手段实现字体加密的呢？

CSS 中有一个 `@font-face` 规则，它允许为网页指定在线字体，也就是说可以引入自定义字体，这个规则本意是用来消除对电脑字体的依赖，现在不少网站也利用这个规则来实现反爬

右侧可以看到网站用的字体，其他的都是常见的微软雅黑，宋体等，但是有一个特殊的：`fangchan-secret` ，不难看出这应该就是58同城的自定义字体了

<fancybox>
![01](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A58/01.png)
</fancybox>

我们通过控制台看到的乱码事实上是由于 unicode 编码导致，查看网页源代码，我们才能看到他真正的编码信息

<fancybox>
![02](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A58/02.png)
</fancybox>

要攻克加密字体，那么我们肯定要分析他的字体文件了，先想办法得到他的加密字体文件，同样查看源代码，在源代码中搜索 `fangchan-secret` 的字体信息

<fancybox>
![03](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A58/03.png)
</fancybox>

选中的蓝色部分就是 base64 编码的加密字体字符串了，我们将其解码成二进制编码，写进 `.woff` 的字体文件，这个过程可以通过以下代码实现：

```python
import requests
import base64

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.142 Safari/537.36'
}

url = 'https://wh.58.com/chuzu/'

response = requests.get(url=url, headers=headers)
# 匹配 base64 编码的加密字体字符串
base64_string = response.text.split("base64,")[1].split("'")[0].strip()
# 将 base64 编码的字体字符串解码成二进制编码
bin_data = base64.decodebytes(base64_string.encode())
# 保存为字体文件
with open('58font.woff', 'wb') as f:
    f.write(bin_data)

```

得到字体文件后，我们可以通过 <font color=#ff0000>FontCreator</font> 这个软件来看看字体对应的编码是什么：

<fancybox>
![04](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A58/04.png)
</fancybox>

观察我们在网页源代码中看到的编码：类似于 `&#x9fa4;`、`&#x9f92;`

对比字体文件对应的编码：类似于 `uni9FA4`、`nui9F92`

可以看到除了前面三个字符不一样以外，后面的字符都是一样的，只不过英文大小写有所差异

现在我们可能会想到，直接把编码替换成对应的数字不就OK了？然而并没有这么简单

尝试刷新一下网页，可以观察到 base64 编码的加密字体字符串会改变，也就是说编码和数字并不是一一对应的，再次获取几个字体文件，通过对比就可以看出来

<fancybox>
![05](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A58/05.png)
</fancybox>


可以看到，虽然每次数字对应的编码都不一样，但是编码总是这10个，是不变的，那么编码与数字之间肯定存在某种对应关系，，我们可以将字体文件转换为 xml 文件来观察其中的对应关系，改进原来的代码即可实现转换功能：

```python
import requests
import base64
from fontTools.ttLib import TTFont

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.142 Safari/537.36'
}

url = 'https://wh.58.com/chuzu/'

response = requests.get(url=url, headers=headers)
# 匹配 base64 编码的加密字体字符串
base64_string = response.text.split("base64,")[1].split("'")[0].strip()
# 将 base64 编码的字体字符串解码成二进制编码
bin_data = base64.decodebytes(base64_string.encode())
# 保存为字体文件
with open('58font.woff', 'wb') as f:
    f.write(bin_data)
# 获取字体文件，将其转换为xml文件
font = TTFont('58font.woff')
font.saveXML('58font.xml')

```

打开 `58font.xml` 文件并分析，在 `<cmap>` 标签内可以看到熟悉的类似于 `0x9476`、`0x958f` 的编码，其后四位字符恰好是网页字体的加密编码，可以看到每一个编码后面都对应了一个 `glyph` 开头的编码

将其与 `58font.woff` 文件对比，可以看到 code 为 `0x958f` 这个编码对应的是数字 `3`，对应的 name 编码是 `glyph00004`

<fancybox>
![06](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A58/06.png)
</fancybox>

我们再次获取一个字体文件作为对比分析

<fancybox>
![07](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A58/07.png)
</fancybox>

依然是 `0x958f` 这个编码，两次对应的 name 分别是 `glyph00004` 和 `glyph00007`，两次对应的数字分别是 `3` 和 `6`，那么结论就来了，每次发送请求，code 对应的 name 会随机发生变化，而 name 对应的数字不会发生变化，`glyph00001` 对应数字 `0`、`glyph00002` 对应数字 `1`，以此类推

那么以 `glyph` 开头的编码是如何对应相应的数字的呢？在 xml 文件里面，每个编码都有一个 `TTGlyph` 的标签，标签里面是一行一行的类似于 x,y 坐标的东西，这个其实就是用来绘制字体的，用 matplotlib 根据坐标画个图，就可以看到是一个数字

<fancybox>
![08](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A58/08.png)
</fancybox>

此时，我们就知道了编码与数字的对应关系，下一步，我们可以查找 xml 文件里，编码对应的 name 的值，也就是以 `glyph` 开头的编码，然后返回其对应的数字，再替换掉网页源代码里的编码，就能成功获取到我们需要的信息了！

总结一下攻克加密字体的大致思路：

- 分析网页，找到对应的加密字体文件

- 如果引用的加密字体是一个 base64 编码的字符串，则需要转换成二进制并保存到 woff 字体文件中

- 将字体文件转换成 xml 文件

- 用 FontCreator 软件观察字体文件，结合 xml 文件，分析其编码与真实字体的关系

- 搞清楚编码与字体的关系后，想办法将编码替换成正常字体 

---

# <font color=#ff0000>【2x00】思维导图</font>

<fancybox>
![09](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A58/09.png)
</fancybox>

---

# <font color=#ff0000>【3x00】加密字体处理模块</font>

## <font color=#1BC3FB>【3x01】获取字体文件并转换为xml文件</font>

```python
def get_font(page_url, page_num):
    response = requests.get(url=page_url, headers=headers)
    # 匹配 base64 编码的加密字体字符串
    base64_string = response.text.split("base64,")[1].split("'")[0].strip()
    # print(base64_string)
    # 将 base64 编码的字体字符串解码成二进制编码
    bin_data = base64.decodebytes(base64_string.encode())
    # 保存为字体文件
    with open('58font.woff', 'wb') as f:
        f.write(bin_data)
    print('第' + str(page_num) + '次访问网页，字体文件保存成功！')
    # 获取字体文件，将其转换为xml文件
    font = TTFont('58font.woff')
    font.saveXML('58font.xml')
    print('已成功将字体文件转换为xml文件！')
    return response.text

```

由主函数传入要发送请求的 url，利用字符串的 `split()` 方法，匹配 base64 编码的加密字体字符串，利用 `base64` 模块的 `base64.decodebytes()` 方法，将 base64 编码的字体字符串解码成二进制编码并保存为字体文件，利用 `FontTools` 库，将字体文件转换为 xml 文件

---

## <font color=#1BC3FB>【3x02】将加密字体编码与真实字体进行匹配</font>

```python
def find_font():
    # 以glyph开头的编码对应的数字
    glyph_list = {
        'glyph00001': '0',
        'glyph00002': '1',
        'glyph00003': '2',
        'glyph00004': '3',
        'glyph00005': '4',
        'glyph00006': '5',
        'glyph00007': '6',
        'glyph00008': '7',
        'glyph00009': '8',
        'glyph00010': '9'
    }
    # 十个加密字体编码
    unicode_list = ['0x9476', '0x958f', '0x993c', '0x9a4b', '0x9e3a', '0x9ea3', '0x9f64', '0x9f92', '0x9fa4', '0x9fa5']
    num_list = []
    # 利用xpath语法匹配xml文件内容
    font_data = etree.parse('./58font.xml')
    for unicode in unicode_list:
        # 依次循环查找xml文件里code对应的name
        result = font_data.xpath("//cmap//map[@code='{}']/@name".format(unicode))[0]
        # print(result)
        # 循环字典的key，如果code对应的name与字典的key相同，则得到key对应的value
        for key in glyph_list.keys():
            if key == result:
                num_list.append(glyph_list[key])
    print('已成功找到编码所对应的数字！')
    # print(num_list)
    # 返回value列表
    return num_list

```

由前面的分析，我们知道 name 的值（即以 glyph 开头的编码）对应的数字是固定的，`glyph00001` 对应数字 `0`、`glyph00002` 对应数字 `1`，以此类推，所以可以将其构造成为一个字典 `glyph_list`

同样将十个 code（即类似于 `0x9476` 的加密字体编码）构造成一个列表

循环查找这十个 `code` 在 xml 文件里对应的 `name` 的值，然后将 `name` 的值与字典文件的 `key` 值进行对比，如果两者值相同，则获取这个 `key` 的 `value` 值，最终得到的列表 `num_list`，里面的元素就是 `unicode_list` 列表里面每个加密字体的真实值

---

## <font color=#1BC3FB>【3x03】替换掉网页中所有的加密字体编码</font>

```python
def replace_font(num, page_response):
    # 9476 958F 993C 9A4B 9E3A 9EA3 9F64 9F92 9FA4 9FA5
    result = page_response.replace('&#x9476;', num[0]).replace('&#x958f;', num[1]).replace('&#x993c;', num[2]).replace('&#x9a4b;', num[3]).replace('&#x9e3a;', num[4]).replace('&#x9ea3;', num[5]).replace('&#x9f64;', num[6]).replace('&#x9f92;', num[7]).replace('&#x9fa4;', num[8]).replace('&#x9fa5;', num[9])
    print('已成功将所有加密字体替换！')
    return result

```

传入由上一步 `find_font()` 函数得到的真实字体的列表，利用 `replace()` 方法，依次将十个加密字体编码替换掉

---

# <font color=#FF0000>【4x00】租房信息提取模块</font>

```python
def parse_pages(pages):
    num = 0
    soup = BeautifulSoup(pages, 'lxml')
    # 查找到包含所有租房的li标签
    all_house = soup.find_all('li', class_='house-cell')
    for house in all_house:
        # 标题
        title = house.find('a', class_='strongbox').text.strip()
        # print(title)

        # 价格
        price = house.find('div', class_='money').text.strip()
        # print(price)

        # 户型和面积
        layout = house.find('p', class_='room').text.replace(' ', '')
        # print(layout)

        # 楼盘和地址
        address = house.find('p', class_='infor').text.replace(' ', '').replace('\n', '')
        # print(address)

        # 如果存在经纪人
        if house.find('div', class_='jjr'):
            agent = house.find('div', class_='jjr').text.replace(' ', '').replace('\n', '')
        # 如果存在品牌公寓
        elif house.find('p', class_='gongyu'):
            agent = house.find('p', class_='gongyu').text.replace(' ', '').replace('\n', '')
        # 如果存在个人房源
        else:
            agent = house.find('p', class_='geren').text.replace(' ', '').replace('\n', '')
        # print(agent)

        data = [title, price, layout, address, agent]
        save_to_mysql(data)
        num += 1
        print('第' + str(num) + '条数据爬取完毕，暂停3秒！')
        time.sleep(3)

```

利用 BeautifulSoup 解析库很容易提取到相关信息，这里要注意的是，租房信息来源分为三种：经纪人、品牌公寓和个人房源，这三个的元素节点也不一样，因此匹配的时候要注意

<fancybox>
![10](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A58/10.png)
</fancybox>

---

# <font color=#FF0000>【5x00】MySQL数据储存模块</font>

## <font color=#1BC3FB>【5x01】创建MySQL数据库的表</font>

```python
def create_mysql_table():
    db = pymysql.connect(host='localhost', user='root', password='000000', port=3306, db='58tc_spiders')
    cursor = db.cursor()
    sql = 'CREATE TABLE IF NOT EXISTS 58tc_data (title VARCHAR(255) NOT NULL, price VARCHAR(255) NOT NULL, layout VARCHAR(255) NOT NULL, address VARCHAR(255) NOT NULL, agent VARCHAR(255) NOT NULL)'
    cursor.execute(sql)
    db.close()

```

首先指定数据库为 58tc_spiders，需要事先使用 MySQL 语句创建，也可以通过 MySQL Workbench 手动创建

然后使用 SQL 语句创建 一个表：58tc_data，表中包含 title、price、layout、address、agent 五个字段，类型都为 varchar

此创建表的操作也可以事先手动创建，手动创建后就不需要此函数了

---

## <font color=#1BC3FB>【5x02】将数据储存到MySQL数据库</font>

```python
def save_to_mysql(data):
    db = pymysql.connect(host='localhost', user='root', password='000000', port=3306, db='58tc_spiders')
    cursor = db.cursor()
    sql = 'INSERT INTO 58tc_data(title, price, layout, address, agent) values(%s, %s, %s, %s, %s)'
    try:
        cursor.execute(sql, (data[0], data[1], data[2], data[3], data[4]))
        db.commit()
    except:
        db.rollback()
    db.close()

```

`commit()` 方法的作用是实现数据插入，是真正将语句提交到数据库执行的方法，使用 `try except` 语句实现异常处理，如果执行失败，则调用 `rollback()` 方法执行数据回滚，保证原数据不被破坏

---

# <font color=#FF0000>【6x00】完整代码</font>

```python
# =============================================
# --*-- coding: utf-8 --*--
# @Time    : 2019-10-21
# @Author  : TRHX
# @Blog    : www.itrhx.com
# @CSDN    : https://blog.csdn.net/qq_36759224
# @FileName: 58tongcheng.py
# @Software: PyCharm
# =============================================

import requests
import time
import random
import base64
import pymysql
from lxml import etree
from bs4 import BeautifulSoup
from fontTools.ttLib import TTFont

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.142 Safari/537.36'
}


# 获取字体文件并转换为xml文件
def get_font(page_url, page_num):
    response = requests.get(url=page_url, headers=headers)
    # 匹配 base64 编码的加密字体字符串
    base64_string = response.text.split("base64,")[1].split("'")[0].strip()
    # print(base64_string)
    # 将 base64 编码的字体字符串解码成二进制编码
    bin_data = base64.decodebytes(base64_string.encode())
    # 保存为字体文件
    with open('58font.woff', 'wb') as f:
        f.write(bin_data)
    print('第' + str(page_num) + '次访问网页，字体文件保存成功！')
    # 获取字体文件，将其转换为xml文件
    font = TTFont('58font.woff')
    font.saveXML('58font.xml')
    print('已成功将字体文件转换为xml文件！')
    return response.text


# 将加密字体编码与真实字体进行匹配
def find_font():
    # 以glyph开头的编码对应的数字
    glyph_list = {
        'glyph00001': '0',
        'glyph00002': '1',
        'glyph00003': '2',
        'glyph00004': '3',
        'glyph00005': '4',
        'glyph00006': '5',
        'glyph00007': '6',
        'glyph00008': '7',
        'glyph00009': '8',
        'glyph00010': '9'
    }
    # 十个加密字体编码
    unicode_list = ['0x9476', '0x958f', '0x993c', '0x9a4b', '0x9e3a', '0x9ea3', '0x9f64', '0x9f92', '0x9fa4', '0x9fa5']
    num_list = []
    # 利用xpath语法匹配xml文件内容
    font_data = etree.parse('./58font.xml')
    for unicode in unicode_list:
        # 依次循环查找xml文件里code对应的name
        result = font_data.xpath("//cmap//map[@code='{}']/@name".format(unicode))[0]
        # print(result)
        # 循环字典的key，如果code对应的name与字典的key相同，则得到key对应的value
        for key in glyph_list.keys():
            if key == result:
                num_list.append(glyph_list[key])
    print('已成功找到编码所对应的数字！')
    # print(num_list)
    # 返回value列表
    return num_list


# 替换掉网页中所有的加密字体编码
def replace_font(num, page_response):
    # 9476 958F 993C 9A4B 9E3A 9EA3 9F64 9F92 9FA4 9FA5
    result = page_response.replace('&#x9476;', num[0]).replace('&#x958f;', num[1]).replace('&#x993c;', num[2]).replace('&#x9a4b;', num[3]).replace('&#x9e3a;', num[4]).replace('&#x9ea3;', num[5]).replace('&#x9f64;', num[6]).replace('&#x9f92;', num[7]).replace('&#x9fa4;', num[8]).replace('&#x9fa5;', num[9])
    print('已成功将所有加密字体替换！')
    return result


# 提取租房信息
def parse_pages(pages):
    num = 0
    soup = BeautifulSoup(pages, 'lxml')
    # 查找到包含所有租房的li标签
    all_house = soup.find_all('li', class_='house-cell')
    for house in all_house:
        # 标题
        title = house.find('a', class_='strongbox').text.strip()
        # print(title)

        # 价格
        price = house.find('div', class_='money').text.strip()
        # print(price)

        # 户型和面积
        layout = house.find('p', class_='room').text.replace(' ', '')
        # print(layout)

        # 楼盘和地址
        address = house.find('p', class_='infor').text.replace(' ', '').replace('\n', '')
        # print(address)

        # 如果存在经纪人
        if house.find('div', class_='jjr'):
            agent = house.find('div', class_='jjr').text.replace(' ', '').replace('\n', '')
        # 如果存在品牌公寓
        elif house.find('p', class_='gongyu'):
            agent = house.find('p', class_='gongyu').text.replace(' ', '').replace('\n', '')
        # 如果存在个人房源
        else:
            agent = house.find('p', class_='geren').text.replace(' ', '').replace('\n', '')
        # print(agent)

        data = [title, price, layout, address, agent]
        save_to_mysql(data)
        num += 1
        print('第' + str(num) + '条数据爬取完毕，暂停3秒！')
        time.sleep(3)


# 创建MySQL数据库的表：58tc_data
def create_mysql_table():
    db = pymysql.connect(host='localhost', user='root', password='000000', port=3306, db='58tc_spiders')
    cursor = db.cursor()
    sql = 'CREATE TABLE IF NOT EXISTS 58tc_data (title VARCHAR(255) NOT NULL, price VARCHAR(255) NOT NULL, layout VARCHAR(255) NOT NULL, address VARCHAR(255) NOT NULL, agent VARCHAR(255) NOT NULL)'
    cursor.execute(sql)
    db.close()


# 将数据储存到MySQL数据库
def save_to_mysql(data):
    db = pymysql.connect(host='localhost', user='root', password='000000', port=3306, db='58tc_spiders')
    cursor = db.cursor()
    sql = 'INSERT INTO 58tc_data(title, price, layout, address, agent) values(%s, %s, %s, %s, %s)'
    try:
        cursor.execute(sql, (data[0], data[1], data[2], data[3], data[4]))
        db.commit()
    except:
        db.rollback()
    db.close()


if __name__ == '__main__':
    create_mysql_table()
    print('MySQL表58tc_data创建成功！')
    for i in range(1, 71):
        url = 'https://wh.58.com/chuzu/pn' + str(i) + '/'
        response = get_font(url, i)
        num_list = find_font()
        pro_pages = replace_font(num_list, response)
        parse_pages(pro_pages)
        print('第' + str(i) + '页数据爬取完毕！')
        time.sleep(random.randint(3, 60))
    print('所有数据爬取完毕！')

```

---

# <font color=#FF0000>【7x00】数据截图</font>

<fancybox>
![11](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A58/11.png)
</fancybox>

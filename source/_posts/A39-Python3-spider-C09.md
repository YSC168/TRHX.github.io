---
title: Python3 爬虫学习笔记 C09
tags:
  - 爬虫
  - 文件储存
categories: 
  - Python3 学习笔记
  - 爬虫学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/spider.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 爬虫学习笔记第九章 —— 【文件储存】</font></center>

<!--more-->

用解析器解析出数据之后，还需要对数据进行保存。保存的形式多种多样，最简单的形式是直接保存为文本文件，如 TXT、JSON、CSV 等。

# <font color=#ff0000>【9.1】TXT 文本存储</font>
TXT 文本存储的优点：操作非常简单，TXT 文本几乎兼容任何平台；缺点：不利于检索。

## <font color=#ff0000>【9.1.1】基本示例</font>

```python
import requests
from lxml import etree

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.86 Safari/537.36',
}
url = "https://blog.csdn.net/qq_36759224"

request = requests.get(url, headers=headers)
tree = etree.HTML(request.text)
title_list = tree.xpath('//h4/a/text()')
for title in title_list:
    with open('blog.txt', 'a', encoding='utf8') as fp:
        fp.write(title)
```

代码实现了我的 CSDN 博客首页所有博文标题的爬取，利用 requests 请求库发送请求，获取响应，用 XPath 获取每一篇博文的标题，然后写入 blog.txt 文件中：

```text
            帝都的凛冬
                最新屏蔽 CSDN 广告方法，专注阅读学习！      
                使用Github Pages和Hexo搭建自己的独立博客【超级详细的小白教程】      
                Python3 爬虫学习笔记 C08【解析库 Beautiful Soup】      
                Python3 爬虫学习笔记 C07 【解析库 lxml】      
                Python3 爬虫学习笔记 C06 【正则表达式】      
                Python3 爬虫学习笔记 C05 【Selenium + 无界面浏览器】      
                Python3 已经安装相关库，Pycharm 仍然报错 ModuleNotFoundError: No module named 'xxxxxx' 的解决办法      
                Windows/Android/iOS 等常见 User-Agent 大全      
                Selenium 显式等待条件及其含义      
                Python3 爬虫学习笔记 C04 【自动化测试工具 Selenium】      
                Python3 爬虫学习笔记 C03 【Ajax 数据爬取】      
                Python3 爬虫学习笔记 C02 【基本库 requests 的使用】      
                Python3 爬虫学习笔记 C01 【基本库 urllib 的使用】      
                利用官方支持为基于GitHub Pages的Hexo博客启用HTTPS      
                光学字符识别 Tesseract-OCR 的下载、安装和基本用法      
                Github+jsDelivr+PicGo 打造稳定快速、高效免费图床      
                利用Cloudflare为基于GitHub Pages的Hexo博客添加HTTPS支持      
                Python 中 if __name__ == '__main__': 的理解      
                Hexo 博客本地预览报错：Error: listen EADDRINUSE 0.0.0.0:4000      
                谷歌浏览器检查更新时出错：无法启动更新检查（错误代码为 3: 0x80080005 -- system level）      
```

## <font color=#ff0000>【9.1.2】打开方式</font>

open() 方法的第二个参数为打开方式，不同的打开方式如下：

| 读写方式 | 可否读写              | 若文件不存在 | 写入方式 |
| -------- | --------------------- | ------------ | -------- |
| r        | 读取                  | 报错         | 不可写入 |
| rb       | 以二进制方式读取      | 报错         | 不可写入 |
| r+       | 读取 + 写入             | 报错         | 覆盖写入 |
| rb+      | 以二进制方式读取+写入 | 报错         | 覆盖写入 |
| w        | 写入                  | 创建         | 覆盖写入 |
| wb       | 以二进制方式写入      | 创建         | 覆盖写入 |
| w+       | 读取 + 写入             | 创建         | 覆盖写入 |
| wb+      | 以二进制方式读取+写入 | 创建         | 覆盖写入 |
| a        | 写入                  | 创建         | 附加写入 |
| ab       | 以二进制方式写入      | 创建         | 附加写入 |
| a+       | 读取 + 写入       | 创建           | 附加写入 |
| ab+     | 以二进制方式读取+写入 | 创建 | 附加写入 |

# <font color=#ff0000>【9.2】JSON 文件存储</font>

JSON，全称为 JavaScript Object Notation, 即 JavaScript 对象标记，它通过对象和数组的组合来表示数据，构造简洁但是结构化程度非常高，是一种轻量级的数据交换格式。

## <font color=#ff0000>【9.2.1】对象和数组</font>

在 JavaScript 语言中，一切都是对象。因此，任何支持的类型都可以通过 JSON 来表示，例如字符串、数字、对象、数组等，但是对象和数组是比较特殊且常用的两种类型

- 对象：它在 JavaScript 中是使用花括号 {} 包裹起来的内容，数据结构为 {key1：value1, key2：value2, ...} 的键值对结构。在面向对象的语言中，key 为对象的属性，value 为对应的值。键名可以使用整数和字符串来表示。值的类型可以是任意类型。

- 数组：数组在 JavaScript 中是方括号 [] 包裹起来的内容，数据结构为 ["java", "javascript", "vb", ...] 的索引结构。在 JavaScript 中，数组是一种比较特殊的数据类型，它也可以像对象那样使用键值对，但还是索引用得多。同样，值的类型可以是任意类型。

示例：一个 JSON 对象

```json
[{
    "name": "TRH",
    "gender": "male",
    "birthday": "1999-01-25"
}, {
     "name": "XXX",
    "gender": "female",
    "birthday": "1999-10-18"
}]
```

## <font color=#ff0000>【9.2.2】读取 JSON</font>
Python 里面的 JSON 库可以实现对 JSON 文件的读写操作，调用 JSON 库的 loads 方法将 JSON 文本字符串转为 JSON 对象、 dumps() 方法将 JSON 对象转为文本字符串

```python
import json

str = '''
[{
    "name": "TRH",
    "gender": "male",
    "birthday": "1999-01-25"
}, {
     "name": "XXX",
    "gender": "female",
    "birthday": "1999-10-18"
}]
'''
data = json.loads(str)
print(data)
print(data[0]['name'])
print(data[0].get('name'))
print(data[0].get('age'))
print(data[0].get('age', 25))
```

使用 loads 方法将字符串转为 JSON 对象，通过索引来获取对应的内容，获取键值时有两种方式，一种是中括号加键名，另一种是通过 get 方法传入键名。使用 get 方法，如果键名不存在，则不会报错，会返回 None，get 方法还可以传入第二个参数（即默认值），尝试获取一个原字典中不存在的键名，此时默认会返回 None。如果传入第二个参数（即默认值），那么在不存在的情况下返回该默认值。

```python
[{'name': 'TRH', 'gender': 'male', 'birthday': '1999-01-25'}, {'name': 'XXX', 'gender': 'female', 'birthday': '1999-10-18'}]
TRH
TRH
None
25
```

## <font color=#ff0000>【9.2.3】写入 JSON 文件</font>
调用 dumps 方法可以将 JSON 对象转化为字符串，然后再调用文件的 write 方法即可写入文本

```python
import json

data = [{
    'name': 'TRH',
    'gender': 'male',
    'birthday': '1999-01-25'
}]
with open('data.json', 'w') as fp:
    fp.write(json.dumps(data))
```

data.json 文件：

```json
[{"name": "TRH", "gender": "male", "birthday": "1999-01-25"}]
```
添加参数 indent（代表缩进字符个数），将会格式化输出：
```python
import json

data = [{
    'name': 'TRH',
    'gender': 'male',
    'birthday': '1999-01-25'
}]
with open('data.json', 'w') as file:
    file.write(json.dumps(data, indent=2))
```

输出结果：

```json
[
  {
    "name": "TRH",
    "gender": "male",
    "birthday": "1999-01-25"
  }
]
```

如果 JSON 中包含中文字符，需要指定参数 ensure_ascii 为 False，另外还要规定文件输出的编码：

```python
import json

data = [{
    'name': '小明',
    'gender': '男',
    'birthday': '1999年01月25日'
}]
with open('data.json', 'w', encoding='utf-8') as file:
    file.write(json.dumps(data, indent=2, ensure_ascii=False))
```

输出结果：

```json
[
  {
    "name": "小明",
    "gender": "男",
    "birthday": "1999年01月25日"
  }
]
```

# <font color=#ff0000>【9.3】CSV 文本存储</font>

CSV（Comma-Separated Values）是逗号分隔值或字符分隔值的文件格式，其文件以纯文本的形式储存表格数据（数字和文本），CSV 文件的行与行之间用换行符分隔，列与列之间用逗号分隔

## <font color=#ff0000>【9.3.1】写入</font>

```python
import csv

with open('data.csv', 'w') as csvfile:
    writer = csv.writer(csvfile)
    writer.writerow(['id', 'name', 'age'])
    writer.writerow(['10001', 'TRHX', 20])
    writer.writerow(['10002', 'Bob', 22])
    writer.writerow(['10003', 'Jordan', 21])
```

打开 data.csv 文件，调用 CSV 库的 writer 方法初始化写入对象，然后调用 writerow 方法传入每行的数据即可完成写入，用 Excel 打开 data.csv 文件将是表格形式

```python
id,name,age

10001,TRHX,20

10002,Bob,22

10003,Jordan,21
```

默认每一行之间是有一行空格的，可以使用参数 `newline` 来去除空行：

```python
import csv

with open('data.csv', 'w', newline='') as csvfile:
    writer = csv.writer(csvfile)
    writer.writerow(['id', 'name', 'age'])
    writer.writerow(['10001', 'TRHX', 20])
    writer.writerow(['10002', 'Bob', 22])
    writer.writerow(['10003', 'Jordan', 21])
```

输出结果：

```python
id,name,age
10001,TRHX,20
10002,Bob,22
10003,Jordan,21
```

列与列之间的分隔符是可以修改的，只需要传入 `delimiter` 参数即可：

```python
import csv

with open('data.csv', 'w') as csvfile:
    writer = csv.writer(csvfile, delimiter=' ')
    writer.writerow(['id', 'name', 'age'])
    writer.writerow(['10001', 'TRHX', 20])
    writer.writerow(['10002', 'Bob', 22])
    writer.writerow(['10003', 'Jordan', 21])
```

输出结果：

```python
id name age

10001 TRHX 20

10002 Bob 22

10003 Jordan 21
```

调用 `writerows` 方法也可以同时写入多行，此时参数就需要为二维列表：

```python
import csv

with open('data.csv', 'w') as csvfile:
    writer = csv.writer(csvfile, delimiter=' ')
    writer.writerow(['id', 'name', 'age'])
    writer.writerows([['10001', 'TRHX', 20], ['10002', 'Bob', 22], ['10003', 'Jordan', 21]])
```

输出结果仍与原来的一样

此外 CSV 库中也提供了字典的写入方式：

```python
import csv

with open('data.csv', 'w') as csvfile:
    fieldnames = ['id', 'name', 'age']
    writer = csv.DictWriter(csvfile, fieldnames=fieldnames)
    writer.writeheader()
    writer.writerow({'id': '10001', 'name': 'TRHX', 'age': 20})
    writer.writerow({'id': '10002', 'name': 'Bob', 'age': 22})
    writer.writerow({'id': '10003', 'name': 'Jordan', 'age': 21})
```

首先定义 3 个字段，用 fieldnames 表示，然后将其传给 DictWriter 来初始化一个字典写入对象，接着可以调用 writeheader 方法先写入头信息，然后再调用 writerow 方法传入相应字典即可

```python
id,name,age

10001,TRHX,20

10002,Bob,22

10003,Jordan,21
```

## <font color=#ff0000>【9.3.2】读取</font>

有写入方法，同样也可以使用 csv 库来读取 CSV 文件：

```python
import csv

with open('data.csv', 'r', encoding='utf-8') as csvfile:
    reader = csv.reader(csvfile)
    for row in reader:
        print(row)
```

构造 Reader 对象，遍历输出每行的内容，每一行都是一个列表形式。（如果 CSV 文件中包含中文的话，还需要指定文件编码）读取结果：

```python
['id', 'name', 'age']
['10001', 'TRHX', '20']
['10002', 'Bob', '22']
['10003', 'Jordan', '21']
```

此外，还可以利用 pandas 的 read_csv 方法将数据从 CSV 中读取出来（pandas 是基于NumPy 的一种工具，该工具是为了解决数据分析任务而创建的。Pandas 纳入了大量库和一些标准的数据模型，提供了高效地操作大型数据集所需的工具）

```python
import pandas as pd

df = pd.read_csv('data.csv')
print(df)
```

读取结果：

```python
      id    name  age
0  10001    TRHX   20
1  10002     Bob   22
2  10003  Jordan   21
```

---
title: Python3 爬虫学习笔记 C08
tags:
  - 爬虫
  - Beautiful Soup
categories: 
  - Python3 学习笔记
  - 爬虫学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/spider.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 爬虫学习笔记第八章 —— 【解析库 Beautiful Soup】</font></center>

<!--more-->

# <font color=#ff0000>【8.1】关于 Beautiful Soup</font>
Beautiful Soup 可以从 HTML 或者 XML 文件中提取数据，Beautiful Soup 可以提供一些简单的、Python 式的函数用来处理导航、搜索、修改分析树等，它借助网页的结构和属性等特性来解析网页，lxml 只会局部遍历，而 Beautiful Soup 是基于 HTML DOM 的，会载入整个文档，解析整个 DOM 树，因此时间和内存开销都会大很多，所以性能要低于lxml

| 抓取工具      | 速度 | 使用难度 | 安装难度   |
| :-------------: | :----: | :--------: | :----------: |
| 正则          | 最快 | 困难     | 无（内置） |
| lxml          | 快   | 简单     | 一般       |
| BeautifulSoup | 慢   | 最简单   | 简单       |

# <font color=#ff0000>【8.2】Beautiful Soup 的基本使用</font>
需要使用命令 `pip install bs4` 安装库，Beautiful Soup 在解析时依赖解析器，除了支持 Python 标准库中的 HTML 解析器外，还支持一些第三方解析器：

| 解析器           | 使用方法                             | 优势                                                        | 劣势                                          |
| ---------------- | ------------------------------------ | ----------------------------------------------------------- | --------------------------------------------- |
| Python 标准库    | BeautifulSoup(markup, "html.parser") | Python 的内置标准库、执行速度适中 、文档容错能力强          | Python 2.7.3 or 3.2.2) 前的版本中文容错能力差 |
| LXML HTML 解析器 | BeautifulSoup(markup, "lxml")        | 速度快、文档容错能力强                                      | 需要安装 C 语言库                             |
| LXML XML 解析器  | BeautifulSoup(markup, "xml")         | 速度快、唯一支持 XML 的解析器                               | 需要安装 C 语言库                             |
| html5lib         | BeautifulSoup(markup, "html5lib")    | 最好的容错性、以浏览器的方式解析文档、生成 HTML5 格式的文档 | 速度慢、不依赖外部扩展                        |

基本使用：
```python
from bs4 import BeautifulSoup
soup = BeautifulSoup('<p>Hello</p>', 'lxml')
# soup = BeautifulSoup(open('soup.html', encoding='utf8'), 'lxml')
print(soup.p.string)
```
输出结果：
```python
Hello
```

# <font color=#ff0000>【8.3】节点选择器</font>
直接调用节点的名称就可以选择节点元素，再调用 string 属性就可以得到节点内的文本

## <font color=#ff0000>【8.3.1】元素选择</font>
新建 `soup.html` 文件：
```python
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <title>测试bs4</title>
</head>
<body>
<div>
    甄姬
    <p>百里守约</p>
    <p>李白</p>
    太乙真人
</div>
<div class="song">
    <p>李清照</p>
    <p>王安石</p>
    <p>苏轼</p>
    <p>柳宗元</p>
    <a href="http://www.song.com/" title="赵匡义" target="_self">宋朝是最强大的王朝，不是军队的强大，而是经济很强大，国民都很有钱。</a>
    <img src="http://www.baidu.com/meinv.jpg" alt="">
    <a href="" class="du">总为浮云能蔽日，长安不见使人愁</a>
</div>
<div class="tang">
    <ul>
        <li><a href="http://www.baidu.com" title="qing">清明时节雨纷纷，路上行人欲断魂，借问酒家何处有，牧童遥指杏花村。</a> </li>
        <li><a href="http://www.163.com" title="qin">秦时明月汉时关，万里长征人未还，但使龙城飞将在，不教胡马度阴山。</a> </li>
        <li><a href="http://www.126.com" alt="qi">岐王宅里寻常见，崔九堂前几度闻，正是江南好风景，落花时节又逢君。</a> </li>
        <li><a href="http://www.sina.com" class="du">杜甫</a> </li>
        <li><b>唐朝</b></li>
        <li><i>宋朝</i></li>
        <li><a href="http://www.haha.com" id="feng">凤凰台上凤凰游，凤去台空江自流，吴宫花草埋幽径，晋代衣冠成古丘。</a> </li>
    </ul>
</div>

</body>
</html>
```

```python
from bs4 import BeautifulSoup
soup = BeautifulSoup(open('soup.html', encoding='utf8'), 'lxml')
print(soup.title)
print(type(soup.title))
print(soup.title.string)
print(soup.head)
print(soup.p)
```
依次查找 title、head、p 节点。输出结果：
```python
<title>测试bs4</title>
<class 'bs4.element.Tag'>
测试bs4
<head>
<meta charset="utf-8"/>
<title>测试bs4</title>
</head>
<p>百里守约</p>
```

## <font color=#ff0000>【8.3.2】提取信息</font>
- string 属性：获取节点包含的文本值（如果标签里面还有标签，那么string获取到的结果为None）
- text 属性：获取节点包含的文本值
- get_text() 属性：获取节点包含的文本值
- name 属性：获取节点的名称
- attrs ：获取所有属性
- attrs['属性名'] ：获取指定属性

依然以 soup.html 为例：
```python
from bs4 import BeautifulSoup
soup = BeautifulSoup(open('soup.html', encoding='utf8'), 'lxml')
print(soup.title)
print(soup.title.text)
print(soup.title.get_text())
print(soup.title.string)
print(soup.div.string)
print(soup.div.text)
print(soup.title.name)
print(soup.a['href'])  # 获取href属性
print(soup.a['title'])  # 获取title属性
print(soup.a['target'])  # 获取target属性
print(soup.a.attrs)  # 获取所有属性
print(soup.a.attrs['href'])  # 获取href属性
```
输出结果：
```python
<title>测试bs4</title>
测试bs4
测试bs4
测试bs4
None

    甄姬
    百里守约
李白
    太乙真人

title
http://www.song.com/
赵匡义
_self
{'href': 'http://www.song.com/', 'title': '赵匡义', 'target': '_self'}
http://www.song.com/
```

# <font color=#ff0000>【8.3.3】嵌套选择</font>
```python
from bs4 import BeautifulSoup

html = """
<html><head><title>This is a demo</title></head>
<body>
"""
soup = BeautifulSoup(html, 'lxml')
print(soup.head.title)
print(type(soup.head.title))
print(soup.head.title.string)
```
获取 head 节点里面的 title 节点，输出结果：
```python
<title>This is a demo</title>
<class 'bs4.element.Tag'>
This is a demo
```

# <font color=#ff0000>【8.3.4】关联选择</font>
- contents 属性：获取某个节点元素的直接子节点
- children 属性：遍历某个节点元素的子节点
- descendants 属性：获取某个节点元素所有的子孙节点
- parent 属性：获取某个节点元素的父节点
- parents 属性：获取某个节点元素所有的祖先节点
- next_sibling 属性：获取节点的下一个兄弟元素
- previous_sibling 属性：获取节点的上一个兄弟元素
- next_siblings 属性：获取某个节点所有后面的兄弟元素
- previous_siblings 属性：获取某个节点所有前面的兄弟元素

<font color=#ff0000>contents 属性应用示例</font>

```python
from bs4 import BeautifulSoup

html = """
<html>
    <head>
        <title>The Dormouse's story</title>
    </head>
    <body>
        <p class="story">
            Once upon a time there were three little sisters; and their names were
            <a href="http://example.com/elsie" class="sister" id="link1">
                <span>Elsie</span>
            </a>
            <a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> 
            and
            <a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>
            and they lived at the bottom of a well.
        </p>
        <p class="story">...</p>
"""
soup = BeautifulSoup(html, 'lxml')
print(soup.p.contents)
```
获取 p 节点元素的直接子节点，输出结果：
```python
['\n            Once upon a time there were three little sisters; and their names were\n            ', <a class="sister" href="http://example.com/elsie" id="link1">
<span>Elsie</span>
</a>, '\n', <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>, ' \n            and\n            ', <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>, '\n            and they lived at the bottom of a well.\n        ']
```
<font color=#ff0000>children 属性应用示例：</font>

```python
from bs4 import BeautifulSoup

html = """
<html>
    <head>
        <title>The Dormouse's story</title>
    </head>
    <body>
        <p class="story">
            Once upon a time there were three little sisters; and their names were
            <a href="http://example.com/elsie" class="sister" id="link1">
                <span>Elsie</span>
            </a>
            <a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> 
            and
            <a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>
            and they lived at the bottom of a well.
        </p>
        <p class="story">...</p>
"""
soup = BeautifulSoup(html, 'lxml')
print(soup.p.children)
for i, child in enumerate(soup.p.children):
    print(i, child)
```
遍历 p 节点元素的子节点，输出结果：
```python
<list_iterator object at 0x00000228E3C205F8>
0 
            Once upon a time there were three little sisters; and their names were
            
1 <a class="sister" href="http://example.com/elsie" id="link1">
<span>Elsie</span>
</a>
2 

3 <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>
4  
            and
            
5 <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>
6 
            and they lived at the bottom of a well.
```

<font color=#ff0000>descendants 属性应用示例：</font>
```python
from bs4 import BeautifulSoup

html = """
<html>
    <head>
        <title>The Dormouse's story</title>
    </head>
    <body>
        <p class="story">
            Once upon a time there were three little sisters; and their names were
            <a href="http://example.com/elsie" class="sister" id="link1">
                <span>Elsie</span>
            </a>
            <a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> 
            and
            <a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>
            and they lived at the bottom of a well.
        </p>
        <p class="story">...</p>
"""
soup = BeautifulSoup(html, 'lxml')
print(soup.p.descendants)
for i, child in enumerate(soup.p.descendants):
    print(i, child)
```
获取 p 节点元素所有的子孙节点，输出结果：
```python
<generator object descendants at 0x0000018404A4C3B8>
0 
            Once upon a time there were three little sisters; and their names were
            
1 <a class="sister" href="http://example.com/elsie" id="link1">
<span>Elsie</span>
</a>
2 

3 <span>Elsie</span>
4 Elsie
5 

6 

7 <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>
8 Lacie
9  
            and
            
10 <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>
11 Tillie
12 
            and they lived at the bottom of a well.
```
<font color=#ff0000>parent 属性应用示例：</font>
```python
from bs4 import BeautifulSoup
html = """
<html>
    <head>
        <title>The Dormouse's story</title>
    </head>
    <body>
        <p class="story">
            Once upon a time there were three little sisters; and their names were
            <a href="http://example.com/elsie" class="sister" id="link1">
                <span>Elsie</span>
            </a>
        </p>
    </body>
</html>
"""
soup = BeautifulSoup(html, 'lxml')
print(soup.a.parent)
```
获取 a 节点元素的父节点，输出结果：
```python
<p class="story">
            Once upon a time there were three little sisters; and their names were
            <a class="sister" href="http://example.com/elsie" id="link1">
<span>Elsie</span>
</a>
</p>
```
<font color=#ff0000>parents 属性应用示例：</font>
```python
from bs4 import BeautifulSoup

html = """
<html>
    <body>
        <p class="story">
            <a href="http://example.com/elsie" class="sister" id="link1">
                <span>Elsie</span>
            </a>
        </p>
"""
soup = BeautifulSoup(html, 'lxml')
print(type(soup.a.parents))
print(list(enumerate(soup.a.parents)))
```

获取 a 节点元素所有的祖先节点，输出结果：

```python
<class 'generator'>
[(0, <p class="story">
<a class="sister" href="http://example.com/elsie" id="link1">
<span>Elsie</span>
</a>
</p>), (1, <body>
<p class="story">
<a class="sister" href="http://example.com/elsie" id="link1">
<span>Elsie</span>
</a>
</p>
</body>), (2, <html>
<body>
<p class="story">
<a class="sister" href="http://example.com/elsie" id="link1">
<span>Elsie</span>
</a>
</p>
</body></html>), (3, <html>
<body>
<p class="story">
<a class="sister" href="http://example.com/elsie" id="link1">
<span>Elsie</span>
</a>
</p>
</body></html>)]
```

<font color=#ff0000>next_sibling、previous_sibling、next_siblings、previous_siblings 属性应用示例：</font>

```python
html = """
<html>
    <body>
        <p class="story">
            Once upon a time there were three little sisters; and their names were
            <a href="http://example.com/elsie" class="sister" id="link1">
                <span>Elsie</span>
            </a>
            Hello
            <a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> 
            and
            <a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>
            and they lived at the bottom of a well.
        </p>
    </body>
</html>
"""
from bs4 import BeautifulSoup
soup = BeautifulSoup(html, 'lxml')
print('Next Sibling', soup.a.next_sibling)
print('Prev Sibling', soup.a.previous_sibling)
print('Next Siblings', list(enumerate(soup.a.next_siblings)))
print('Prev Siblings', list(enumerate(soup.a.previous_siblings)))
```

next_sibling 和 previous_sibling 分别获取 a 节点的下一个和上一个兄弟元素，next_siblings 和 previous_siblings 则分别返回 a 节点后面和前面的兄弟节点，输出结果：

```python
Next Sibling 
            Hello
            
Prev Sibling 
            Once upon a time there were three little sisters; and their names were
            
Next Siblings [(0, '\n            Hello\n            '), (1, <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>), (2, ' \n            and\n            '), (3, <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>), (4, '\n            and they lived at the bottom of a well.\n        ')]
Prev Siblings [(0, '\n            Once upon a time there were three little sisters; and their names were\n            ')]
```

# <font color=#ff0000>【8.4】方法选择器</font>

节点选择器直接调用节点的名称就可以选择节点元素，如果进行比较复杂的选择的话，方法选择器是一个不错的选择，它更灵活，常见的方法有 find_all、find 等，调用它们，直接传入相应的参数，就可以灵活查询了。

## <font color=#ff0000>【8.4.1】find_all() 方法</font>

find_all 方法可以查询所有符合条件的元素，给它传入一些属性或文本来得到符合条件的元素。find_all 方法的 API：`find_all(name , attrs , recursive , text , **kwargs)`
新建 soup.html：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <title>测试bs4</title>
</head>
<body>
<div>
    甄姬
    <p>百里守约</p>
    <p>李白</p>
    太乙真人
</div>
<div class="song">
    <p>李清照</p>
    <p>王安石</p>
    <p>苏轼</p>
    <p>柳宗元</p>
    <a href="http://www.song.com/" title="赵匡义" target="_self">宋朝是最强大的王朝，不是军队的强大，而是经济很强大，国民都很有钱。</a>
    <img src="http://www.baidu.com/meinv.jpg" alt="">
    <a href="" class="du">总为浮云能蔽日，长安不见使人愁</a>
</div>
<div class="tang">
    <ul>
        <li><a href="http://www.baidu.com" title="qing">清明时节雨纷纷，路上行人欲断魂，借问酒家何处有，牧童遥指杏花村。</a> </li>
        <li><a href="http://www.163.com" title="qin">秦时明月汉时关，万里长征人未还，但使龙城飞将在，不教胡马度阴山。</a> </li>
        <li><a href="http://www.126.com" name="qi">岐王宅里寻常见，崔九堂前几度闻，正是江南好风景，落花时节又逢君。</a> </li>
        <li><a href="http://www.sina.com" class="du">杜甫</a> </li>
        <li><b>唐朝</b></li>
        <li><i>宋朝</i></li>
        <li><a href="http://www.haha.com" id="feng">凤凰台上凤凰游，凤去台空江自流，吴宫花草埋幽径，晋代衣冠成古丘。</a> </li>
    </ul>
</div>

</body>
</html>
```
示例代码：
```python
from bs4 import BeautifulSoup

soup = BeautifulSoup(open('soup.html', encoding='utf8'), 'lxml')
print(soup.find_all('a'), '\n')
print(soup.find_all('a')[1], '\n')
print(soup.find_all('a')[1].text, '\n')
print(soup.find_all(['a', 'b', 'i']), '\n')
print(soup.find_all('a', limit=2), '\n')
print(soup.find_all(title='qing'), '\n')
print(soup.find_all(attrs={'id': 'feng'}), '\n')
```
输出结果：
```python
[<a href="http://www.song.com/" target="_self" title="赵匡义">宋朝是最强大的王朝，不是军队的强大，而是经济很强大，国民都很有钱。</a>, <a class="du" href="">总为浮云能蔽日，长安不见使人愁</a>, <a href="http://www.baidu.com" title="qing">清明时节雨纷纷，路上行人欲断魂，借问酒家何处有，牧童遥指杏花村。</a>, <a href="http://www.163.com" title="qin">秦时明月汉时关，万里长征人未还，但使龙城飞将在，不教胡马度阴山。</a>, <a href="http://www.126.com" name="qi">岐王宅里寻常见，崔九堂前几度闻，正是江南好风景，落花时节又逢君。</a>, <a class="du" href="http://www.sina.com">杜甫</a>, <a href="http://www.haha.com" id="feng">凤凰台上凤凰游，凤去台空江自流，吴宫花草埋幽径，晋代衣冠成古丘。</a>] 

<a class="du" href="">总为浮云能蔽日，长安不见使人愁</a> 

总为浮云能蔽日，长安不见使人愁 

[<a href="http://www.song.com/" target="_self" title="赵匡义">宋朝是最强大的王朝，不是军队的强大，而是经济很强大，国民都很有钱。</a>, <a class="du" href="">总为浮云能蔽日，长安不见使人愁</a>, <a href="http://www.baidu.com" title="qing">清明时节雨纷纷，路上行人欲断魂，借问酒家何处有，牧童遥指杏花村。</a>, <a href="http://www.163.com" title="qin">秦时明月汉时关，万里长征人未还，但使龙城飞将在，不教胡马度阴山。</a>, <a href="http://www.126.com" name="qi">岐王宅里寻常见，崔九堂前几度闻，正是江南好风景，落花时节又逢君。</a>, <a class="du" href="http://www.sina.com">杜甫</a>, <b>唐朝</b>, <i>宋朝</i>, <a href="http://www.haha.com" id="feng">凤凰台上凤凰游，凤去台空江自流，吴宫花草埋幽径，晋代衣冠成古丘。</a>] 

[<a href="http://www.song.com/" target="_self" title="赵匡义">宋朝是最强大的王朝，不是军队的强大，而是经济很强大，国民都很有钱。</a>, <a class="du" href="">总为浮云能蔽日，长安不见使人愁</a>] 

[<a href="http://www.baidu.com" title="qing">清明时节雨纷纷，路上行人欲断魂，借问酒家何处有，牧童遥指杏花村。</a>] 

[<a href="http://www.haha.com" id="feng">凤凰台上凤凰游，凤去台空江自流，吴宫花草埋幽径，晋代衣冠成古丘。</a>] 
```

## <font color=#ff0000>【8.4.2】find() 方法</font>
find() 方法使用方法与 find_all() 方法相同，不同的是，find 方法返回的是单个元素，也就是第一个匹配的元素，而 find_all 返回的是所有匹配的元素组成的列表
特别的：
- find_parents 和 find_parent：前者返回所有祖先节点，后者返回直接父节点。

- find_next_siblings 和 find_next_sibling：前者返回后面所有的兄弟节点，后者返回后面第一个兄弟节点。

- find_previous_siblings 和 find_previous_sibling：前者返回前面所有的兄弟节点，后者返回前面第一个兄弟节点。

- find_all_next 和 find_next：前者返回节点后所有符合条件的节点，后者返回第一个符合条件的节点。

- find_all_previous 和 find_previous：前者返回节点前所有符合条件的节点，后者返回第一个符合条件的节点。

# <font color=#ff0000>【8.5】CSS 选择器</font>
使用 CSS 选择器，只需要调用 select 方法，传入相应的 CSS 选择器即可
新建 soup.html 文件：
```html
<!DOCTYPE html>
<html lang="en">
<div class="tang">
    <ul>
        <li><a href="http://www.baidu.com" title="qing">清明时节雨纷纷，路上行人欲断魂，借问酒家何处有，牧童遥指杏花村。</a> </li>
        <li><a href="http://www.163.com" title="qin">秦时明月汉时关，万里长征人未还，但使龙城飞将在，不教胡马度阴山。</a> </li>
        <li><a href="http://www.126.com" name="qi">岐王宅里寻常见，崔九堂前几度闻，正是江南好风景，落花时节又逢君。</a> </li>
        <li><a href="http://www.sina.com" class="du">杜甫</a> </li>
        <li><a href="http://www.haha.com" id="feng">凤凰台上凤凰游，凤去台空江自流，吴宫花草埋幽径，晋代衣冠成古丘。</a> </li>
    </ul>
</div>

</body>
</html>
```
通过 CSS 选择器依次选择 class="tang" 的 div 节点下的 a 节点、id 为 feng 的节点以及其 href 元素：
```python
from bs4 import BeautifulSoup

soup = BeautifulSoup(open('soup.html', encoding='utf8'), 'lxml')
print(soup.select('li'), '\n')
print(soup.select('.tang > ul > li > a')[2], '\n')
print(soup.select('#feng')[0].text, '\n')
print(soup.select('#feng')[0]['href'], '\n')
```
输出结果：
```python
[<li><a href="http://www.baidu.com" title="qing">清明时节雨纷纷，路上行人欲断魂，借问酒家何处有，牧童遥指杏花村。</a> </li>, <li><a href="http://www.163.com" title="qin">秦时明月汉时关，万里长征人未还，但使龙城飞将在，不教胡马度阴山。</a> </li>, <li><a href="http://www.126.com" name="qi">岐王宅里寻常见，崔九堂前几度闻，正是江南好风景，落花时节又逢君。</a> </li>, <li><a class="du" href="http://www.sina.com">杜甫</a> </li>, <li><a href="http://www.haha.com" id="feng">凤凰台上凤凰游，凤去台空江自流，吴宫花草埋幽径，晋代衣冠成古丘。</a> </li>] 

<a href="http://www.126.com" name="qi">岐王宅里寻常见，崔九堂前几度闻，正是江南好风景，落花时节又逢君。</a> 

凤凰台上凤凰游，凤去台空江自流，吴宫花草埋幽径，晋代衣冠成古丘。 

http://www.haha.com 
```
附表：CSS 选择器，来源：https://www.w3school.com.cn/cssref/css_selectors.asp

| 选择器	| 例子 |	例子描述	| CSS |
|--------|--------|-------|-------|
| .class | .intro	| 选择 class="intro" 的所有元素 |	1 |
| #id	   |#firstname|	选择 id="firstname" 的所有元素 |	1 |
| *	     |*	       | 选择所有元素  |	2  |
|element |	p	     |选择所有 <p> 元素  |	1  |
|element,element|	div,p	|选择所有 <div> 元素和所有 <p> 元素|	1|
|element element|	div p	|选择 <div> 元素内部的所有 <p> 元素|	1|
|element>element|	div>p	|选择父元素为 <div> 元素的所有 <p> 元素|	2|
|element+element|	div+p	|选择紧接在 <div> 元素之后的所有 <p> 元素|	2|
|[attribute]    |	[target]|	选择带有 target 属性所有元素|2|
|[attribute=value]|	[target=_blank]	|选择 target="_blank" 的所有元素|	2|
|[attribute~=value]|	[title~=flower]	|选择 title 属性包含单词 "flower" 的所有元素|2|
|[attribute|=value]|	[lang|=en]	|选择 lang 属性值以 "en" 开头的所有元素|	2|
|:link	       |a:link	|选择所有未被访问的链接|	1|
|:visited	     |a:visited	|选择所有已被访问的链接|	1|
|:active	     |a:active	|选择活动链接|	1|
|:hover	       |a:hover	|选择鼠标指针位于其上的链接|1|
|:focus	       |input:focus	|选择获得焦点的 input 元素|	2|
|:first-letter |	p:first-letter	|选择每个 <p> 元素的首字母|	1|
|:first-line	 |p:first-line	|选择每个 <p> 元素的首行|	1|
|:first-child	 |p:first-child	|选择属于父元素的第一个子元素的每个 <p> 元素|	2|
|:before	     |p:before	|在每个 <p> 元素的内容之前插入内容|	2|
|:after	       |p:after	|在每个 <p> 元素的内容之后插入内容|	2|
|:lang(language)|	p:lang(it)	|选择带有以 "it" 开头的 lang 属性值的每个 <p> 元素|	2|
|element1~element2|	p~ul	|选择前面有 <p> 元素的每个 <ul> 元素|	3|
|[attribute^=value]|	a[src^="https"]	|选择其 src 属性值以 "https" 开头的每个 <a> 元素|	3|
|[attribute$=value]|	a[src$=".pdf"]	|选择其 src 属性以 ".pdf" 结尾的所有 <a> 元素|	3|
|[attribute*=value]|	a[src*="abc"]	|选择其 src 属性中包含 "abc" 子串的每个 <a> 元素|	3|
|:first-of-type|	p:first-of-type	|选择属于其父元素的首个 <p> 元素的每个 <p> 元素|	3|
|:last-of-type|	p:last-of-type	|选择属于其父元素的最后 <p> 元素的每个 <p> 元素|	3|
|:only-of-type|	p:only-of-type	|选择属于其父元素唯一的 <p> 元素的每个 <p> 元素|	3|
|:only-child  |	p:only-child	|选择属于其父元素的唯一子元素的每个 <p> 元素|	3|
|:nth-child(n)|	p:nth-child(2)	|选择属于其父元素的第二个子元素的每个 <p> 元素|	3|
|:nth-last-child(n)|	p:nth-last-child(2)	|同上，从最后一个子元素开始计数|	3|
|:nth-of-type(n)|	p:nth-of-type(2)	|选择属于其父元素第二个 <p> 元素的每个 <p> 元素|	3|
|:nth-last-of-type(n)|	p:nth-last-of-type(2)	|同上，但是从最后一个子元素开始计数|	3|
|:last-child|	p:last-child	|选择属于其父元素最后一个子元素每个 <p> 元素|	3|
|:root|	:root	|选择文档的根元素|3|
|:empty|	p:empty	|选择没有子元素的每个 <p> 元素（包括文本节点）|	3|
|:target|	#news:target	|选择当前活动的 #news 元素|	3|
|:enabled	|input:enabled	|选择每个启用的 <input> 元素|	3|
|:disabled|	input:disabled	|选择每个禁用的 <input> 元素|	3|
|:checked|	input:checked	|选择每个被选中的 <input> 元素|	3|
|:not(selector)|	:not(p)	|选择非 <p> 元素的每个元素|	3|
|::selection|	::selection	|选择被用户选取的元素部分|	3|

# <font color=#ff0000>【8.6】附表：Beautiful Soup 库 soup 对象常用属性与方法</font>

| 基本元素 |	说明	| 返回类型 |
|---------|--------|---------|
|tag	    |soup.a	 |bs4.element.Tag|
|name	    |soup.a.name |	str|
|attrs	|soup.a.attrs	|dict|
|contents	|子节点	|list|
|children	|遍历子节点	|list_iterator|
|descendants	|遍历所有子孙节点	|generator|
|parent	|返回父亲标签	|bs4.element.Tag|
|parents	|上行遍历父辈标签	|generator|
|prettify()	|添加/n	|str|
|find_all(name,attr)	|soup.find_all(‘a’)/([‘a’,‘b’])/(True)/(‘p’,‘course’)/(id=‘link1’)/(string=‘python’)	|bs4.element.ResultSet|
|find()	|soup.find(‘a’)/返回第一个a标签	|bs4.element.Tag|
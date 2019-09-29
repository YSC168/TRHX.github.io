---
title: Python3 爬虫学习笔记 C07
tags:
  - 爬虫
  - lxml
  - XPath
categories: 
  - Python3 学习笔记
  - 爬虫学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/spider.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 爬虫学习笔记第七章 —— 【解析库 lxml】</font></center>

<!--more-->

# <font color=#ff0000>【7.1】关于 lxml</font>
 lxml 是 Python 的一个解析库，支持 HTML 和 XML 的解析，支持 XPath 解析方式，解析效率非常高，使用前需要用命令 `pip3 install lxml` 安装 lxml 库
 
 # <font color=#ff0000>【7.2】使用  XPath</font>
 XPath（XML Path Language）即 XML 路径语言， lxml 解析库使用的正是 XPath 语法，最初是用来搜寻 XML 文档的，是一门在 XML 文档中查找信息的语言，它同样适用于 HTML 文档的搜索
 
  XPath 常用规则
  

| 表达式   | 描述                     |
| :--------: | :------------------------: |
| nodename | 选取此节点的所有子节点   |
| /        | 从当前节点选取直接子节点 |
| //       | 从当前节点选取子孙节点   |
| .        | 选取当前节点             |
| ..       | 选取当前节点的父节点     |
| @        |    选取属性      |
| *     | 通配符，选择所有元素节点与元素名 |
| @* |	选取所有属性   |
| [@attrib]	|  选取具有给定属性的所有元素 |
| [@attrib='value']	| 选取给定属性具有给定值的所有元素 | 
|  [tag]	|  选取所有具有指定元素的直接子节点 |
| [tag='text']	| 选取所有具有指定元素并且文本内容是text节点 |

 浏览器插件 XPath Helper，在线验证 XPath，谷歌商店下载地址：https://chrome.google.com/webstore/detail/hgimnogjllphhhkhlmebbmlgjoejdpjl
 
 XPath 基本使用方法：首先使用代码 `from lxml import etree`导入库，然后将 HTML 文档变成一个对象，再调用对象的方法去查找指定的节点，方法有两种：`tree = etree.parse()` 为本地文件查找，`tree = etree.HTML()` 为网络文件查找，再使用语句 `tree.xpath()` 查找指定节点。
 
 # <font color=#ff0000>【7.3】查找所有节点</font>
 新建一个 xpath.html 本地文件，内容如下：
 ```html
 <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <title>xpath测试</title>
</head>
<body>
<div class="song">
    火药
    <b>指南针</b>
    <b>印刷术</b>
    造纸术
</div>
<div class="tang">
    <ul>
        <li class="balove">停车坐爱枫林晚，霜叶红于二月花。</li>
        <li id="hua">商女不知亡国恨，隔江犹唱后庭花。</li>
        <li class="love" name="yang">一骑红尘妃子笑，无人知是荔枝来。</li>
        <li id="bei">葡萄美酒夜光杯，欲饮琵琶马上催。</li>
        <li><a href="http://www.baidu.com/">百度一下</a> </li>
    </ul>
    <ol>
        <li class="balucy">寻寻觅觅冷冷清清，凄凄惨惨戚戚。</li>
        <li class="lily">咋暖还寒时候，最难将息。</li>
        <li class="lilei">三杯两盏淡酒。</li>
        <li>怎敌他晚来风急。</li>
        <li>雁过也，正伤心，却是旧时相识。</li>
        <li>爱情三十六计</li>
        <li>什么是爱情</li>
    </ol>
</div>
</body>
</html>
```
查找所有节点：
```python
from lxml import etree

html = etree.parse('./xpath.html')
result = html.xpath('//*')
print(result)
```
使用 `*` 代表匹配所有节点，整个 xpath.html 文件中的所有节点都会被获取到，返回形式是一个列表，每个元素是 Element 类型，其后跟了节点的名称，如 html、body、div、ul、li、a 等，所有节点都包含在列表中，输出结果如下：
```python
[<Element html at 0x1a836a34508>, <Element head at 0x1a836a344c8>, <Element meta at 0x1a836a345c8>, <Element title at 0x1a836a34608>, <Element body at 0x1a836a34648>, <Element div at 0x1a836a346c8>, <Element b at 0x1a836a34708>, <Element b at 0x1a836a34748>, <Element div at 0x1a836a34788>, <Element ul at 0x1a836a34688>, <Element li at 0x1a836a347c8>, <Element li at 0x1a836a34808>, <Element li at 0x1a836a34848>, <Element li at 0x1a836a34888>, <Element li at 0x1a836a348c8>, <Element a at 0x1a836a34908>, <Element ol at 0x1a836a34948>, <Element li at 0x1a836a34988>, <Element li at 0x1a836a349c8>, <Element li at 0x1a836a34a08>, <Element li at 0x1a836a34a48>, <Element li at 0x1a836a34a88>, <Element li at 0x1a836a34ac8>, <Element li at 0x1a836a34b08>]
```

 # <font color=#ff0000>【7.4】查找子节点</font>
 通过 <font color=#ff0000>/</font> 或 <font color=#ff0000>//</font> 即可查找元素的子节点或子孙节点：
 ```python
 from lxml import etree

html = etree.parse('./xpath.html')
result = html.xpath('//ul/li')
print(result)
```
选择 ul 节点的所有<font color=#ff0000>直接</font> li 子节点：
```python
[<Element li at 0x2a094d044c8>, <Element li at 0x2a094d045c8>, <Element li at 0x2a094d04608>, <Element li at 0x2a094d04648>, <Element li at 0x2a094d04688>]
```

 # <font color=#ff0000>【7.5】查找父节点</font>
 知道了子节点，也可以用 <font color=#ff0000>..</font> 或者 <font color=#ff0000>parent::</font> 查找其父节点
 ```python
 from lxml import etree

html = etree.parse('./xpath.html')
result = html.xpath('//ol/../@class')
print(result)
```

```python
from lxml import etree

html = etree.parse('./xpath.html')
result = html.xpath('//ol/parent::*/@class')
print(result)
```
先查找到 ol 节点，随后获取其父节点以及其 class 属性：
```python
['tang']
```

 # <font color=#ff0000>【7.6】属性匹配</font>
 有时候 HTML 包含多个相同名的节点，而节点的属性是不一样的，此时可以用 <font color=#ff0000>@</font> 符号进行属性过滤
 ```python
 from lxml import etree

html = etree.parse('./xpath.html')
result = html.xpath('//li[@class="balucy"]')
print(result)
```
xpath.html 文件中，只有一个 class 为 balucy 的节点：`<li class="balucy">寻寻觅觅冷冷清清，凄凄惨惨戚戚。</li>`，运行以上代码将返回一个该元素：
```python
[<Element li at 0x16e53aa54c8>]
```

 # <font color=#ff0000>【7.7】文本获取</font>
 使用 text() 方法即可提取节点中的文本：
 ```python
 from lxml import etree

html = etree.parse('./xpath.html')
result = html.xpath('//li[@class="balucy"]/text()')
print(result)
```
输出结果：
```python
['寻寻觅觅冷冷清清，凄凄惨惨戚戚。']
```
再次观察 xpath.html 文件中的 `<ol></ol>`这一部分：
```python
<ol>
    <li class="balucy">寻寻觅觅冷冷清清，凄凄惨惨戚戚。</li>
    <li class="lily">咋暖还寒时候，最难将息。</li>
    <li class="lilei">三杯两盏淡酒。</li>
    <li>怎敌他晚来风急。</li>
    <li>雁过也，正伤心，却是旧时相识。</li>
    <li>爱情三十六计</li>
    <li>什么是爱情</li>
</ol>

```
如果我们想要提取 `<li>` 节点里面所有的文本，就可以使用 `html.xpath('//ol/li/text()')` 语句：
```python
from lxml import etree

html = etree.parse('./xpath.html')
result = html.xpath('//ol/li/text()')
print(result)
```
输出结果：
```python
['寻寻觅觅冷冷清清，凄凄惨惨戚戚。', '咋暖还寒时候，最难将息。', '三杯两盏淡酒。', '怎敌他晚来风急。', '雁过也，正伤心，却是旧时相识。', '爱情三十六计', '什么是爱情']
```


同样还有另一种方法，使用 `html.xpath('//ol//text()')` 语句，`//` 将会选取所有子孙节点的文本，`<ol>` 和 `<li>` 节点下的换行符也将被提取出来：
```python
from lxml import etree

html = etree.parse('./xpath.html')
result = html.xpath('//ol//text()')
print(result)
```
输出结果：
```python
['\n        ', '寻寻觅觅冷冷清清，凄凄惨惨戚戚。', '\n        ', '咋暖还寒时候，最难将息。', '\n        ', '三杯两盏淡酒。', '\n        ', '怎敌他晚来风急。', '\n        ', '雁过也，正伤心，却是旧时相识。', '\n        ', '爱情三十六计', '\n        ', '什么是爱情', '\n    ']
```

 # <font color=#ff0000>【7.8】属性获取</font>
 与属性匹配一样，属性获取仍然使用 <font color=#ff0000>@</font>：
 ```python
 from lxml import etree

html = etree.parse('./xpath.html')
result = html.xpath('//ul/li[5]/a/@href')
print(result)
 ```
 获取 href 属性：
 ```python
 ['http://www.baidu.com/']
 ```
 
  # <font color=#ff0000>【7.9】一个属性包含多个值的匹配</font>
某个节点的某个属性可能有多个值，例如：
```html
<li class="li li-first"><a href="link.html">first item</a></li> 
```
li 节点的 class 属性有 li 和 li-first 两个值，如果使用 `html.xpath('//li[@class="li"]` 语句，将无法成功匹配，这时就需要使用<font color=#ff0000> contains</font> 方法了，第一个参数传入属性名称，第二个参数传入属性值，只要此属性包含所传入的属性值，就可以完成匹配了

```python
from lxml import etree
text = '''  
<li class="li li-first"><a href="link.html">first item</a></li>  
'''
html = etree.HTML(text)
result = html.xpath('//li[contains(@class, "li")]/a/text()')
print(result)
```
输出结果：
```python
['first item']
```
  # <font color=#ff0000>【7.10】多个属性匹配一个节点</font>
XPath 还可以根据多个属性来确定一个节点，这时就需要同时匹配多个属性。此时可以使用运算符 and 来连接：
```python
from lxml import etree
text = '''  
<li class="li" name="item"><a href="link.html">first item</a></li>
'''
html = etree.HTML(text)
result = html.xpath('//li[@class="li" and @name="item"]/a/text()')
print(result)
```
输出结果：
```python
['first item']
```
示例中运用了运算符 and 来连接，此外常见的运算符如下：

| 运算符 | 描述           | 实例              | 返回值                                                    |
| ------ | -------------- | ----------------- | --------------------------------------------------------- |
| or     | 或             | age=19 or age=20  | 如果 age 是 19 或者 20，则返回 true。如果 age 是其他值，则返回 false |
| and    | 与             | age>19 and age<21 | 如果 age 大于 19 且小于 21，则返回 true。如果 age 是其他值，则返回 false |
| mod    | 计算除法的余数 | 5 mod 2           | 1                                                         |
| &#124; | 计算两个节点集 | //book &#124; //cd     | 返回所有拥有 book 和 cd 元素的节点集                      |
| +      | 加法           | 10 + 5            | 15                                                        |
| -      | 减法           | 10 - 5            | 5                                                         |
| *      | 乘法           | 10 * 5            | 50                                                        |
| div    | 除法           | 10 div 5          | 2                                                         |
| =      | 等于           | age=19            | 如果 age 是 19，则返回 true。如果 age 不是 19，则返回 false |
| !=     | 不等于         | age!=19           | 如果 age 不是 19，则返回 true。如果 age 是 19，则返回 false |
| < | 小于  |  age<19  | 如果 age 小于 19，则返回 true。如果 age 不小于 19，则返回 false |
| <= | 小于或等于 | age<=19 | 如果 age 小于等于 19，则返回 true。如果 age 大于 19，则返回 false |
| > | 大于 | age>19 | 如果 age 大于 19，则返回 true。如果 age 不大于 19，则返回 false |
| >= | 大于或等于 | age>=19 | 如果 age 大于等于 19，则返回 true。如果 age 小于 19，则返回 false |

  # <font color=#ff0000>【7.11】按顺序选择节点</font>
某些属性可能同时匹配了多个节点，如果要选择其中几个节点，可以利用中括号传入索引的方法获取特定次序的节点
```python
from lxml import etree

text = '''
<div>
    <ul>
         <li class="item-0"><a href="link1.html">first item</a></li>
         <li class="item-1"><a href="link2.html">second item</a></li>
         <li class="item-inactive"><a href="link3.html">third item</a></li>
         <li class="item-1"><a href="link4.html">fourth item</a></li>
         <li class="item-0"><a href="link5.html">fifth item</a>
    </ul>
 </div>
'''
html = etree.HTML(text)
result = html.xpath('//li[1]/a/text()')
print(result)
result = html.xpath('//li[last()]/a/text()')
print(result)
result = html.xpath('//li[position()<3]/a/text()')
print(result)
result = html.xpath('//li[last()-2]/a/text()')
print(result)
```
- li[1]：选取第一个 li 节点；
- li[last()]：选取最后一个 li 节点；
- position()<3：选取位置小于 3 的 li 节点；
- li[last()-2]：选取倒数第三个 li 节点

输出结果：
```python
['first item']
['fifth item']
['first item', 'second item']
['third item']
```

  # <font color=#ff0000>【7.12】节点轴选择</font>
节点轴选择：获取子元素、兄弟元素、父元素、祖先元素等
```python
from lxml import etree

text = '''
<div>
    <ul>
         <li class="item-0"><a href="link1.html"><span>first item</span></a></li>
         <li class="item-1"><a href="link2.html">second item</a></li>
         <li class="item-inactive"><a href="link3.html">third item</a></li>
         <li class="item-1"><a href="link4.html">fourth item</a></li>
         <li class="item-0"><a href="link5.html">fifth item</a>
     </ul>
 </div>
'''
html = etree.HTML(text)
result = html.xpath('//li[1]/ancestor::*')
print(result)
result = html.xpath('//li[1]/ancestor::div')
print(result)
result = html.xpath('//li[1]/attribute::*')
print(result)
result = html.xpath('//li[1]/child::a[@href="link1.html"]')
print(result)
result = html.xpath('//li[1]/descendant::span')
print(result)
result = html.xpath('//li[1]/following::*[2]')
print(result)
result = html.xpath('//li[1]/following-sibling::*')
print(result)
```
输出结果：
```python
[<Element html at 0x1d3749e9548>, <Element body at 0x1d3749e94c8>, <Element div at 0x1d3749e9488>, <Element ul at 0x1d3749e9588>]
[<Element div at 0x1d3749e9488>]
['item-0']
[<Element a at 0x1d3749e9588>]
[<Element span at 0x1d3749e9488>]
[<Element a at 0x1d3749e9588>]
[<Element li at 0x1d3749e94c8>, <Element li at 0x1d3749e95c8>, <Element li at 0x1d3749e9608>, <Element li at 0x1d3749e9648>]
```

基本语法：轴名称::节点测试[谓语]

轴名称对应的结果：

| 轴名称             | 结果                                                   |
| ------------------ | ------------------------------------------------------ |
| ancestor           | 选取当前节点的所有先辈（父、祖父等）                   |
| ancestor-or-self   | 选取当前节点的所有先辈（父、祖父等）以及当前节点本身   |
| attribute          | 选取当前节点的所有属性                                 |
| child              | 选取当前节点的所有子元素                               |
| descendant         | 选取当前节点的所有后代元素（子、孙等）                 |
| descendant-or-self | 选取当前节点的所有后代元素（子、孙等）以及当前节点本身 |
| following          | 选取文档中当前节点的结束标签之后的所有节点             |
| namespace          | 选取当前节点的所有命名空间节点                         |
| parent             | 选取当前节点的父节点                                   |
| preceding          | 选取文档中当前节点的开始标签之前的所有节点             |
| preceding-sibling | 选取当前节点之前的所有同级节点 |
| self | 选取当前节点  |

实例：

| 例子  | 	结果                         |
| ------------------ | ------------------------------------------------------ |
| child::book	|   选取所有属于当前节点的子元素的 book 节点   |
| attribute::lang	|   选取当前节点的 lang 属性                           |
| child::*	        |     选取当前节点的所有子元素                           |
| attribute::*	  |      选取当前节点的所有属性                              |
| child::text()	 |       选取当前节点的所有文本子节点                  |
| child::node()	|     选取当前节点的所有子节点                          |
| descendant::book	|  选取当前节点的所有 book 后代           |
| ancestor::book	|   选择当前节点的所有 book 先辈              |
| ancestor-or-self::book	|   选取当前节点的所有 book 先辈以及当前节点（如果此节点是 book 节点）|
| child::*/child::price	 |       选取当前节点的所有 price 孙节点   |
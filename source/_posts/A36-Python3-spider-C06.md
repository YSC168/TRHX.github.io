---
title: Python3 爬虫学习笔记 C06
tags:
  - 爬虫
  - 正则表达式
categories: 
  - Python3 学习笔记
  - 爬虫学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/spider.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 爬虫学习笔记第六章 —— 【正则表达式】</font></center>

<!--more-->

# <font color=#ff0000>【6.1】关于正则表达式</font>
正则表达式是对字符串操作的一种逻辑公式，用定义好的特定字符和这些特定字符的组合组成一个规则字符串，这个规则字符串原来表达对字符串的一种过滤逻辑，从而实现字符串的检索、替换、匹配验证等。Python 的 re 库提供了整个正则表达式的实现，包含五种方法：<font color=#ff0000>match、search、findall、sub、compile</font>
常用的匹配规则：

| 模式 | 描述                                                     |
| ---------- | -------------------------------------------------------- |
| \w   | 匹配字母、数字及下划线                                   |
| \W   | 匹配不是字母、数字及下划线的字符                         |
| \s   | 匹配任意空白字符，等价于 [\t\n\r\f]                      |
| \S   | 匹配任意非空字符                                         |
| \d   | 匹配任意数字，等价于 [0-9]                               |
| \D   | 匹配任意非数字的字符                                     |
| \A   | 匹配字符串开头                                           |
| \z   | 匹配字符串结尾，如果存在换行，同时还会匹配换行符         |
| \Z   | 匹配字符串结尾，如果存在换行，只匹配到换行前的结束字符串 |
| \G   |  匹配最后匹配完成的位置    |
| \n   |    匹配一个换行符   |
|  \t  |  匹配一个制表符  |
| ^  | 匹配一行字符串的开头  |
| $   |  匹配一行字符串的结尾  |
| .  |  匹配任意字符，除了换行符，当 re.DOTALL 标记被指定时，则可以匹配包括换行符的任意字符 |
|  [...]   | 用来表示一组字符，单独列出，比如 [amk] 匹配 a、m 或 k  |
|  [^...]   |  不在 [] 中的字符，比如  匹配除了 a、b、c 之外的字符  |
| *  | 匹配 0 个或多个表达式  |
|  +  |  匹配 1 个或多个表达式  |
|  ?    |  匹配 0 个或 1 个前面的正则表达式定义的片段，非贪婪方式  |
|  {n}   |  精确匹配 n 个前面的表达式  |
|  {n, m}   | 匹配 n 到 m 次由前面正则表达式定义的片段，贪婪方式  |
| a\|b   | 匹配 a 或 b |
| ( )  | 匹配括号内的表达式，也表示一个组  |

# <font color=#ff0000>【6.2】re.match 方法</font>
match() 方法会尝试从字符串的起始位置匹配正则表达式，如果匹配，就返回匹配成功的结果；如果不匹配，就返回 None，在 match() 方法中，第一个参数传入正则表达式，第二个参数传入要匹配的字符串。
```python
import re

content = 'This is a Demo_123 4567_I Love China'
print(len(content))
result = re.match('^This\s\w\w\s\w\s\w{5}\d{3}\s\w{6}', content)
print(result)
print(result.group())
print(result.span())
```
输出结果：
```python
36
<_sre.SRE_Match object; span=(0, 25), match='This is a Demo_123 4567_I'>
This is a Demo_123 4567_I
(0, 25)
```
打印 result 结果是 SRE_Match 对象，表明匹配成功。SRE_Match 对象有两种方法：<font color=#ff0000>group()</font> 方法可以输出匹配到的内容；<font color=#ff0000>span()</font> 方法可以输出匹配的范围。

## <font color=#ff0000>【6.2.1】提取内容</font>
使用括号将想提取的子字符串括起来。括号实际上标记了一个子表达式的开始和结束位置，被标记的每个子表达式会依次对应每一个分组，调用 <font color=#ff0000>group()</font> 方法传入分组的索引即可获取提取的结果。
```python
import re

content = 'This is a Demo_123 4567_I Love China'
result = re.match('^This\s\w\w\s\w\s(\w{5})\d{3}\s\w{6}', content)
print(result)
print(result.group())
print(result.group(1))
print(result.span())
```
输出结果：
```python
<_sre.SRE_Match object; span=(0, 25), match='This is a Demo_123 4567_I'>
This is a Demo_123 4567_I
Demo_
(0, 25)
```

## <font color=#ff0000>【6.2.2】通用匹配</font>
如果每个字符都用都用一个符号来匹配的话就显得比较麻烦，可以用 `.*`来匹配，`.` 可以匹配除换行符外的任意字符，`*` 代表匹配前面的字符无限次。
```python
import re

content = 'This is a Demo_123 4567_I Love China'
result = re.match('^This.*China$', content)
print(result)
print(result.group())
print(result.span())
```
输出结果：
```python
<_sre.SRE_Match object; span=(0, 36), match='This is a Demo_123 4567_I Love China'>
This is a Demo_123 4567_I Love China
(0, 36)
```
## <font color=#ff0000>【6.2.3】贪婪匹配</font>
```python
import re

content = 'This is a Demo_1234567_I Love China'
result = re.match('^This.*(\d+).*China$', content)
print(result)
print(result.group())
print(result.group(1))
print(result.span())
```
输出结果：
```python
<_sre.SRE_Match object; span=(0, 35), match='This is a Demo_1234567_I Love China'>
This is a Demo_1234567_I Love China
7
(0, 35)
```
<font color=#ff0000>`.*` 为贪婪匹配，会匹配尽可能多的字符，所以 \d+ 只会匹配到最后一个数字，而不是所有的数字</font>

## <font color=#ff0000>【6.2.4】非贪婪匹配</font>
```python
import re

content = 'This is a Demo_1234567_I Love China'
result = re.match('^This.*?(\d+).*China$', content)
print(result)
print(result.group())
print(result.group(1))
print(result.span())
```
输出结果：
```python
<_sre.SRE_Match object; span=(0, 35), match='This is a Demo_1234567_I Love China'>
This is a Demo_1234567_I Love China
1234567
(0, 35)
```
<font color=#ff0000>`.*?` 为非贪婪匹配，会匹配尽可能少的字符，所以 \d+ 会匹配到所有的数字</font>

## <font color=#ff0000>【6.2.5】转义匹配</font>
当遇到用于正则匹配模式的特殊字符时，在前面加反斜线转义一下即可。例如 `.` 可以用 `\.` 来匹配：
```python
import re

content = '(博客)www.itrhx.com'
result = re.match('\(博客\)www\.itrhx\.com', content)
print(result)
print(result.group())
```
输出结果：
```python
<_sre.SRE_Match object; span=(0, 17), match='(博客)www.itrhx.com'>
(博客)www.itrhx.com
```

## <font color=#ff0000>【6.2.6】修饰符</font>
修饰符用来解决换行、大小写等问题，较为常用的有 re.S 和 re.I。

|  修饰符  |      描述     |
| :----: | :------------------------------------------------------------: |
| re.S | 使 `.` 匹配包括换行在内的所有字符                              |
| re.I | 使匹配对大小写不敏感                                         |
| re.L | 做本地化识别（locale-aware）匹配                             |
| re.M | 多行匹配，影响 `^` 和 `$`                                        |
| re.U | 根据 Unicode 字符集解析字符。这个标志影响 `\w`、`\W`、`\b` 和 `\B`   |
| re.X | 该标志通过给予你更灵活的格式以便你将正则表达式写得更易于理解 |

示例：
```python
import re

content = '''This is a Demo_1234567
          _I Love China'''
result = re.match('^This.*?(\d+).*China$', content)
print(result)
print(result.group())
print(result.group(1))
print(result.span())
```
示例中 content 字段进行了换行处理，如果没有修饰符，就会报错：
```python
Traceback (most recent call last):
None
  File "F:/PycharmProjects/Python3爬虫/test.py", line 7, in <module>
    print(result.group())
AttributeError: 'NoneType' object has no attribute 'group'
```
添加 re.S 修饰符后即可匹配成功：
```python
import re

content = '''This is a Demo_1234567
          _I Love China'''
result = re.match('^This.*?(\d+).*China$', content, re.S)
print(result)
print(result.group())
print(result.group(1))
print(result.span())
```
输出结果：
```python
<_sre.SRE_Match object; span=(0, 46), match='This is a Demo_1234567\n          _I Love China'>
This is a Demo_1234567
          _I Love China
1234567
(0, 46)
```
# <font color=#ff0000>【6.3】re.search 方法</font>
match() 方法只能从字符串的开头开始匹配，一旦开头不匹配，那么整个匹配就失败了，match() 方法更适合用来检测某个字符串是否符合某个正则表达式的规则，而 search() 方法则会扫描整个字符串并返回第一个成功的匹配
```python
import re

content = 'This is a Demo_1234567_I Love China'
result = re.search('a.*?(\d{5})', content)
print(result)
print(result.group(1))
```
输出结果：
```python
<_sre.SRE_Match object; span=(8, 20), match='a Demo_12345'>
12345
```
# <font color=#ff0000>【6.4】re.findall 方法</font>
search() 方法则会扫描整个字符串，但是返回的是第一个成功的匹配，而 findall() 方法将会返回所有成功的匹配
```python
import re

html = '''<div id="songs-list">
        <h2 class="title"> 民谣 </h2>
        <p class="introduction">
        民谣歌曲列表
        </p>
        <ul id="list" class="list-group">
        <li data-view="2"> 七里香 </li>
        <li data-view="7">
        <a href="/2.mp3" singer="赵雷"> 理想 </a>
        </li>
        <li data-view="4" class="active">
        <a href="/3.mp3" singer="许巍"> 像风一样自由 </a>
        </li>
        <li data-view="6"><a href="/4.mp3" singer="安与骑兵"> 红山果 </a></li>
        <li data-view="5"><a href="/5.mp3" singer="薛之谦"> 意外 </a></li>
        <li data-view="5">
        <a href="/6.mp3" singer="马頔"> 但南山南 </a>
        </li>
        </ul>
        </div>'''
results = re.findall('<li.*?href="(.*?)".*?singer="(.*?)">(.*?)</a>', html, re.S)
print(results)
print(type(results))
for result in results:
    print(result)
    print(result[0], result[1], result[2])
```
输出结果：
```python
[('/2.mp3', '赵雷', ' 理想 '), ('/3.mp3', '许巍', ' 像风一样自由 '), ('/4.mp3', '安与骑兵', ' 红山果 '), ('/5.mp3', '薛之谦', ' 意外 '), ('/6.mp3', '马頔', ' 但南山南 ')]
<class 'list'>
('/2.mp3', '赵雷', ' 理想 ')
/2.mp3 赵雷  理想 
('/3.mp3', '许巍', ' 像风一样自由 ')
/3.mp3 许巍  像风一样自由 
('/4.mp3', '安与骑兵', ' 红山果 ')
/4.mp3 安与骑兵  红山果 
('/5.mp3', '薛之谦', ' 意外 ')
/5.mp3 薛之谦  意外 
('/6.mp3', '马頔', ' 但南山南 ')
/6.mp3 马頔  但南山南 
```
# <font color=#ff0000>【6.5】re.sub 方法</font>
与字符串的 replace() 方法类似，sub() 方法可以对文本进行修改，sub() 方法第一个参数为匹配对象，第二个参数为替换成的字符串，如果要去掉匹配对象的话，可以赋值为空，第三个参数为原来的字符串
```python
import re

content = '87dsf4as2w4jh1k4kdl4'
result = re.sub('\d+', '', content)
print(result)
```
输出结果：
```python
dsfaswjhkkdl
```

# <font color=#ff0000>【6.5】re.compile() 方法</font>
compile() 方法可以将正则字符串编译成正则表达式对象，以便在后面的匹配中复用
```python
import re

content1 = '北京时间：2019-08-24 18:30'
content2 = '伦敦时间：2019-08-24 11:30'
content3 = '巴黎时间：2019-08-24 12:30'
content4 = '外星时间：9019-99-66 50:30'
pattern = re.compile('\d{2}:\d{2}')
result1 = re.sub(pattern, '', content1)
result2 = re.sub(pattern, '', content2)
result3 = re.sub(pattern, '', content3)
result4 = re.sub(pattern, '', content4)
print(result1, result2, result3, result4)
```
利用 compile() 方法将正则表达式编译成一个正则表达式对象，以便复用，然后用 sub() 方法去掉具体时间
输出结果：
```python
北京时间：2019-08-24  伦敦时间：2019-08-24  巴黎时间：2019-08-24  外星时间：9019-99-66 
```
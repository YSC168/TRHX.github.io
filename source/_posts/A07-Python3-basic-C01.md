---
title: Python3 基础学习笔记 C01
tags: 
  - 数据类型
  - 变量
categories: 
  - Python3 学习笔记
  - 基础学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/python.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 基础学习笔记第一章 —— 【变量和简单数据类型】</font></center>

<!--more-->

----------
 # <font color=#FF0000> - 1.1变量的命名和使用 </font> 

> 变量名只能包含字母、数字和下划线。变量名可以字母或者下划线打头，但不能以数字开头，例如，可以将变量命名为message_1，但不能将其命名为1_message

> 变量名不能包含空格，但可使用下划线来分割其中的单词，例如，变量名greeting_message可行，但变量名greeting message会引发错误

> 不要将Python关键字和函数名用作变量名，即不要使用Python保留用于特殊用途的单词，如print

> 变量名应既简短又具有描述性，例如，name比n好，student_name比s_n好，name_length比length_of_persons_name好

> 慎用小写字母l和大写字母O，因为它们可能被人看错成数字1和0

 # <font color=#FF0000> - 1.2字符串 </font> 
 字符串就是一系列字符，在Python中，用引号括起来的都是字符串，其中的引号可以是单引号也可以双引号：

``` python
 "This is a string."
 'This is also a string.'
```
这种灵活性让我们能够在字符串中包含引号和撇号：

``` python
'I told my friend,"Python is my favorite language!"'
"The language 'Python' is named er Monty Python,not the snake."
"One of Python's strengths is i diverse and supportive community."
```

# <font color=#FF0000> - 1.2.1使用方法修改字符串的大小写</font>
三种处理方法如下：
``` python
title()     #将字符串每个单词的首字母都改为大写
upper()     #将字符串的每个字母都改为大写
lower()     #将字符串的每个字母都改为小写
```
例如：
``` python
message = "I love you!"
print(name.title())
print(name.upper())
print(name.lower())
```
输出结果如下：
``` python
I Love You!
I LOVE YOU!
i love you!
```

# <font color=#FF0000> - 1.2.2合并（拼接）字符串</font>
Python使用加号（+）来合并字符串，举例说明：

``` python
first_name = "I"
second_name = "love"
third_name = "python"
full_name = first_name + " " + second_name + " " + third_time
print(full_name.title() + "!")
```

输出结果如下：

``` python
I Love Python!
```
# <font color=#FF0000> - 1.2.3使用制表符或换行符来添加空白</font>
添加横向制表符：

``` python
>>>print("\tPython")
    Python
```
添加换行符：

``` python
>>>print("C\nC++\nPython\nJavaScript")
C
C++
Python
JavaScript
```

<center><font color=#FF0000> 附表：Python转义符 </font>

![](https://i.imgur.com/Do9ja5A.png)

</center>

# <font color=#FF0000> - 1.2.4删除空白</font>
在Python中可用 lstrip()、rstrip()、strip() 分别删除字符串开头、结尾、全部的空白，举例说明：

``` python
>>>message = ' python '
>>>message
' python '
>>>message.lstrip()
'python '
>>>message.rstrip()
' python'
>>>message.strip()
'python'
```
如果要永久删除字符串中的空白，必须将删除操作的结果存回到变量中：

``` python
>>>message = ' python '
>>>message = message.strip()
>>>message
'python'
```
# <font color=#FF0000> - 1.3数字</font>
在编程中，经常使用数字来记录游戏得分、表示可视化数据、储存Web应用信息等。Python根据数字的用法以不同的方式处理它们

# <font color=#FF0000> - 1.3.1整数</font>
在Python中，可对整数执行加（+）减（-）乘（*）除（/）乘方（**）运算，同时也支持运算次序：

``` python
>>>3 + 2
5
>>>3 - 2
1
>>>3 * 2
6
>>>3 \ 2
1.5
>>>3 ** 2
9
>>>3 ** 3
27
>>>2 + 3 * 4
14
>>>(2 + 3) * 4
20
```
# <font color=#FF0000> - 1.3.2浮点数</font>
Python将带小数点的数字都称为浮点数：

``` python
>>>0.1 + 0.1
0.2
>>>2 * 0.2
0.4
```
需要注意的是，结果包含的小数位可能是不确定的，就现在而言，暂时忽略多余的小数位即可：

``` python
>>>0.2 + 0.1
0.30000000000000004
>>>3 * 0.1
0.30000000000000004
```
# <font color=#FF0000> - 1.3.3使用函数 str() 避免错误</font>
错误例子：

``` python
age = 23
message = "Happy " + age + "rd Birthday!"
print(message)
```
运行时会报错：

``` python
Traceback (most recent call last):
  File "birthday.py", line 2, in <module>
    message = "Happy " + age + "rd Birthday!"
TypeError: must be str, not int
```
这是一个类型错误，意味着Python无法识别我们使用的信息。在这个例子中，Python发现我们使用了一个值为整数（int）的变量，但它不知道该如何解读这个值，这个变量表示的可能是数值23，也可能是字符2和3。像上面这样的字符串中使用整数时，需要显式地指出我们希望Python将这个整数用作字符串。为此，可调用函数 str()，它让Python将非字符串值表示为字符串：

``` python
age = 23
message = "Happy " + str(age) + "rd Birthday!"
print(message)
```
输出结果如下：

``` python
Happy 23rd Birthday!
```
# <font color=#FF0000> - 1.4注释</font>
注释让我们能够使用自然语言在程序中添加说明，Python中注释有三种方法：

``` python
print("Hello Python!")

#这是单行注释

'''这是多行注释
这是多行注释'''

"""这也是多行注释
这也是多行注释"""
```


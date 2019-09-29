---
title: Python3 基础学习笔记 C02
tags: 
  - 列表
categories: 
  - Python3 学习笔记
  - 基础学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/python.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 基础学习笔记第二章 —— 【列表】</font></center>

<!--more-->

----------
 # <font color=#FF0000> - 2.1列表是什么 </font>
 列表由一系列按特定顺序的元素组成，在 Python 中用方括号（ [ ] ）来表示列表，并用逗号来分隔其中的元素，例：


``` python
list1 = ['a','b','c','d','e','f']
list2 = ['abc', 'xyz', 2018, 2020]
list3 = [1, 2, 3, 4, 5 ,6]
list4 = ["a", "b", "c", "d"]
print(list1, list2, list3 ,list4)
```

 输出结果如下：

``` python
 ['a', 'b', 'c', 'd', 'e', 'f'] ['abc', 'xyz', 2018, 2020] [1, 2, 3, 4, 5, 6] ['a', 'b', 'c', 'd']
```

 # <font color=#FF0000> - 2.1.1访问列表元素 </font>
 列表是有序集合，因此要访问列表的元素，只需要将该元素的位置或索引告诉Python即可，<font color=#FF0000>注意：在Python中的第一个列表元素的索引为0，而不是1</font>
 

``` python
list = ['a','b','c','d','e','f']
print(list[0])
print(list[3])
print(list[-1])   #Python为访问最后一个列表元素提供了一种特殊语法，通过将索引指定为-1，可以让Python返回最后一个列表元素
print(list[-3])
```
输出结果如下：

``` python
a
d
f
d
```
# <font color=#FF0000> - 2.1.2列表切片 </font>

``` python
list = ['a','b','c','d','e','f']
print(list[:])     #省略全部，代表截取全部内容，可以用来将一个列表拷给另一个列表
print(list[:3])     #省略起始位置的索引，默认起始位置从头开始，结束位置索引为2
print(list[3:])     #省略结束位置的索引，默认结束位置为最后一个，开始位置索引为3
print(list[1:4])    #开始位置索引为1，结束位置索引为3，顾头不顾尾
print(list[4:1])    #从左到右索引，因此为空值
print(list[-1:-3])   #从左到右索引，因此为空值
print(list[-3:-1])   #开始位置索引为倒数第三个，结束位置索引为倒数第二个
print(list[1:5:2])   #开始位置索引为1，结束位置索引为4，间隔2
print(list[5:1:-1])   #反向取值，开始位置索引为5，结束位置索引为2
print(list[::-1])    #反向取值，反向输出列表
```

输出结果如下：

``` python
['a', 'b', 'c', 'd', 'e', 'f']
['a', 'b', 'c']
['d', 'e', 'f']
['b', 'c', 'd']
[]
[]
['d', 'e']
['b', 'd']
['f', 'e', 'd', 'c']
['f', 'e', 'd', 'c', 'b', 'a']
```

 # <font color=#FF0000> - 2.1.3使用列表中的各个值 </font>
 可像使用其他变量一样使用列表中的各个值，例如，我们可以使用拼接根据列表中的值来创建消息：
 

``` python
 list = ['python', 'c', 'c++', 'java', 'php']
 message = "My favorite language is " + list[0].title() + "!"
 print(message)
```

输出结果如下：

``` python
My favorite language is Python!
```

 # <font color=#FF0000> - 2.1.4修改元素 </font>
 
 修改列表元素的语法与访问列表元素的语法类似，要修改列表元素，可指定列表名和要修改的元素的索引，再次指定该元素的新值：
 
``` python
 names = ['zhangsan', 'lishi', 'wanger', 'liming', 'xiaowang']
 print(names)
 names[1] = 'lifang'
 print(names)
```
输出结果如下：

``` python
['zhangsan', 'lishi', 'wanger', 'liming', 'xiaowang']
['zhangsan', 'lifang', 'wanger', 'liming', 'xiaowang']
```

 # <font color=#FF0000> - 2.1.5添加元素 </font>
 <font color=#FF0000> - 使用方法 append() 在列表末尾添加元素 </font>
``` python
list = ['a', 'b', 'c', 'd', 'e', 'f']
print(list)
list.append('g')
print(list)
```
输出结果如下：

``` python
['a', 'b', 'c', 'd', 'e', 'f']
['a', 'b', 'c', 'd', 'e', 'f', 'g']
```

 <font color=#FF0000> - 使用方法 insert() 在列表指定位置添加元素 </font>
 
 ``` python
list = ['a', 'b', 'c', 'd', 'e', 'f']
print(list)
list.insert(2,"h")    #其中括号里的数字表示要插入的位置，此后面的元素将右移一个位置
print(list)
```
输出结果如下：

``` python
['a', 'b', 'c', 'd', 'e', 'f']
['a', 'b', 'h', 'c', 'd', 'e', 'f', 'g']
```
 # <font color=#FF0000> - 2.1.6删除元素 </font>
 
<font color=#FF0000> - 使用 del 语句删除元素 </font>

``` python
list = ['a', 'b', 'c', 'd', 'e', 'f']
print(list)
del list[3]
print(list)
```
输出结果如下：

``` python
list = ['a', 'b', 'c', 'd', 'e', 'f']
list = ['a', 'b', 'c', 'e', 'f']
```
<font color=#FF0000> - 使用方法 pop() 删除最后一个元素</font>
方法 pop() 可以删除列表末尾的元素，并让你能够接着使用它。术语<font color=#FF0000>弹出（pop）</font>源自这样的类比：列表就像是一个栈，而删除列表末尾的元素就相当于弹出栈顶元素：

``` python
list = ['a', 'b', 'c', 'd', 'e', 'f']
print(list)
new_list = list.pop()
print(list)
print(new_list)
```
输出结果如下：

``` python
['a', 'b', 'c', 'd', 'e', 'f']
['a', 'b', 'c', 'd', 'e']
f
```
<font color=#FF0000> - 使用方法 pop() 删除任意位置元素</font>
可以使用 pop() 来删除列表中任何位置的元素，只需要在括号中指定要删除的元素的索引即可：

``` python
list = ['a', 'b', 'c', 'd', 'e', 'f']
print(list)
new_list = list.pop(1)
print(list)
print(new_list)
```
输出结果如下：

``` python
['a', 'b', 'c', 'd', 'e', 'f']
['a', 'c', 'd', 'e', 'f']
b
```
<font color=#FF0000> - 使用方法 remove() 删除未知位置元素</font>
当我们不知道元素的位置，只知道元素的值的时候，就可以使用方法 remove()

``` python
list = ['a', 'b', 'c', 'd', 'e', 'f']
print(list)
list.remove('d')
print(list)
```
输出结果如下：

``` python
['a', 'b', 'c', 'd', 'e', 'f']
['a', 'b', 'c', 'e', 'f']
```
 # <font color=#FF0000> - 2.1.7使用方法 index() 查找指定元素位置 </font>

``` python
list = ["a", "b", "c", "d", "e", "a"]
print(list.index('c')) 
```
输出结果如下：

``` python
2
```

# <font color=#FF0000> - 2.1.8使用方法 count() 统计指定元素数量 </font>

``` python
list = ["a", "b", "c", "d", "e", "a"]
print(list.count('a')) 
```
输出结果如下：

``` python
2
```
# <font color=#FF0000> - 2.1.9清空列表 </font>

``` python
list = ["a", "b", "c", "d", "e", "a"]
list.clear()
print(list)
```

输出结果如下：

``` python
[]
```

# <font color=#FF0000> - 2.2组织列表 </font>
在创建的列表中，元素的排列顺序常常是无法预测的，因为我们并非总能控制用户提供数据的顺序。有时候，我们希望保留列表元素最初的排列顺序，而有时候又需要调整排列顺序。Python提供了很多组织列表的方式，可根据具体情况选用

# <font color=#FF0000> - 2.2.1使用方法 sort() 对列表进行永久排序 </font>
使用方法 sort() 可以对列表按照特殊符号，数字，大写字母，小写字母顺序进行永久排序：

``` python
cars = ['bmw', 'audi', 'toyota', 'subaru']
cars.sort()
print(cars)
```
输出结果如下：

``` python
['audi', 'bmw', 'subaru', 'toyota']
```
还可以按与字母顺序相反的顺序排列列表元素，只需要向 sort() 方法传递参数 reverse = True 就可以了：

``` python
cars = ['bmw', 'audi', 'toyota', 'subaru']
cars.sort(reverse = True)
print(cars)
```
输出结果如下：

``` python
['toyota', 'subaru', 'bmw', 'audi']
```
# <font color=#FF0000> - 2.2.2使用函数 sorted() 对列表进行临时排序 </font>
要保留列表元素原来的排列顺序，同时以特定的顺序呈现它们，可使用函数sorted()。函数sorted()让你能够按特定顺序显示列表元素，同时不影响它们在列表中的原始排列顺序：

``` python
cars = ['bmw', 'audi', 'toyota', 'subaru']
print("Here is the original list:")
print(cars)
print("\nHere is the sorted list:")
print(sorted(cars))
print("\nHere is the sorted reverse list:")
print(sorted(cars, reverse=True))
print("\nHere is the original list again:")
print(cars)
```
输出结果如下：

``` python
Here is the original list:
['bmw', 'audi', 'toyota', 'subaru']

Here is the sorted list:
['audi', 'bmw', 'subaru', 'toyota']

Here is the sorted reverse list:
['toyota', 'subaru', 'bmw', 'audi']

Here is the original list again:
['bmw', 'audi', 'toyota', 'subaru']
```
# <font color=#FF0000> - 2.2.3使用方法 reverse() 对列表进行反向排序 </font>
要反转列表元素的排列顺序，可使用方法 reverse() 

``` python
cars = ['bmw', 'audi', 'toyota', 'subaru']
cars.reverse()
print(cars)
```
输出结果如下：

``` python
['subaru', 'toyota', 'audi', 'bmw']
```
# <font color=#FF0000> - 2.2.4确定列表的长度 </font>
使用函数 len() 可以快速获悉列表的长度：

``` python
>>>cars = ['bmw', 'audi', 'toyota', 'subaru']
>>>len(cars)
4
```
# <font color=#FF0000> - 2.2.5合并列表 </font>
<font color=#FF0000> - 使用方法 extend() 合并列表</font>

``` python
list1 = [1, 2, 3, 4]
list2 = ['a', 'b', 'c', 'd']
list1.extend(list2)    #将列表list2添加到list1当中去
print(list1)
print(list2)
```

输出结果如下：

``` python
[1, 2, 3, 4, 'a', 'b', 'c', 'd']
['a', 'b', 'c', 'd']
```
<font color=#FF0000> - 使用 "+" 号合并列表</font>

``` python
list1 = [1, 2, 3, 4]
list2 = ['a', 'b', 'c', 'd']
print(list1 + list2)
print(list2 + list1)
```

输出结果如下：

``` python
[1, 2, 3, 4, 'a', 'b', 'c', 'd']
['a', 'b', 'c', 'd', 1, 2, 3, 4]
```
<font color=#FF0000> - 使用切片合并列表</font>

``` python
list1 = [1, 2, 3, 4]
list2 = ['a', 'b', 'c', 'd']
list1[len(list1) : len(list1)] = list2   #len(list1)代表要将list2插入list1中的位置
print(list1)

list1 = [1, 2, 3, 4]
list2 = ['a', 'b', 'c', 'd']
list1[0 :0] = list2
print(list1)

list1 = [1, 2, 3, 4]
list2 = ['a', 'b', 'c', 'd']
list1[1:1] = list2
print(list1)
```
输出结果如下：

``` python
[1, 2, 3, 4, 'a', 'b', 'c', 'd']
['a', 'b', 'c', 'd', 1, 2, 3, 4]
[1, 'a', 'b', 'c', 'd', 2, 3, 4]
```
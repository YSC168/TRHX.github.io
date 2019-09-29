---
title: Python3 基础学习笔记 C03
tags: 
  - 操作列表
categories: 
  - Python3 学习笔记
  - 基础学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/python.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 基础学习笔记第三章 —— 【操作列表】</font></center>

<!--more-->

----------
 # <font color=#FF0000> - 3.1遍历整个列表 </font>
 使用 for 循环来遍历整个列表：

``` python
 names = ['alice' , 'david' , 'liwei']
 for name in names:
	print(name)
```
 输出结果如下：

``` python
alice
david
liwei
```
for循环让Python从列表names中取出一个名字，并将其储存在变量name中，最后  让Python打印前面储存到变量name中的名字，对于列表中的每个名字，Python都将 重复执行后两行代码，将列表names中的每个名字都打印出来

 #  <font color=#FF0000>- 3.1.1在for循环中执行更多的操作 </font>
在for循环中，可对每个元素执行任何操作，下面对前面的示例进行扩展：

例一：
``` python
names = ['alice' , 'david' , 'liwei']
for name in names:
	print(name.title() + ", that was a good man!")
```
输出结果如下：

``` python
Alice, that was a good man!
David, that was a good man!
Liwei, that was a good man!
```
例二：

``` python
names = ['alice' , 'david' , 'liwei']
for name in names:
	print(name.title() + ", that was a good man!")
	print("I can't wait to see you again," + name.title() + ".\n")
print("Nice to meet you!")
```

输出结果如下：

``` python
Alice, that was a good man!
I can't wait to see you again,Alice.

David, that was a good man!
I can't wait to see you again,David.

Liwei, that was a good man!
I can't wait to see you again,Liwei.

Nice to meet you!
```

 #  <font color=#FF0000>- 3.2 range()函数 </font>

> Python使用range()函数能够轻松地生成一系列的数字

> Python3 range() 函数返回的是一个可迭代对象（类型是对象），而不是列表类型， 所以打印的时候不会打印列表；

> Python3 list() 函数是对象迭代器，可以把range()返回的可迭代对象转为一个列表，返回的变量类型为列表；

> Python2 range() 函数返回的是列表

例一：
``` python
for i in range(1,5):
	print(i)
```
输出结果如下：

``` python
1
2
3
4
```
例二：
``` python
for i in range(5):
	print(i)
```
输出结果如下：
``` python
0
1
2
3
4
```
例三：
``` python
>>> list(range(5))
[0, 1, 2, 3, 4]
>>> list(range(0))
[]
>>>list(range(0, 30, 5))
[0, 5, 10, 15, 20, 25]
>>> list(range(0, 10, 2))
[0, 2, 4, 6, 8]
>>> list(range(0, -10, -1))
[0, -1, -2, -3, -4, -5, -6, -7, -8, -9]
>>> list(range(1, 0))
[]
```
例四：

``` python
squares = []
for value in range(1,11):
	square = value ** 2
	squares.append(square)
print(squares)
```

输出结果如下：

``` python
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```
 #  <font color=#FF0000>- 3.2.1 对数字列表执行简单的统计计算 </font>

``` python
 >>> digits = [1, 2 , 3 , 4 , 5 , 6 , 7 , 8 , 9 , 0]
 >>> min(digits)
 0
 >>>max(digits)
 9
 >>>sum(digits)
 45
```
 #  <font color=#FF0000>- 3.2.2 列表解析 </font>
 列表解析能够让比如3.2中的例四更加简化，只需要一行代码就能生成这样的列表，列表解析将for循环和创建新元素的代码合并成一行，并自动附加新元素：

``` python
 squares = [value ** 2 for value in range(1,11)]
 print(squares)
```

在这个示例中，for循环为for value in range(1,11)，它将值1~10提供给表达式value ** 2
输出结果如下：

``` python
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```
 #  <font color=#FF0000>- 3.3 使用列表的一部分 </font>
 处理列表的部分元素——Python称之为切片
# <font color=#FF0000>- 3.3.1 切片 </font>
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
# <font color=#FF0000>- 3.3.2 遍历列表 </font>

``` python
players = ['charles' , 'martina' , 'michael' , 'florence' , 'eli']
print("Here are the first three players on my team:")
for player in players[:3]:
	print(player.title())
```
输出结果如下：

``` python
Here are the first three players on my team:
Charles
Martina
Michael
```
# <font color=#FF0000>- 3.3.3 复制列表 </font>
要复制列表，可以创建一个包含整个列表的切片，方法是同时省略起始索引和终止索引`（[:]）`，这让Python创建一个始于第一个元素，终止于最后一个元素的切片，即复制整个列表：

``` python
my_foods = ['pizza' , 'falafel' , 'carrot cake']
friend_foods = my_foods[:]
print("My favorite foods are:")
print(my_foods)
print("\nMy friend's favorite foods are:")
print(friend_foods)
```

输出结果如下：

``` python
My favorite foods are:
['pizza', 'falafel', 'carrot cake']

My friend's favorite foods are:
['pizza', 'falafel', 'carrot cake']
```
为核实我们的确有两个列表，下面在每个列表中都添加一种食品，并核实每个列表都记录了相应人员喜欢的食品：
``` python
my_foods = ['pizza' , 'falafel' , 'carrot cake']
friend_foods = my_foods[:]

my_foods.append('cannoli')
friend_foods.append('ice cream')

print("My favorite foods are:")
print(my_foods)
print("\nMy friend's favorite foods are:")
print(friend_foods)
```

输出结果如下：

``` python
My favorite foods are:
['pizza', 'falafel', 'carrot cake', 'cannoli']

My friend's favorite foods are:
['pizza', 'falafel', 'carrot cake', 'ice cream']
```
输出结果表明，'cannoli'包含在我喜欢的食品列表中，而'ice cream'没有；'ice cream'包含在我朋友喜欢的食品中，而'cannoli'没有，假如我们只是简单的将my_foods赋给friend_foods，就不能得到两个列表。下面是错误示例：

``` python
my_foods = ['pizza' , 'falafel' , 'carrot cake']
friend_foods = my_foods    #错误写法

my_foods.append('cannoli')
friend_foods.append('ice cream')

print("My favorite foods are:")
print(my_foods)
print("\nMy friend's favorite foods are:")
print(friend_foods)
```
错误示例输出结果如下：

``` python
My favorite foods are:
['pizza', 'falafel', 'carrot cake', 'cannoli', 'ice cream']

My friend's favorite foods are:
['pizza', 'falafel', 'carrot cake', 'cannoli', 'ice cream']
```
 #  <font color=#FF0000>- 3.4 元组 </font>
 Python将不能修改的值称为不可变的，而不可变的列表被称为元组
  #  <font color=#FF0000>- 3.4.1 定义元组 </font>
  元组看起来就像是列表，但元组使用圆括号而不是方括号来标识，定义元组后，就可以使用索引来访问其元素，就像访问列表元素一样：

``` python
dimensions = (200,50)
print(dimensions[0])
print(dimensions[1])
```

  输出结果如下：

``` python
200
50
```
如果尝试修改元组中元素的值，将会导致Python返回类型错误消息，由于试图修改元组的操作是被禁止的，因此Python指出不能给元组的元素赋值：

``` python
dimensions = (200,50)
dimensions[0] = 300
```

  将会报错：

``` python
Traceback (most recent call last):
  File "dimensions.py", line 2, in <module>
    dimensions[0] = 300
TypeError: 'tuple' object does not support item assignment
```
  #  <font color=#FF0000>- 3.4.2 遍历元组中所有的值 </font>
  像列表一样，元组也可以使用for循环来遍历元组中的所有值：
  
例一：
``` python
dimensions = (200,100,50,6)
for dimension in dimensions:
	print(dimension)
```
输出结果如下：

``` python
200
100
50
6
```
例二：

``` python
dimensions = (200,100,50,6)
for dimension in dimensions[:3]:
	print(dimension)
```
输出结果如下：

``` python
200
100
50
```
  #  <font color=#FF0000>- 3.4.3 修改元组变量 </font>
  虽然不能修改元组元素，但是可以给储存元组的变量赋值：

``` python
dimensions = (200,50)
print("Original dimensions:")
for dimension in dimensions:
	print(dimension)
	
dimensions = (400,100)
print("\nModified dimensions:")
for dimension in dimensions:
	print(dimension)
```
输出结果如下：

``` python
Original dimensions:
200
50

Modified dimensions:
400
100
```
我们首先定义了一个元组，并将其储存的尺寸打印了出来；然后将一个新元组储存到变量dimensions中，打印新的尺寸；相比于列表，元组是更简单的数据结构。如果需要储存的一组值在程序的整个生命周期内都不变，可使用元组
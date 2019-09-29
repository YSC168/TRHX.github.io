---
title: Python3 基础学习笔记 C05
tags: 字典
categories: 
  - Python3 学习笔记
  - 基础学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/python.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 基础学习笔记第五章 —— 【字典】</font></center>

<!--more-->

----------
 # <font color=#FF0000> - 5.1 一个简单的字典 </font>

``` python
fruits = {'apple' : 'red' , 'number' : 5}
print(fruits['apple'])
print(fruits['number'])
```
输出结果如下：

``` python
red
5
```
在Python中，字典是一系列<font color = #FF000>键-值对</font>。每个键都与一个值相关联，你可以使用键来访问与之相关联的值。与键相关联的值可以是数字、字符串、列表乃至字典。事实上，可以将任何Python对象用作字典中的值。<font color=#FF0000>键-值对</font>是两个相关联的值。在指定键时，Python将返回与之相关联的值。键和值之间用冒号分隔，而<font color = #FF000>键-值对</font>之间用逗号分隔。在字典中，想储存多少个<font color = #FF000>键-值对</font>都可以

 # <font color=#FF0000> - 5.1.1 访问字典中的值 </font>
 要获取与键相关联的值，可依次指定字典名和放在方括号内的键：

``` python
fruits = {'apple' : 'red' , 'number' : 5}
number_fruits = fruits['number']
print("The number of apple is " + str(number_fruits) + "!")
```
输出结果如下：

``` python
The number of apple is 5!
```
 # <font color=#FF0000> - 5.1.2 添加键-值对 </font>
 字典是一种动态结构，可随时在其中添加键-值对。要添加键-值对，可依次指定字典名、用方括号括起来的键和相关联的值

``` python
fruits = {'apple' : 'red' , 'number1' : 5}
print(fruits)
fruits['banana'] = 'yellow'
fruits['number2'] = 13
print(fruits)
```
输出结果如下：

``` python
{'apple': 'red', 'number1': 5}
{'apple': 'red', 'number1': 5, 'banana': 'yellow', 'number2': 13}
```
<font color = #FF0000>注意：键-值对的排列顺序与添加顺序不同。Python不关心键-值对的添加顺序，而只关心键和值之间的关联关系</font>

有时候为了方便也可以先使用一对空的花括号定义一个字典，再分行添加各个键-值对：

``` python
fruits = {}
fruits['banana'] = 'yellow'
fruits['number2'] = 13
print(fruits)
```

输出结果如下：

``` python
{'banana': 'yellow', 'number2': 13}
```

 # <font color=#FF0000> - 5.1.3 修改字典中的值 </font>
 要修改字典中的值，可依次指定字典名、用方括号括起来的键以及与该键相关联的新值

``` python
fruits = {'color' : 'red'}
print("The color of the fruits is " + fruits['color'] + "!")
fruits['color'] = 'yellow'
print("The color of the fruits is " + fruits['color'] + " now!")
```
输出结果如下：

``` python
The color of the fruits is red!
The color of the fruits is yellow now!
```
进阶：对一个能够以不同速度移动的外星人的位置进行跟踪，为此，我们将储存该外星人的当前速度，并据此确定该外星人将向右移动多远：

``` python
alien = {'x_position': 0, 'y_position': 25, 'speed': 'medium'}
print("Original x-position: " + str(alien['x_position']))

#向右移动外星人，据外星人当前速度决定将其移动多远
if alien['speed'] == 'slow':
    x_increment = 1
elif alien['speed'] == 'medium':
    x_increment = 2
else:
    x_increment = 3

#新位置等于老位置加上增量
alien['x_position'] = alien['x_position'] + x_increment
print("New x_position: " + str(alien['x_position']))
```
输出结果如下：

``` python
Original x-position: 0
New x_position: 2
```
 # <font color=#FF0000> - 5.1.4 删除键-值对 </font>
 对于字典中不再需要的信息，可使用del语句将相应的键-值对彻底删除。使用del语句时，必须指定字典名和要删除的键

``` python
fruits = {'apple' : 'red' , 'number' : 5}
print(fruits)
del fruits['number']
print(fruits)
```
输出结果如下：

``` python
{'apple': 'red', 'number': 5}
{'apple': 'red'}
```
 # <font color=#FF0000> - 5.1.5 由类似对象组成的字典 </font>
 字典储存的可以是一个对象的多种信息，也可以储存众多对象的同一种信息，例如要调查很多人最喜欢的编程语言：

``` python
favorite_languages = {
    'jen' : 'python' ,
    'sarah' : 'c' ,
    'edward' : 'ruby' ,
    'phil' : 'java' ,
    }
print("Sarah's favorite languages is " + favorite_languages['sarah'].title() + "!")
```
输出结果如下：

``` python
Sarah's favorite languages is C!
```
 # <font color=#FF0000> - 5.2 遍历字典 </font>
 # <font color=#FF0000> - 5.2.1 方法 items() 遍历所有的键-值对 </font>
使用for循环来遍历字典：
``` python
name = {
    'username' : 'efermi' ,
    'first' : 'enrico' ,
    'last' : 'fermi' ,
    }
for key , value in name.items():
    print("\nKey: " + key)
    print("Value: " + value)
```
输出结果如下：
``` python

Key: username
Value: efermi

Key: first
Value: enrico

Key: last
Value: fermi
```
for语句的第二部分包含字典和方法items()，它返回一个键-值对列表。接下来，for循环依次将每个键-值对储存到指定的两个变量中

``` python
favorite_languages = {
    'jen' : 'python' ,
    'sarah' : 'c' ,
    'edward' : 'ruby' ,
    'phil' : 'java' ,
    }
for name, language in favorite_languages.items():
    print(name.title() + "'s favorite language is " + language.title() + ".")
```
输出结果如下：

``` python
Jen's favorite language is Python.
Sarah's favorite language is C.
Edward's favorite language is Ruby.
Phil's favorite language is Java.
```
 # <font color=#FF0000> - 5.2.2 方法 keys() 遍历字典中所有的键 </font>
 在不需要使用字典中的值时，方法key()很有用，下面来遍历字典favorite_languages，并将每个被调查者的名字都打印出来：

``` python
favorite_languages = {
    'jen' : 'python' ,
    'sarah' : 'c' ,
    'edward' : 'ruby' ,
    'phil' : 'java' ,
    }
for name in favorite_languages.keys():
    print(name.title())
```

输出结果如下：

``` python
Jen
Sarah
Edward
Phil
```
遍历字典时，会默认遍历所有的键，因此，如果将上述代码中的`for name in favorite_languages.keys():`替换为`for name in favorite_languages:`输出结果将不变
进阶：

``` python
favorite_languages = {
    'jen' : 'python' ,
    'sarah' : 'c' ,
    'edward' : 'ruby' ,
    'phil' : 'java' ,
    }
friends = ['phil', 'sarah']
for name in favorite_languages.keys():
    print(name.title())
    if name in friends:
        print("Hi " + name + ", I see your favorite languages is " + favorite_languages[name].title() + "!")
```
输出结果如下：

``` python
Jen
Sarah
Hi sarah, I see your favorite languages is C!
Edward
Phil
Hi phil, I see your favorite languages is Java!
```
 # <font color=#FF0000> - 5.2.3 函数 sorted() 按顺序遍历字典中的所有键 </font>
字典总是明确地记录键和值之间的关联关系，但获取字典的元素时，获取顺序是不可预测的，要以特定的顺序返回元素，一种办法是在for循环中对返回的键进行排序，为此，可以使用函数sorted()来获得按特定顺序排列的键列表的副本：

``` python
favorite_languages = {
    'jen' : 'python' ,
    'sarah' : 'c' ,
    'edward' : 'ruby' ,
    'phil' : 'java' ,
    }
for name in sorted(favorite_languages.keys()):
    print(name.title())
```
输出结果如下：

``` python
Edward
Jen
Phil
Sarah
```
# <font color=#FF0000> - 5.2.4 方法 values() 遍历字典中的所有值 </font>

``` python
favorite_languages = {
    'jen' : 'python' ,
    'sarah' : 'c' ,
    'edward' : 'ruby' ,
    'phil' : 'java' ,
    }
for languages in favorite_languages.values():
    print(languages.title())
```
输出结果如下：

``` python
Python
C
Ruby
Java
```
这种做法提取字典中所有的值，而没有考虑是否重复，<font color=#FF000>为剔除重复项，可使用集合(set)</font>，集合类似于列表，但每个元素都必须是独一无二的：

``` python
favorite_languages = {
    'jen' : 'python' ,
    'sarah' : 'c' ,
    'edward' : 'ruby' ,
    'phil' : 'python' ,
    }
for languages in set(favorite_languages.values()):
    print(languages.title())
```
输出结果如下：

``` python
C
Python
Ruby
```
# <font color=#FF0000> - 5.3 嵌套 </font>
有时候，需要将一系列字典储存在列表中，或将列表作为值储存在字典中，这称为嵌套。可以在列表中嵌套字典、在字典中嵌套列表甚至在字典中嵌套字典
# <font color=#FF0000> - 5.3.1 字典列表 </font>
下面代码创建三个字典，每个字典都表示一个个学生，将这三个字典都放到一个名为students的列表当中，遍历列表将每个学生都打印出来：

``` python
student_0 = {'name' : 'anily' , 'class' : 2}
student_1 = {'name' : 'nikey' , 'class' : 5}
student_2 = {'name' : 'heyk' , 'class' : 3}
students = [student_0 , student_1 , student_2]
for student in students:
    print(student)
```
输出结果如下：

``` python
{'name': 'anily', 'class': 2}
{'name': 'nikey', 'class': 5}
{'name': 'heyk', 'class': 3}
```
进阶：使用 `range()` 自动生成三十个外星人：

``` python
#创建一个用于存储外星人的空列表
aliens = []

#创建三十个绿色的外星人
for alien_number in range(30):
    new_alien = {'color' : 'green' , 'points' : 5 , 'speed' : 'slow'}
    aliens.append(new_alien)

#显示前五个外星人
for alien in aliens[:5]:
    print(alien)
print("......")

#显示创建了多少外星人
print("Total number of aliens: " + str(len(aliens)))
```
输出结果如下：

``` python
{'color': 'green', 'points': 5, 'speed': 'slow'}
{'color': 'green', 'points': 5, 'speed': 'slow'}
{'color': 'green', 'points': 5, 'speed': 'slow'}
{'color': 'green', 'points': 5, 'speed': 'slow'}
{'color': 'green', 'points': 5, 'speed': 'slow'}
......
Total number of aliens: 30
```
在上述例子中，虽然每个外星人都具有相同特征，但在Python看来，每个外星人都是独立的，我们可以独立地修改每个外星人：

``` python
aliens = []
for alien_number in range(30):
    new_alien = {'color' : 'green' , 'points' : 5 , 'speed' : 'slow'}
    aliens.append(new_alien)
for alien in aliens[0:3]:
    if alien['color'] == 'green':
        alien['color'] = 'yellow'
        alien['points'] = 10
        alien['speed'] = 'medium'
for alien in aliens[:5]:
    print(alien)
print("......")
print("Total number of aliens: " + str(len(aliens)))
```
输出结果如下：

``` python
{'color': 'yellow', 'points': 10, 'speed': 'medium'}
{'color': 'yellow', 'points': 10, 'speed': 'medium'}
{'color': 'yellow', 'points': 10, 'speed': 'medium'}
{'color': 'green', 'points': 5, 'speed': 'slow'}
{'color': 'green', 'points': 5, 'speed': 'slow'}
......
Total number of aliens: 30
```
# <font color=#FF0000> - 5.3.2 在字典中存储列表 </font>
有时候需要将列表储存在字典中，而不是将字典储存在列表中：
例一：

``` python
#储存所点比萨的信息
pizza = {
    'crust' : 'thick' ,
    'toppings' : ['mushrooms' , 'extra chees'] ,
    }

#概述所点的比萨
print("You ordered a " + pizza['crust'] + "-crust pizza" + "with the following toppings :" )
for topping in pizza['toppings']:
    print("\t" + topping)
```
输出结果如下：

``` python
You ordered a thick-crust pizzawith the following toppings :
	mushrooms
	extra chees
```
例二：

``` python
favorite_languages = {
    'jen' : ['python' , 'ruby'] ,
    'sarah' : ['c'] ,
    'edward' : ['go' , 'ruby'] ,
    'phil' : ['python' , 'java'] ,
    }
for name , languages in favorite_languages.items():
    print("\n" + name.title() + "'s favorite languages are:")
    for language in languages:
        print("\t" + language.title())
```
输出结果如下：

``` python

Jen's favorite languages are:
	Python
	Ruby

Sarah's favorite languages are:
	C

Edward's favorite languages are:
	Go
	Ruby

Phil's favorite languages are:
	Python
	Java
```
# <font color=#FF0000> - 5.3.3 在字典中存储字典 </font>

``` python
users = {
    'aeinstein' : {
        'first' : 'albert' ,
        'last' : 'einstein' ,
        'location' : 'princeton' ,
        } ,
    'mcurie' : {
        'first' : 'marie' ,
        'last' : 'curie' ,
        'location' : 'paris' ,
        } ,
    }
for username , user_info in users.items():
    print("\nUsername : " + username)
    full_name = user_info['first'] + " " + user_info['last']
    location = user_info['location']
    print("\tFull name : " + full_name.title())
    print("\tlocation : " + location .title())
```
输出结果如下：

``` python

Username : aeinstein
	Full name : Albert Einstein
	location : Princeton

Username : mcurie
	Full name : Marie Curie
	location : Paris
```
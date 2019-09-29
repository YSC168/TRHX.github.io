---
title: Python3 基础学习笔记 C04
tags: if语句
categories: 
  - Python3 学习笔记
  - 基础学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/python.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 基础学习笔记第四章 —— 【if语句】</font></center>

<!--more-->

----------
 # <font color=#FF0000> - 4.1 一个简单的数列 </font>
 给定一个汽车列表，将其中每一辆汽车的名称打印出来，要求打印 'bmw' 时所有字母都要大写，其余名称只需要首字母大写：

``` python
 cars = ['audi' , 'bmw' , 'subaru' , 'toyota']
 for car in cars:
 	if car == 'bmw':
		print(car.upper())
	else:
		print(car.title())
```
		
输出结果如下：

``` python
Audi
BMW
Subaru
Toyota
```
 # <font color=#FF0000> - 4.1.1 检查特定值是否包含在列表当中 </font>

>  要判断特定的值是否已包含在列表当中，可使用关键字 <font color=#FF0000>in</font>

``` python
 user_names = ['andia' , 'david' , 'liwa']
 user = 'andia'
 if user in user_names:
 	print(user.title() + "is in user_name.")
```
输出结果如下：

``` python
Andiais in user_name.
```
 

> 要判断特定的值是否不包含在列表当中，可使用关键字 <font color=#FF0000> not in</font>

 
``` python
 user_names = ['andia' , 'david' , 'liwa']
 user = 'kivle'
 if user not in user_names:
 	print(user.title() + "is not in user_name.")
```
输出结果如下：

``` python
Kivleis not in user_name.
```
 # <font color=#FF0000> - 4.2 if-else 语句 </font>

``` python
 age = input("请输入你的年龄查看是否可以去网吧：")
 if int(age) >= 18:
     print("You are old enough to go to the net bar!")
     print("You should go to net bar less,study more!")
 else:
     print("You are too young to go to the net bar!")
     print("Wait until you are 18 to go to the net bar!")
```
分别输入19和15，输出结果如下：

``` python
 请输入你的年龄查看是否可以去网吧：19
 You are old enough to go to the net bar!
 You should go to net bar less,study more!
```



``` python
 请输入你的年龄查看是否可以去网吧：15
 You are too young to go to the net bar!
 Wait until you are 18 to go to the net bar!
```

 # <font color=#FF0000> - 4.3 if-elif-else 结构 </font>

``` python
 age = 12
 if age < 4:
 	price = 0
 elif age < 18:
 	price = 5
 else:
 	price = 10
 print("Your admission cost is $" + str(price) + ".")
```
输出结果如下：

``` python
 Your admission cost is $5.
```
 # <font color=#FF0000> - 4.3.1 使用多个 elif 代码块 </font>
 ``` python
 age = 20
 if age < 4:
 	price = 0
 elif age < 18:
 	price = 5
 elif age < 65:
 	price = 15
 else:
 	price = 10
 print("Your admission cost is $" + str(price) + ".")
```
输出结果如下：

``` python
Your admission cost is $15.
```
 # <font color=#FF0000> - 4.3.2 省略 else 代码块 </font>
 Python并不要求 if-elif 结构后面必须有 else 代码块：
 ``` python
 age = 20
 if age < 4:
 	price = 0
 elif age < 18:
 	price = 5
 elif age < 65:
 	price = 15
 elif age >= 65:
 	price = 10
 print("Your admission cost is $" + str(price) + ".")
```
输出结果仍与3.3.1一样

 # <font color=#FF0000> - 4.4 测试多个条件 </font>
 if-elif-else结构功能强大，但仅适用于只有一个条件满足的情况：遇到通过了的测试后，Python就会跳过余下的测试：

``` python
 names = ['Zhangshan' , 'Wanger']
if 'Zhangshan' in names:
    print("Zhangshan is here!")
if 'Wanger' in names:
    print("Wanger is here!")
if 'Xiaoming' in names:
    print("Xiaoming is here!")
print("All the students are here!")
```
输出结果如下：

``` python
Zhangshan is here!
Wanger is here!
All the students are here!
```
相同的程序，如果使用 if-elif-else 结构，代码将不能正确运行：

``` python
names = ['Zhangshan' , 'Wanger']
if 'Zhangshan' in names:
    print("Zhangshan is here!")
elif 'Wanger' in names:
    print("Wanger is here!")
elif 'Xiaoming' in names:
    print("Xiaoming is here!")
print("All the students are here!")
```
输出结果如下：
``` python
Zhangshan is here!
All the students are here!
```
<font color=#FF000>总之，如果我们只想执行一个代码块，就使用 if-elif-else 结构；如果要运行多个代码块，就必须使用一系列独立的 if 语句！</font>

 # <font color=#FF0000> - 4.5 使用 if 语句处理列表 </font>
  # <font color=#FF0000> - 4.5.1 检查特殊元素</font>
对3.4例子改版，加入姓名 'Xiaoming'，当检索到Xiaoming时告诉他，他妈妈叫他回家吃饭
``` python
names = ['Zhangshan' , 'Wanger' , 'Xiaoming']
for name in names:
    if name == 'Xiaoming':
        print("Xiaoming,Your mother told you to go home for dinner!")
    else:
        print(name +"is here!")
print("All the students are here!")
```

输出结果如下：

``` python
Zhangshanis here!
Wangeris here!
Xiaoming,Your mother told you to go home for dinner!
All the students are here!
```
  # <font color=#FF0000> - 4.5.2 确定列表不是空的</font>
  在检索姓名前检查姓名是否为空，不为空则打印出所有姓名，为空则提示没有姓名：

``` python
names = []
if names:
    for name in names:
        print(name +" is here!")
    print("All the students are here!")
else:
    print("There is no students!")
```
输出结果如下：

``` python
There is no students!
```
<font color=#FF000>在if语句中将列表名用在条件表达式中时，Python将在列表至少包含一个元素时返回Ture，并在列表为空时返回False</font>
  
  # <font color=#FF0000> - 4.5.3 使用多个列表</font>
  两个列表names_1和names_2，要求输出既在names_2中又在names_1中的元素：

``` python
names_1 = ['Zhangshan' , 'Liyang'  , 'Wanger' , 'Tangyang' , 'Xiaoming']
names_2 = ['Liyang' , 'Zhangwei' , 'Tangyang']
for names in names_2:
    if names in names_1:
        print(names +" is here!")
print("All the students are here!")
```
输出结果如下：

``` python
Liyang is here!
Tangyang is here!
All the students are here!
```
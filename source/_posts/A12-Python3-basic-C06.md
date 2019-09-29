---
title: Python3 基础学习笔记 C06
tags:
  - input()函数
  - while循环
categories: 
  - Python3 学习笔记
  - 基础学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/python.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 基础学习笔记第六章 —— 【用户输入和 while 循环】</font></center>

<!--more-->

----------
 # <font color=#FF0000> - 6.1 函数 input() 的工作原理 </font>
 函数 input() 让程序暂停运行，等待用户输入一些文本。获取用户输入后，Python将其储存在一个变量当中，以方便你使用；函数 input() 返回为 string 类型

``` python
message = input("Please tell me your name:")
print("Hello , " + message + "!")
```
输出结果如下：

``` python
Please tell me your name:anliy
Hello , anliy!
```
进阶：

``` python
message = "Please tell me your name so that we can personalize the messages you see."
message += "\nWhat's your first name?"
name = input(message)
print("\nHello , " + name + "!")
```
输出结果如下：

``` python
Please tell me your name so that we can personalize the messages you see.
What's your first name?trhx

Hello , trhx!
```
 # <font color=#FF0000> - 6.1.1 使用 int() 来获取数值输入 </font>
 使用函数 input() 时，Python会将用户输入解读为字符串：

``` python
>>> age = input("How old are you?")
How old are you?19
>>> age
'19'
```
为了解决这个问题，可以使用函数 int() ，它让Python将输入视为数值：

``` python
>>> age = input("How old are you?")
How old are you?19
>>> age = int(age)
>>> age
19
```
实例：

``` python
age = input("Please tell me your age:")
age = int(age)
if age >= 18:
    print("You are old enough to go to the Internet bar!")
else:
    print("You are not old enough to go to Internet bar!")
```
输出结果如下：

``` python
Please tell me your age:17
You are not old enough to go to Internet bar!
```
 # <font color=#FF0000> - 6.1.2 求模运算符 </font>
 处理数值信息时，求模运算符（%）是一个很有用的工具，它将两个数相除并返回余数：

``` python
>>> 4 % 3
1
>>> 5 % 3
2
>>> 8 % 2
0
>>> 7 % 3
1
```
 # <font color=#FF0000> - 6.1.3 在 Python 2.7 中获取输入 </font>
 如果使用 Python 2.7，应该使用函数 `raw_input()` 来提示用户输入，这个函数与 Python 3 中的 `input()` 一样，也将输入解读为字符串；Python 2.7 也包含函数 `input()`，但它将用户输入解读为Python代码，并尝试运行它们
 
 # <font color=#FF0000> - 6.2 while 循环 </font>
  for 循环用于针对集合中的每一个元素的一个代码块，而 while 循环不断地运行，直到指定的条件不满足为止
  
 # <font color=#FF0000> - 6.2.1 使用 while 循环 </font>
 一个简单的 while 循环：

``` python
num = 1
while num < 5:
    print(num)
    num += 1
```
输出结果如下：

``` python
1
2
3
4
```
 # <font color=#FF0000> - 6.2.2 让用户选择退出循环 </font>

``` python
prompt = "\nTell me something, and I will repeat it back to you:"
prompt += "\nEnter 'quit' to end the program."
message = " "
while message != 'quit':
    message = input(prompt)
    print(message)
```
运行程序：

``` python

Tell me something, and I will repeat it back to you:
Enter 'quit' to end the program.Hello everyone!
Hello everyone!

Tell me something, and I will repeat it back to you:
Enter 'quit' to end the program.Hello again!
Hello again!

Tell me something, and I will repeat it back to you:
Enter 'quit' to end the program.quit
quit
```
 # <font color=#FF0000> - 6.2.3 使用标志 </font>
 在要求很多条件都满足才继续运行的程序中，可以定义一个变量，用于判断整个程序是否处于活动状态，这个变量称为<font color=#FF0000>标志</font>

``` python
prompt = "\nTell me something, and I will repeat it back to you:"
prompt += "\nEnter 'quit' to end the program."
active = True
while active:
    message = input(prompt)
    if message == 'quit':
        active = False
    else:
        print(message)
```
运行结果与6.2.2一致

 # <font color=#FF0000> - 6.2.4 使用 break 退出循环 </font>
 要立即退出 while 循环，不再运行循环中余下的代码，也不管条件测试的结果如何，可使用 break 语句，break 语句用于控制程序流程，可使用它来控制哪些代码将执行，哪些代码不执行

``` python
prompt = "\nPlease enter the name of a city you have visited:"
prompt += "\nEnter 'quit' when you are finished."
active = True
while active:
    city = input(prompt)
    if city == 'quit':
        break
    else:
        print("I'd love to go to " + city.title() + "!")
```
运行程序：

``` python

Please enter the name of a city you have visited:
Enter 'quit' when you are finished.Shanghai
I'd love to go to Shanghai!

Please enter the name of a city you have visited:
Enter 'quit' when you are finished.Beijing
I'd love to go to Beijing!

Please enter the name of a city you have visited:
Enter 'quit' when you are finished.quit
```
在任何Python循环中都可以使用break语句，例如，可以使用break语句来退出遍历列表或字典

 # <font color=#FF0000> - 6.2.5 在循环中使用 continue </font>
 要返回到循环开头，并根据条件测试结果决定是否继续执行循环，可使用 continue 语句，它不像 break 语句那样不再执行余下的代码并退出整个循环，例如，从1到10只打印其中奇数：

``` python
number =0
while number < 10:
    number += 1
    if number % 2 == 0:
        continue
    print(number)
```
输出结果如下：
```python
1
3
5
7
9
```
 # <font color=#FF0000> - 6.3 使用 while 循环来处理列表和字典 </font>
for循环是一种遍历列表的有效方式，但在for循环中不应修改列表，否则将导致Python难以跟踪其中的元素，要在遍历列表的同时对其进行修改，可使用while循环
 
 # <font color=#FF0000> - 6.3.1 在列表之间移动元素 </font>

``` python
unconfirmed_users = ['alice' , 'brian' , 'candace']
confirmed_users = []
while unconfirmed_users:
    current_user = unconfirmed_users.pop()
    print("Verifying user: " + current_user.title())
    confirmed_users.append(current_user)
print("\nThe following users have been confirmed:")
for confirmed_user in confirmed_users:
    print(confirmed_user.title())
```

 
 首先创建一个未验证用户列表，其中包含用户Alice、Brian和Candace，还创建了一个空列表，用于存储已验证的用户，程序中的 while 循环将不断地运行，直到列表 unconfirmed_users 变成空的。在这个循环中，函数pop() 以每次一个的方式从列表 unconfirmed_users 末尾删除未验证的用户。由于Candace位于列表 unconfirmed_users 的末尾，因此其名字将首先被删除、存储到变量 current_user 中并加入到列表 confirmed_users 中。接下来是Brian，然后是Alice

为模拟用户验证过程，我们打印一条验证消息并将用户加入到已验证用户列表中。未验证用户列表越来越短，而已验证用户列表越来越长。未验证用户列表为空后结束循环，再打印已验证用户列表：

``` python
Verifying user: Candace
Verifying user: Brian
Verifying user: Alice

The following users have been confirmed:
Candace
Brian
Alice
```
 # <font color=#FF0000> - 6.3.2 删除包含特定值的所有列表元素 </font>
 可以使用方法 remove() 来删除列表中特定的值，但如果要删除的值在列表中出现了多次，方法 remove() 就不管用了，如果要删除列表中所有包含特定值的元素则可以使用 while 循环：

``` python
names = ['alice' , 'candace' , 'alice' , 'brian' , 'alix' , 'candace' , 'heliy']
print(names)
while 'candace' in names:
    names.remove('candace')
print(names)
```

 输出结果如下：

``` python
['alice', 'candace', 'alice', 'brian', 'alix', 'candace', 'heliy']
['alice', 'alice', 'brian', 'alix', 'heliy']
```
使用方法 remove() 做对比：

``` python
names = ['alice' , 'candace' , 'alice' , 'brian' , 'alix' , 'candace' , 'heliy']
print(names)
names.remove('candace')
print(names)
```

输出结果如下：

``` python
['alice', 'candace', 'alice', 'brian', 'alix', 'candace', 'heliy']
['alice', 'alice', 'brian', 'alix', 'candace', 'heliy']
```
 # <font color=#FF0000> - 6.3.3 使用用户输入来填充字典 </font>

``` python
responses = {}

#设置一个标志，指出调查是否继续
polling_active = True

while polling_active:
    #提示输入被调查者的姓名和回答
    name = input("\nWhat's your name?")
    response = input("What kind of fruit do you like?")
    
    #将答卷储存在字典中
    responses[name] = response
    
    #询问是否还有其他人要参与回答
    repeat = input("Would you like to let another person respond?(Yes/No)")
    if repeat == 'No':
        polling_active = False

#调查结束，显示结果
print("\n------ Poll Results ------")
for name , response in responses.items():
    print(name + " like " + response + ".")
```
运行程序：

``` python
What's your name?TRHX
What kind of fruit do you like?apple
Would you like to let another person respond?(Yes/No)Yes

What's your name?TRHXCC
What kind of fruit do you like?banana
Would you like to let another person respond?(Yes/No)No

------ Poll Results ------
TRHX like apple.
TRHXCC like banana.
```
---
title: Python3 基础学习笔记 C09
tags:
  - 文件
  - 异常
categories: 
  - Python3 学习笔记
  - 基础学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/python.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 基础学习笔记第九章 —— 【文件和异常】</font></center>

<!--more-->

----------
# <font color=#FF0000> - 9.1 从文件中读取数据  </font>
# <font color=#FF0000> - 9.1.1 读取整个文件  </font>
有一个文件，包含精确到小数点后30位的圆周率值，且在小数点后每10位处都换行：
```txt
Circumference rate.txt
----------
3.1415926535
  8979323846
  2643383279
  ```
以下两个程序将打开并读取这个文件，再将其内容显示到屏幕上：
```python
#file_reader.py

with open('Circumference rate.txt') as file_object:
    contents = file_object.read()
    print(contents)
```
```python
#file_reader2.py

contents = open ('Circumference rate.txt')
print(contents.read())
contents.close()
```

函数<font color=#FF0000>open()</font>接受一个参数：要打开的文件的名称，Python在当前执行的文件所在的目录中查找指定的文件；关键字<font color=#FF0000>with</font>在不再需要访问文件后将其关闭；也可以调用<font color=#FF0000>open()</font>和<font color=#FF0000>close()</font>来打开和关闭文件，如果使用这种方法，当程序存在bug时，<font color=#FF0000>close()</font>语句未执行，文件将不会被关闭；方法<font color=#FF0000>read()</font>将读取这个文件的全部内容，并将其作为一个长长的字符串储存在变量<font color=#FF0000>contents</font>中，通过打印<font color=#FF0000>contents</font>的值，就可以将这个文本文件的全部内容打印出来：
```python
3.1415926535
  8979323846
  2643383279
  
```
输出结果末尾有一空行，这是因为read()到达末尾时返回一个空字符串，而将这个空字符串显示出来就是一个空行，如果要删除末尾的空行，可在print语句中使用rstrip():
```python
#file_reader.py

with open('Circumference rate.txt') as file_object:
    contents = file_object.read()
    print(contents.rstrip())
```
输出结果如下：
```python
3.1415926535
  8979323846
  2643383279
```
# <font color=#FF0000> - 9.1.2 文件路径  </font>

> <font color=#FF0000>相对文件路径：</font>假定程序文件位于python_work文件夹中，程序文件操作的文本文件位于python_work文件夹的子文件夹text_files中，此时可以使用相对文件路径来打开该文本文件，相对文件路径让Python到指定的位置去查找，而该位置是相对于当前运行的程序所在目录的

在Linux和OS X中，相对路径类似于如下：
```python
with open('text_files/filename.txt') as file_object:
```
在Windows系统中，文件路径中使用反斜杠（\）而不是斜杠（/）:
```python
with open('text_files\filename.txt') as file_object:
```


> <font color=#FF0000>绝对文件路径：</font>不用关心当前运行的程序储存在什么地方，直接将文件在计算机中的准确位置告诉Python，这称为绝对文件路径，绝对路径通常比相对路径更长，因此将其储存在一个变量中，再将变量传递给open()会有所帮助

在Linux和OS X中，绝对路径类似于如下：
```python
file_path = '/home/ehmatthes/other_files/text_files/filename.txt'
with open(file_path) as file_object:
```
在Windows系统中，绝对路径类似于如下：
```python
file_path = 'C:\Users\ehmatthes\other_files\text_files\filename.txt'
with open(file_path) as file_object:
```

# <font color=#FF0000> - 9.1.3 逐行读取  </font>
要以每次一行的方式检查文件，可对文件对象使用for循环：
```python
#file_reader.py

filename = 'Circumference rate.txt'
with open(filename) as file_object:
    for line in file_object:
        print(line)
```
在文件中每行的末尾都有一个看不见的换行符，而print语句也会加上一个换行符，因此每行末尾都有两个换行符：一个来自文件，一个来自print语句，输出结果如下：
```python
3.1415926535

  8979323846

  2643383279
```
要消除这些多余的空白行，可以使用rstrip():
```python
#file_reader.py

filename = 'Circumference rate.txt'
with open(filename) as file_object:
    for line in file_object:
        print(line.rstrip())
```
输出结果如下：
```python
3.1415926535
  8979323846
  2643383279
```
# <font color=#FF0000> - 9.1.4 创建一个包含文件各行内容的列表  </font>
使用关键字with时，open()返回的文件对象只在with代码块内可用，如果要在with代码块外访问文件的内容，可在with代码块内将文件的各行储存在一个列表当中，并在with代码块外使用该列表：
```python
#file_reader.py

filename = 'Circumference rate.txt'
with open(filename) as file_object:
    lines = file_object.readlines()
    
for line in lines:
    print(line.rstrip())
```
输出结果与文件内容完全一致

# <font color=#FF0000> - 9.1.5 使用文件的内容  </font>
创建一个字符串，它包含文件中储存的所有数字，且没有任何空格：
```python
#pi_string.py

filename = 'Circumference rate.txt'
with open(filename) as file_object:
    lines = file_object.readlines()

pi_string = ''
for line in lines:
    pi_string += line.rstrip()
    
print(pi_string)
print(len(pi_string))
```
打印该字符串以及其长度：
```python
3.1415926535  8979323846  2643383279
36
```
由于原文件每行左边都有空格，我们可以使用strip()而不是rstrip()来删除它：
```python
#pi_string.py

filename = 'Circumference rate.txt'
with open(filename) as file_object:
    lines = file_object.readlines()

pi_string = ''
for line in lines:
    pi_string += line.strip()
    
print(pi_string)
print(len(pi_string))
```
输出结果如下：
```python
3.141592653589793238462643383279
32
```

> Python中有三个去除头尾字符、空白符的函数，它们依次为: 
> <font color=#FF0000>strip：</font>用来去除头尾字符、空白符(包括\n、\r、\t、' '，即：换行、回车、制表符、空格) 
> <font color=#FF0000>lstrip：</font>用来去除开头字符、空白符(包括\n、\r、\t、' '，即：换行、回车、制表符、空格)
> <font color=#FF0000>rstrip：</font>用来去除结尾字符、空白符(包括\n、\r、\t、' '，即：换行、回车、制表符、空格)
> 注意：这些函数都只会删除头和尾的字符，中间的不会删除。
> 用法分别为： 
> string.strip([chars]) 
> string.lstrip([chars])
> string.rstrip([chars])
> 参数chars是可选的，当chars为空，默认删除string头尾的空白符(包括\n、\r、\t、' ')
> 当chars不为空时，函数会被chars解成一个个的字符，然后将这些字符去掉
> 它返回的是去除头尾字符(或空白符)的string副本，string本身不会发生改变

# <font color=#FF0000> - 9.2 写入文件 </font>

将一条简单的消息储存到文件中：
```python
#write_message.py

filename = 'programming.txt'
with open(filename,'w') as file_object:
    file_object.write("I love programming!")
```
调用open()时提供了两个实参，第一个实参也是要打开文件的名称，第二个实参（'w'）告诉Python，我们要以写入模式打开这个文件，打开文件时，可指定<font color=#FF0000>读取模式（'r'）、写入模式（'w'）、附加模式（'a'）</font>或者让我们能够<font color=#FF0000>读取和写入文件的模式（'r+'）</font>，如果省略模式实参，则默认以只读模式打开文件

<center>附表：Python读写文件各种模式区别</center>

| 模式 | 可做操作                           | 若文件不存在 | 是否覆盖   |
| :----: | :----------------------------------: | :------------: | :----------: |
| r    | 打开一个文件用于只读               | 报错         | -          |
| rb   | 以二进制格式打开一个文件用于只读   | 报错         | -          |
| r+   | 打开一个文件用于读和写             | 报错         | 是         |
| rb+  | 以二进制格式打开一个文件用于读和写 | 报错         | 是         |
| w    | 打开一个文件用于只写               | 创建         | 是         |
| wb   | 以二进制格式打开一个文件只用于只写 | 创建         | 是         |
| w+   | 打开一个文件用于读和写             | 创建         | 是         |
| wb+  | 以二进制格式打开一个文件用于读和写 | 创建         | 是         |
| a    | 打开一个文件用于追加               | 创建         | 否，追加写 |
| ab   | 以二进制格式打开一个文件用于追加   | 创建         | 否，追加写 |
| a+    | 打开一个文件用于读和写               | 创建         | 否，追加写 |
| ab+   | 以二进制格式打开一个文件用于追加   | 创建         | 否，追加写 |

# <font color=#FF0000> - 9.3 使用 try-except 代码块处理异常 </font>
当我们尝试将一个数字除以0时，会发生ZeroDivisionError异常：
```python
>>> print(5/0)
Traceback (most recent call last):
  File "<pyshell#0>", line 1, in <module>
    print(5/0)
ZeroDivisionError: division by zero
```
此时我们可以编写一个<font color=#FF0000>try-except代码块</font>来处理该异常：
```python
try:
    print(5/0)
except ZeroDivisionError:
    print("You can't divide by zero!")
```
当我们运行该程序时，会出现提示：
```python
You can't divide by zero!
```
在<font color=#FF0000>try-except代码块</font>中加入else，编写一个只执行除法运算的简单计算器：
```python
print("Give me two numbers,and I'll divide them.")
print("Enter 'q' to quit.")

while True:
    first_number = input("\nFirst number:")
    if first_number == 'q':
        break
    second_number = input("\nSecond number:")
    if second_number == 'q':
        break
    try:
        answer = int(first_number)/int(second_number)
    except ZeroDivisionError:
        print("You can't divide by 0!")
    else:
        print(answer)
```
运行程序：
```python
Give me two numbers,and I'll divide them.
Enter 'q' to quit.

First number:45

Second number:0
You can't divide by 0!

First number:36

Second number:8
4.5

First number:q
```
若不加入<font color=#FF0000>try-except代码块</font>，我们在输入0时，程序就会出现异常而崩溃，而<font color=#FF0000>try-except代码块</font>很好的解决了这种问题，而且还起到了提示的作用，同样的，<font color=#FF0000>try-except代码块</font>也可以处理其他异常，如<font color=#FF0000>FileNotFoundError</font>等

# <font color=#FF0000> - 9.4 储存数据 </font>
# <font color=#FF0000> - 9.4.1 使用 json.dump() 和 json.load() </font>
<font color=#FF0000> 模块json</font>能够将简单的Python数据结构转储到文件中，并在程序再次运行时加载该文件中的数据；编写一个储存一组数字的简短程序，再编写一个将这些数字读取到内存中的程序，第一个程序将使用<font color=#FF0000> json.dump()</font>来储存这组数据，而第二个程序将使用<font color=#FF0000> json.load()</font>。函数<font color=#FF0000> json.dump()</font>接受两个实参：要储存的数据以及可用于储存数据的文件对象：
```python
#number_writer.py

import json

numbers = [2,3,5,7,11,13]

filename = 'numbers.json'
with open(filename,'w') as f_obj:
    json.dump(numbers,f_obj)
```
先导入模块json，再创建一个数字列表，<font color=#FF0000> 通常用文件扩展名.json来指出文件储存的数据为JSON格式</font>，然后以写入模式打开该文件，使用函数<font color=#FF0000>json.dump()</font>将数字列表储存到文件numbers.json中，打开该文件，数据的储存格式与Python一样：
```txt
[2, 3, 5, 7, 11, 13]
```
再编写一个程序，使用<font color=#FF0000>json.load()</font>将这个列表读取到内存中：
```python
#number_reader.py

import json

filename = 'numbers.json'
with open(filename) as f_obj:
    numbers = json.load(f_obj)
print(numbers)
```
输出结果与number_writer.py中创建的数字列表相同：
```txt
[2, 3, 5, 7, 11, 13]
```
进阶：在同一个程序中使用 <font color=#FF0000>json.dump()</font> 和 <font color=#FF0000>json.load()</font>：创建文件username.json储存用户名，从该文件中获取用户名，如果这个文件不存在，就在except代码块中提示用户输入用户名，并将其储存在username.json中：
```python
#remember_me.py

import json

#如果以前储存了用户名，就加载它
#否则就提示用户输入用户名并储存它
filename = 'numbers.json'
try:
    with open(filename) as f_obj:
        username = json.load(f_obj)
except FileNotFoundError:
    username = input("What's your name?")
    with open(filename,'w') as f_obj:
        json.dump(username,f_obj)
        print("We'll remember you when you come back, " + username + "!")
else:        
    print("Welcome back, " + username + "!")
```
以前没有储存用户名，第一次运行程序：
```python
What's your name?TRHX
We'll remember you when you come back, TRHX!
```
再次运行程序：
```python
Welcome back, TRHX!
```

# <font color=#FF0000> - 9.4.2 重构 </font>
代码能够正确运行，但可以做进一步的改进——将代码划分为一系列完成具体工作的函数，这样的过程称为<font color=#FF0000>重构</font>，重构让代码更清晰、更易于理解、更容易扩展
重构remember_me.py，将大部分逻辑放到一个或者多个函数中：
```python
#remember_me.py

import json

def greet_user():
    #问候用户，并指出其名字
    filename = 'numbers.json'
    try:
        with open(filename) as f_obj:
            username = json.load(f_obj)
    except FileNotFoundError:
        username = input("What's your name?")
        with open(filename,'w') as f_obj:
            json.dump(username,f_obj)
            print("We'll remember you when you come back, " + username + "!")
    else:        
        print("Welcome back, " + username + "!")

greet_user()
```
重构greet_user()，让它不执行这么多任务——将获取储存的用户名的代码移到另一个函数中：
```python
#remember_me.py

import json

def get_stored_username():
    #如果储存了用户名，就获取它
    filename = 'numbers.json'
    try:
        with open(filename) as f_obj:
            username = json.load(f_obj)
    except FileNotFoundError:
        return None
    else:
        return username

def greet_user():
    #问候用户，并指出其名字
    username = get_stored_username()
    if username:
        print("Welcome back, " + username + "!")
    else:
        username = input("What's your name?")
        filename = 'username.json'
        with open(filename,'w') as f_obj:
            json.dump(username,f_obj)
            print("We'll remember you when you come back, " + username + "!")     

greet_user()
```
将greet_user()中的另一个代码块提取出来：将没有储存用户名时提示用户输入的代码放在一个独立的函数中：
```python
#remember_me.py

import json

def get_stored_username():
    #如果储存了用户名，就获取它
    filename = 'numbers.json'
    try:
        with open(filename) as f_obj:
            username = json.load(f_obj)
    except FileNotFoundError:
        return None
    else:
        return username

def get_new_username():
    #提示输入用户名
    username = input("What's your name?")
    filename = 'username.json'
    with open(filename,'w') as f_obj:
        json.dump(username,f_obj)
    return username
    

def greet_user():
    #问候用户，并指出其名字
    username = get_stored_username()
    if username:
        print("Welcome back, " + username + "!")
    else:
        username = get_new_username()
        print("We'll remember you when you come back, " + username + "!")     

greet_user()
```
最终版本实现了每个函数只负责单一而清晰的任务，我们在编写程序时也要像这样，要写出清晰而易于维护和扩展的代码
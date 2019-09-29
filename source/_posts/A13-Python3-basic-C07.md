---
title: Python3 基础学习笔记 C07
tags:
  - 函数
  - 模块
categories: 
  - Python3 学习笔记
  - 基础学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/python.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 基础学习笔记第七章 —— 【函数】</font></center>

<!--more-->

----------
 # <font color=#FF0000> - 7.1 定义函数 </font>
 一个简单的函数，命名为 example()，其中，关键字<font color=#FF0000> def </font>来告诉Python我们要定义一个函数，这就是<font color=#FF0000>函数定义</font>

``` python
def example():
    print("Hello world!")
example()
```
输出结果如下：

``` python
Hello world!
```
 # <font color=#FF0000> - 7.1.1 向函数传递信息 </font>
 在函数定义 def example() 的括号中添加 username，可以让函数接受我们给 username 指定的任何值，在调用函数时给 username 指定一个值，调用 example() 时，可将一个名字传递给它：

``` python
def example(username):
    print("Hello , " + username + '!')
example('TRHX')
```
输出结果如下：

``` python
Hello , TRHX!
```
 # <font color=#FF0000> - 7.1.2 实参和形参 </font>
 在 7.1.1 的例子中，函数 example() 的定义中，变量 username 是一个<font color=#FF0000>形参</font>——函数完成其工作所需的一项信息，在代码 example('TRHX') 中，值'TRHX'是一个<font color=#FF0000>实参</font>，<font color=#FF0000>实参</font>是调用函数时传递给函数的信息，调用函数时，将要让函数使用的信息放在括号内。在 example('TRHX') 中，将<font color=#FF0000>实参</font> 'TRHX' 传递给了函数 example，这个值被储存在<font color=#FF0000>形参</font> username 中
 
  # <font color=#FF0000> - 7.2 传递实参 </font>
  鉴于函数定义中可能包含多个形参，因此函数调用中也可能包含多个实参。向函数传递实参的方式很多，可使用<font color=#FF0000>位置实参</font>，这要求实参的顺序与形参的顺序相同；也可以使用<font color=#FF0000>关键字实参</font>，其中每个实参都由变量和值组成；还可以使用<font color=#FF0000>列表和字典</font>

  # <font color=#FF0000> - 7.2.1 位置实参 </font>
  调用函数时，Python必须将函数调用中的每个实参都关联到函数定义中的一个形参。为此，最简单的方法是基于实参的顺序，这种关联方式被称为位置实参

``` python
def describe_pet(animal_type , pet_name):
    print("I have a " + animal_type + ".")
    print("My " + animal_type + "'s name is " + pet_name.title() + ".")
describe_pet('hamster' , 'harry')
```
输出结果如下：

``` python
I have a hamster.
My hamster's name is Harry.
```

调用函数多次：我们可以根据需要调用函数任意次，要再描述一个宠物，只需要再次调用 

``` python
describe_pet() 即可
def describe_pet(animal_type , pet_name):
    print("I have a " + animal_type + ".")
    print("My " + animal_type + "'s name is " + pet_name.title() + ".")
describe_pet('hamster' , 'harry')
describe_pet('dog' , 'willi')
```
输出结果如下：

``` python
I have a hamster.
Myhamster's name is Harry.
I have a dog.
My dog's name is Willi.
```
# <font color=#FF0000> - 7.2.2 关键字实参 </font>
关键字实参是传递给函数的<font color=#FF0000>名称-值对</font>。直接在实参中将名称和值关联起来，不用考虑函数调用中的实参顺序

``` python
def describe_pet(animal_type , pet_name):
    print("I have a " + animal_type + ".")
    print("My " + animal_type + "'s name is " + pet_name.title() + ".")
describe_pet(animal_type = 'hamster' , pet_name = 'harry')
describe_pet(pet_name = 'willi' , animal_type = 'dog' )
```
输出结果如下：

``` python
I have a hamster.
Myhamster's name is Harry.
I have a dog.
My dog's name is Willi.
```

# <font color=#FF0000> - 7.2.3 默认值 </font>
编写函数时，可给每个形参指定默认值，在调用函数中给形参提供了实参时，Python将使用指定的实参值，否则将使用形参的默认值

``` python
def describe_pet(pet_name , animal_type = 'dog'):
    print("I have a " + animal_type + ".")
    print("My " + animal_type + "'s name is " + pet_name.title() + ".")
describe_pet(pet_name = 'willi')
```
输出结果如下：

``` python
I have a dog.
My dog's name is Willi.
```
在这个函数定义中，修改了形参的排列顺序，由于给 animal_type 指定了默认值，无需通过实参来指定动物类型，因此在函数调用中只包含一个实参——宠物的名字，然而Python依然将这个实参视为位置实参，因此如果函数调用中只包含宠物的名字，这个实参将关联到函数定义中的第一个形参，这就是需要将 pet_name 放在形参列表开头的原因所在
<font color=#FF0000>注意：使用默认值时，在形参列表中必须先列出没有默认值的形参，再列出有默认值的形参，这让Python依然能够准确地解读位置实参</font>

# <font color=#FF0000> - 7.3 返回值 </font>
函数并非总是直接显示输出，相反，它可以处理一些数据，并返回一个或一组值，函数返回的值被称为<font color=#FF0000>返回值</font>，在函数中，<font color=#FF0000>可使用 return 语句将值返回到函数调用的代码行</font>

# <font color=#FF0000> - 7.3.1 返回简单值 </font>

``` python 
def name(first_name , last_name):
    full_name = first_name + ' ' + last_name
    return full_name.title()
student = name('jimi' , 'hendrix')
print(student)
```
输出结果如下：

``` python
Jimi Hendrix
```
# <font color=#FF0000> - 7.3.2 让实参变成可选的 </font>
对 7.3.1 的例子进行改进，扩展函数 name，使其还能够处理中间名：

``` python
def name(first_name , middle_name , last_name):
    full_name = first_name + ' ' + middle_name + ' ' + last_name
    return full_name.title()
student = name('jimi' , 'lee' , 'hendrix')
print(student)
```
输出结果如下：

``` python
Jimi Lee Hendrix
```
然而，如果一个人没有中间名，那么在调用这个函数时就会出错，为了让中间名变成可选的，可以给实参 middle_name 指定一个默认值——空字符串，并在用户没有提供中间名时不使用这个实参，注意需要将 middle_name 移到形参列表的末尾：

``` python
def name(first_name , last_name , middle_name = ' '):
    if middle_name:
        full_name = first_name + ' ' + middle_name + ' ' + last_name
    else:
        full_name = first_name + ' ' + last_name
    return full_name.title()
student = name('jimi' , 'hendrix')
print(student)
student = name('jimi' , 'hendrix' , 'lee' )
print(student)
```
输出结果如下：

``` python
Jimi   Hendrix
Jimi Lee Hendrix
```
# <font color=#FF0000> - 7.3.3 返回字典 </font>
函数可返回任何类型的值，包括列表和字典等较复杂的数据结构：

``` python
def name(first_name , last_name):
    full_name = {'first' : first_name , 'last' : last_name}
    return full_name
student = name('jimi' , 'hendrix')
print(student)
```
输出结果如下：

``` python
{'first': 'jimi', 'last': 'hendrix'}
```
# <font color=#FF0000> - 7.3.4 结合使用函数和 while 循环 </font>

``` python
def name(first_name , last_name):
    full_name = first_name + ' ' + last_name
    return full_name
while True:
    print("\nPlease input your name:")
    print("(Enter 'exit' to quit)")
    f_name = input("First_name:")
    if f_name == 'exit':
        break
    l_name = input("Last_name:")
    if l_name == 'exit':
        break
    student = name(f_name , l_name)
    print(student)
    print("Hello, " + student.title() + "!")
```
运行程序：

``` python

Please input your name:
(Enter 'exit' to quit)
First_name:jimi
Last_name:hendrix
jimi hendrix
Hello, Jimi Hendrix!

Please input your name:
(Enter 'exit' to quit)
First_name:exit
```
# <font color=#FF0000> - 7.4 传递列表 </font>

``` python
def users(names):
    for name in names:
        message = "Hello, " + name.title() + "!"
        print(message)
usernames = ['hannah' , 'tony' , 'margot']
users(usernames)
```

输出结果如下：

``` python
Hello, Hannah!
Hello, Tony!
Hello, Margot!
```
# <font color=#FF0000> - 7.4.1 在函数中修改列表 </font>
将列表传递给函数后，函数就可以对其进行修改，在函数中对这个列表所做的任何修改都是永久性的
#首先创造一个列表，其中包含一些要打印的设计

``` python
unprinted_designs = ['iphone case' , 'robot pendannt' , 'dodecahedron']
completed_models = []

#模拟打印每个设计，直到没有未打印的设计为止
#打印每个设计后，都将其移到列表completed_models中
while unprinted_designs:
    current_design = unprinted_designs.pop()
    
    #模拟根据设计制作3D打印模型的过程
    print("Printing model: " + current_design)
    completed_models.append(current_design)
    
#显示打印好的所有模型
print("\nThe following models have been printed: ")
for completed_model in completed_models:
    print(completed_model)
```
输出结果如下：

``` python
Printing model: dodecahedron
Printing model: robot pendannt
Printing model: iphone case

The following models have been printed: 
dodecahedron
robot pendannt
iphone case
```
编写两个函数重新组织这些代码，每一个函数都做一件具体的工作，输出结果与原程序相同：

``` python
def print_models(unprinted_designs , completed_models):
#模拟打印每个设计，直到没有未打印的设计为止
#打印每个设计后，都将其移到列表completed_models中
    while unprinted_designs:
        current_design = unprinted_designs.pop()
    
        #模拟根据设计制作3D打印模型的过程
        print("Printing model: " + current_design)
        completed_models.append(current_design)

def show_completed_models(completed_models):    
#显示打印好的所有模型
    print("\nThe following models have been printed: ")
    for completed_model in completed_models:
        print(completed_model)

unprinted_designs = ['iphone case' , 'robot pendannt' , 'dodecahedron']
completed_models = []

print_models(unprinted_designs , completed_models)
show_completed_models(completed_models)
```
# <font color=#FF0000> - 7.4.2 禁止函数修改列表 </font>
有时候需要禁止函数修改列表，拿 7.4.1 的例子来说，我们打印了所有设计后，也要保留原来的未打印的设计列表，以供备案，但由于我们将所有的设计都移出了 unprinted_designs，这个列表变成了空的，原来的列表没有了，为了解决这个问题，可向函数传递列表的副本而不是原件；这样函数所做的任何修改都只影响副本，而丝毫不影响原件，要将列表的副本传递给函数，可以像下面这样做：

``` python
function_name(list_name[:])
```
切片表示法 [:] 创建列表的副本，在 7.4.1 的例子中如果不想清空未打印的设计列表，可像下面这样调用 print_models()：

``` python
print_models(unprinted_designs[:] , completed_models)
```
# <font color=#FF0000> - 7.5 传递任意数量的实参 </font>
Python允许函数从调用语句中收集任意数量的实参

``` python
def make_pizza(*toppings):
    print(toppings)
make_pizza('pepperoni')
make_pizza('mushrooms' , 'green peppers' , 'extra cheese')
```
形参名 `*toppings` 中的星号让Python创建一个名为 toppings 的空元组，并将收到的所有值都封装到这个元组中，函数体内的print语句通过生成输出来证明Python能够处理使用一个值调用函数的情形，也能处理使用三个值来调用函数的情形，输出结果如下：

``` python
('pepperoni',)
('mushrooms', 'green peppers', 'extra cheese')
```
使用循环语句：

``` python
def make_pizza(*toppings):
    print("\nMaking a pizza with the followiing toppings: ")
    for topping in toppings:
        print("- " + topping)
make_pizza('pepperoni')
make_pizza('mushrooms' , 'green peppers' , 'extra cheese')
```
输出结果如下：

``` python

Making a pizza with the followiing toppings: 
- pepperoni

Making a pizza with the followiing toppings: 
- mushrooms
- green peppers
- extra cheese
```
# <font color=#FF0000> - 7.5.1 结合使用位置实参和任意数量实参 </font>
如果要让函数接受不同类型的实参，必须在函数定义中将接纳任意数量实参的形参放在最后。Python先匹配位置实参和关键字实参，再将余下的实参都收集到最后一个形参中：

``` python
def make_pizza(size , *toppings):
    print("\nMaking a " + str(size) + "-inch pizza with the followiing toppings: ")
    for topping in toppings:
        print("- " + topping)
make_pizza(16 , 'pepperoni')
make_pizza(18 , 'mushrooms' , 'green peppers' , 'extra cheese')
```
输出结果如下：

``` python

Making a 16-inch pizza with the followiing toppings: 
- pepperoni

Making a 18-inch pizza with the followiing toppings: 
- mushrooms
- green peppers
- extra cheese
```
# <font color=#FF0000> - 7.5.2 使用任意数量的关键字实参 </font>
有时候，需要接受任何数量的实参，但预先我们不知道传递给函数的会是什么样的信息，在这种情况下，可以将函数编写成能够接受任意数量的键-值对——调用语句提供了多少就接受多少：

``` python
def build_profile(first , last , **user_info):
    #创建一个字典，其中包括我们知道的有关用户的一切
    profile = {}
    profile['first_name'] = first
    profile['last_name'] = last
    for key , value in user_info.items():
        profile[key] = value
    return profile
user_profile = build_profile('albert' , 'einstein' , location = 'princeton' , field = 'physics')
print(user_profile)
```
形参 `**user_info` 中的两个星号让Python创建一个名为 user_info 的空字典，并将收到的所有名称-值对都封装到这个字典中，在这个函数中，可以像访问其他字典那样访问 user_info 中的名字-值对，程序运行结果如下：

``` python
{'first_name': 'albert', 'last_name': 'einstein', 'location': 'princeton', 'field': 'physics'}
```
# <font color=#FF0000> - 7.6 将函数储存在模块中 </font>
更进一步，我们可以把函数储存在被称为<font color=#FF0000>模块</font>的独立文件中，再将模块导入到主程序中，import 语句运行在当前运行的程序文件中使用模块中的代码

# <font color=#FF0000> - 7.6.1 导入整个模块 </font>
要让函数是可导入的，得先创建模块，<font color=#FF0000>模块是扩展名为.py的文件</font>，包含要导入到程序中的代码，下面将创建一个包含函数 make_pizza() 的模块

``` python
#pizza.py

def make_pizza(size , *toppings):
    #概述要制作的比萨
    print("\nMaking a " + str(size) + "-inch pizza with the followiing toppings: ")
    for topping in toppings:
        print("- " + topping)
```
接下来，我们在 pizza.py 所在的目录中创建另一个名为 making_pizzas.py 的文件，在这个文件中导入刚刚创建的模块，在调用 make_pizza() 两次：

``` python
#making_pizzas.py

import pizza
pizza.make_pizza(16 , 'pepperoni')
pizza.make_pizza(18 , 'mushrooms' , 'green peppers' , 'extra cheese')
```
Python在读取这个文件时，代码行 import pizza 让Python打开文件 pizza.py，并在幕后将其中所有函数都复制到这个程序中，在 making_pizzas.py 中，可以使用 pizza.py 中定义的所有函数，要调用被导入的模块中的函数，可指定导入的模块的名称 pizza 和函数名 make_pizza()，并使用句点分隔它们，最终运行结果与原程序相同：

``` python

Making a 16-inch pizza with the followiing toppings: 
- pepperoni

Making a 18-inch pizza with the followiing toppings: 
- mushrooms
- green peppers
- extra cheese
```

# <font color=#FF0000> - 7.6.2 导入特定的函数 </font>
导入模块中特定的函数，可以使用以下语法：

``` python
from module_name import function_name
```
通过用逗号分隔函数名，可根据需要从模块中导入任意数量的函数：
```python
from module_name import function_0 , function_1 , function_2
```
以前面的 making_pizzas.py 为例，如果只想导入要使用的函数，代码类似于下面这样：
```python
from pizza import make_pizza

make_pizza(16 , 'pepperoni')
make_pizza(18 , 'mushrooms' , 'green peppers' , 'extra cheese')
```
# <font color=#FF0000> - 7.6.3 使用 as 给函数指定别名 </font>
如果要导入的函数名称可能与程序中现有的名称冲突，或者函数的名称太长，可指定简短而独一无二的别名，要给函数指定别名，需要在导入它的时候这样做，通用语法为：
```python
from module_name import function_name as fn
```
同样以前面的 making_pizzas.py 为例：
```python
from pizza import make_pizza as mp

mp(16 , 'pepperoni')
mp(18 , 'mushrooms' , 'green peppers' , 'extra cheese')
```

# <font color=#FF0000> - 7.6.4 使用 as 给模块指定别名 </font>
我们还可以给模块指定别名，通用语法为：
```python
import module_name as mn
```
同样以前面的 making_pizzas.py 为例：
```python
import pizza as p

p.make_pizza(16 , 'pepperoni')
p.make_pizza(18 , 'mushrooms' , 'green peppers' , 'extra cheese')
```
# <font color=#FF0000> - 7.6.5 导入模块中的所有函数 </font>
导入模块中所有函数的通用语法为：
```python
from module_name import *
```
同样以前面的 making_pizzas.py 为例：
```python
from pizza import *

make_pizza(16 , 'pepperoni')
make_pizza(18 , 'mushrooms' , 'green peppers' , 'extra cheese')
```
import 语句中的星号让Python将模块 pizza 中的每个函数都复制到这个程序中，由于导入了每个函数，可通过名称来调用每个函数，而不需要用句点表示法，然而，如果模块中有函数的名称与项目中的名称相同，就有可能导致意想不到的结果，<font color=#FF0000>最佳的做法是，要么只导入我们需要使用的函数，要么导入整个模块并使用句点表示法</font>
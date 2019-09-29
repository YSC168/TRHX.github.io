---
title: Python3 基础学习笔记 C08
tags:
  - 类
  - 继承
categories: 
  - Python3 学习笔记
  - 基础学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/python.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 基础学习笔记第八章 —— 【类】</font></center>

<!--more-->

----------

# <font color=#FF0000> - 8.1 创建类和使用类  </font>

创建一个表示小狗的简单类Dog，根据Dog类创建的每个实例都将储存名字和年龄，赋予每条小狗蹲下（sit()）和打滚（roll_over()）的能力：

``` python
class Dog():

    def __init__(self,name,age):
        #初始化属性name和age
        self.name = name
        self.age = age

    def sit(self):
        #模拟小狗被命令时蹲下
        print(self.name.title() + " is now sitting.")

    def roll_over(self):
        #模拟小狗被命令时打滚
        print(self.name.title() + " rolled over!")
```
方法__init__()：类中的函数称为<font color=#FF0000>方法</font>，本例中<font color=#FF0000>方法__init__()</font>是一个特殊的方法，每当我们根据Dog类创建新实例时，Python都会自动运行它，在方法的名称中，开头和结尾各有两个下划线，这是一种约定，避免Python默认方法与普通方法发生名称冲突，例子中将<font color=#FF0000>方法__init__()</font>定义成了包含三个形参：self、name和age，在这个方法的定义中，形参self必不可少，还必须位于其他形参的前面，Python调用<font color=#FF0000>方法__init__()</font>来创建Dog实例时，将自动传入实参self，每个与类相关联的方法调用都自动传递实参self，它是一个指向实例本身的引用，让实例能够访问类中的属性和方法，我们创建Dog实例时，Python将调用Dog类的<font color=#FF0000>方法__init__()</font>，我们将通过实参向Dog()传递名字和年龄；self会自动传递，因此我们不需要传递它，每当我们根据Dog类创建实例时，都只需要给最后两个形参（name和age）提供值；定义的两个变量都有前缀self，以self为前缀的变量都可以供类中的所有方法使用，还可以通过类的任何实例来访问这些变量。self.name = name 获取储存在形参name中的值，并将其储存到变量name中，然后该变量被关联到当前创建的实例。self.age = age 的作用与此类似，像这样可通过实例访问的变量称为<font color = #FF0000>属性</font>；Dog还定义了另外两种方法：sit() 和 roll_over() ，由于这些方法不需要额外的信息，如名字和年龄，因此它们只有一个形参self

在Python 2.7中创建类时，需要在括号内包含单词object：
``` python
class ClassName(object):
   ---snip---
```
 
# <font color=#FF0000>- 8.2 根据类创建实例</font>

访问属性：创建一个表示特定小狗的实例：

``` python
class Dog():

    def __init__(self,name,age):
        #初始化属性name和age
        self.name = name
        self.age = age

    def sit(self):
        #模拟小狗被命令时蹲下
        print(self.name.title() + " is now sitting.")

    def roll_over(self):
        #模拟小狗被命令时打滚
        print(self.name.title() + " rolled over!")

my_dog = Dog('willie',6)
print("My dog's name is " + my_dog.name.title() + ".")
print("My dog is " + str(my_dog.age) + " years old.")
```
让Python创建一条名字为'willie'，年龄为6的小狗，Python使用实参'willie'和6调用Dog类中的方法__init__()。方法__init__()创建一个表示特定小狗的示例，并使用我们提供的值来设置属性name和age；在访问实例的属性时，可使用句点表示法，比如该例子中的 my_dog.name；最终程序输出结果如下：

``` python
My dog's name is Willie.
My dog is 6 years old.
```
调用方法：根据Dog类创建实例后，就可以使用句点表示法来调用Dog类中定义的任何方法：
``` python
class Dog():

    def __init__(self,name,age):
        #初始化属性name和age
        self.name = name
        self.age = age

    def sit(self):
        #模拟小狗被命令时蹲下
        print(self.name.title() + " is now sitting.")

    def roll_over(self):
        #模拟小狗被命令时打滚
        print(self.name.title() + " rolled over!")

my_dog = Dog('willie',6)
my_dog.sit()
my_dog.roll_over()
```
输出结果如下：
``` python
Willie is now sitting.
Willie rolled over!
```
创建多个实例：可按需求根据类创建任意数量的实例：
``` python
class Dog():

    def __init__(self,name,age):
        #初始化属性name和age
        self.name = name
        self.age = age

    def sit(self):
        #模拟小狗被命令时蹲下
        print(self.name.title() + " is now sitting.")

    def roll_over(self):
        #模拟小狗被命令时打滚
        print(self.name.title() + " rolled over!")

my_dog = Dog('willie',6)
your_dog = Dog('lucy',8)

print("My dog's name is " + my_dog.name.title() + ".")
print("My dog is " + str(my_dog.age) + " years old.")
my_dog.sit()

print("\nYour dog's name is " + your_dog.name.title() + ".")
print("Your dog is " + str(your_dog.age) + " years old.")
your_dog.roll_over()
```
输出结果如下：
``` python 
My dog's name is Willie.
My dog is 6 years old.
Willie is now sitting.

Your dog's name is Lucy.
Your dog is 8 years old.
Lucy rolled over!
```

# <font color=#FF0000> - 8.3 使用类和实例 </font>

创建一个表示汽车的类，其中储存了有关汽车的信息，还有一个汇总这些信息的方法：
```python
class Car():

    def __init__(self,make,model,year):
        self.make = make
        self.model = model
        self.year = year
		
    def get_descriptive_name(self):
        long_name = str(self.year) + ' ' + self.make + ' ' +self.model
        return long_name.title()
		
my_new_car = Car('audi','a9','2018')
print(my_new_car.get_descriptive_name())
```
输出结果如下：
``` python
2018 Audi A9
```

# <font color=#FF0000> - 8.3.1 给属性指定默认值  </font>

类中的每个属性都必须有初始值，如果我们设置了默认值，就无需包含为它提供初始值的形参，下面为8.3的例子添加一个 odometer_reading 的属性，其初值是0，添加一个 odometer_reading() 方法，用于读取汽车的里程表：
 ```python
 class Car():
 
    def __init__(self,make,model,year):
        self.make = make
        self.model = model
        self.year = year
        self.odometer_reading = 0
		
    def get_descriptive_name(self):
        long_name = str(self.year) + ' ' + self.make + ' ' +self.model
        return long_name.title()
		
    def read_odomter(self):
        print("This car has " + str(self.odometer_reading) + " miles on it.")

my_new_car = Car('audi','a9','2018')
print(my_new_car.get_descriptive_name())
my_new_car.read_odomter()
```
输出结果如下：
``` python
2018 Audi A9
This car has 0 miles on it.
```

# <font color=#FF0000> - 8.3.2 修改属性的值  </font>

 可以以三种不同的方式修改属性的值：直接通过实例进行修改；通过方法进行设置；通过方法进行递增（增加特定的值）
 
直接修改属性的值：
要修改属性的值，最简单的方法就是通过实例直接访问它，将8.3.1中的例子第7行代码 `self.odometer_reading = 0` 改为 `self.odometer_reading = 66`，输出结果如下：
``` python
 2018 Audi A9
This car has 66 miles on it.
```
通过方法修改属性的值：
``` python
class Car():
    
    def __init__(self,make,model,year):
        self.make = make
        self.model = model
        self.year = year
        self.odometer_reading = 0
        
    def get_descriptive_name(self):
        long_name = str(self.year) + ' ' + self.make + ' ' +self.model
        return long_name.title()
    
    def read_odomter(self):
        print("This car has " + str(self.odometer_reading) + " miles on it.")

    def update_odometer(self,mileage):
        self.odometer_reading = mileage
        
my_new_car = Car('audi','a9','2018')
print(my_new_car.get_descriptive_name())

my_new_car.update_odometer(66)
my_new_car.read_odomter()
```
对Car类所做的唯一修改就是在第17、18行添加了方法 update_odometer()，这个方法接受一个里程值，并将其储存到 self.odometer_reading 中，在倒数第二行，调用了 update_odometer()，并向它提供了一个实参（该实参对应于方法定义中的形参mileage），它将里程数设置为66，而方法 read_odomter() 打印该读数：
```python
2018 Audi A9
This car has 66 miles on it.
```
可对方法 update_odometer() 进行扩展，使其能够在修改里程表读数时做一些额外的工作，添加一些逻辑，禁止任何人将里程表读数往回调：
``` python
class Car():

    def __init__(self,make,model,year):
        self.make = make
        self.model = model
        self.year = year
        self.odometer_reading = 50
        
    def get_descriptive_name(self):
        long_name = str(self.year) + ' ' + self.make + ' ' +self.model
        return long_name.title()
    
    def read_odomter(self):
        print("This car has " + str(self.odometer_reading) + " miles on it.")

    def update_odometer(self,mileage):
        if mileage >= self.odometer_reading:
            self.odometer_reading = mileage
        else:
            print("You can't roll back an odometer!")
        
my_new_car = Car('audi','a9','2018')
print(my_new_car.get_descriptive_name())

my_new_car.update_odometer(33)
my_new_car.read_odomter()
```
修改 self.odometer_reading 的默认值为50，当我们再次尝试修改其值为33时，由于小于原来的里程，因此无法修改：

``` python
2018 Audi A9
You can't roll back an odometer!
This car has 50 miles on it.
```
通过方法对属性的值进行递增：
有时候需要将属性值递增到特定的量，而不是将其设置为全新的值，假设我们购买了一辆二手车，从购买到登记期间增加了100英里的里程，下面的方法让我们能够传递这个增量，并相应地增加里程表读数：
```python
class Car():
    
    def __init__(self,make,model,year):
        self.make = make
        self.model = model
        self.year = year
        self.odometer_reading = 0
        
    def get_descriptive_name(self):
        long_name = str(self.year) + ' ' + self.make + ' ' +self.model
        return long_name.title()
    
    def read_odomter(self):
        print("This car has " + str(self.odometer_reading) + " miles on it.")

    def update_odometer(self,mileage):
        if mileage >= self.odometer_reading:
            self.odometer_reading = mileage
        else:
            print("You can't roll back an odometer!")

    def increment_odometer(self,miles):
        #将里程表读数增加指定的量
        self.odometer_reading += miles
        
my_new_car = Car('audi','a9','2018')
print(my_new_car.get_descriptive_name())

my_new_car.update_odometer(6600)
my_new_car.read_odomter()

my_new_car.increment_odometer(100)
my_new_car.read_odomter()
```
输出结果如下：
``` python
2018 Audi A9
This car has 6600 miles on it.
This car has 6700 miles on it.
```

# <font color=#FF0000> - 8.4 继承 </font>

编写类时，并非总是要从空白开始，如果要编写的类是另一个现成类的特殊版本，可使用<font color=#FF0000>继承</font>，一个类<font color=#FF0000>继承</font>另一个类时，它自动获得另一个类的所有属性和方法；原有的类称为<font color=#FF0000>父类</font>，而新类称为<font color=#FF0000>子类</font>，子类<font color=#FF0000>继承</font>了其父类的所有属性和方法，同时还可以定义自己的属性和方法；继承的通用语法大致如下：

```python
class ClassName1(object):
    def __init__(self,name1,name2,name3):
        --snip--

class ClassName2(ClassName1):
    def __init__(self,name1,name2,name3):
        super().__init__(name1,name2,name3)
        --snip--
```

# <font color=#FF0000> - 8.4.1 子类的方法__init__()  </font>

 ```python
 class Car():
    
    def __init__(self,make,model,year):
        self.make = make
        self.model = model
        self.year = year
        self.odometer_reading = 0
        
    def get_descriptive_name(self):
        long_name = str(self.year) + ' ' + self.make + ' ' +self.model
        return long_name.title()

class ElectricCar(Car):
    #电动车的独特之处
    def __init__(self,make,model,year):
        #初始化父类的属性
        super().__init__(make,model,year)

my_new_car = ElectricCar('tesla','model s','2016')
print(my_new_car.get_descriptive_name())
```
创建子类时，父类必须包含在当前文件中，且位于子类前面，定义了子类 ElectricCar，定义子类时，必须在括号内指定父类名称，方法 `__init__()`接受创建Car实例所需信息，super() 是一个特殊的函数，帮助Python将父类和子类关联起来，让Python调用 ElectricCar 的父类的方法  `__init__()`，让 ElectricCar 实例包含父类的所有属性，父类也称为超类（superclass），程序输出结果如下：
```python
2016 Tesla Model S
```

# <font color=#FF0000> - 8.4.2 Python 2.7 中的继承  </font>

在Python 2.7中，ElectricCar类的定义类似于下面这样：
```python
class Car(object):
    def __init__(self,make,model,year):
        --snip--

class ElectricCar(Car):
    def __init__(self,make,model,year):
        super(ElectricCar,self).__init__(make,model,year)
        --snip--
```

# <font color=#FF0000> - 8.4.3 给子类定义属性和方法  </font>

  让一个类继承另一个类后，可添加区分子类和父类所需的新属性和方法，下面添加一个电动车特有的属性（battery），以及一个描述该属性的方法：

```python
class Car():
    
    def __init__(self,make,model,year):
        self.make = make
        self.model = model
        self.year = year
        self.odometer_reading = 0
        
    def get_descriptive_name(self):
        long_name = str(self.year) + ' ' + self.make + ' ' +self.model
        return long_name.title()

class ElectricCar(Car):
    def __init__(self,make,model,year):
        super().__init__(make,model,year)
        self.battery_size = 80

    def describe_battery(self):
        print("This car has a " + str(self.battery_size) + "-KWh battery.")

my_new_car = ElectricCar('tesla','model s','2016')
print(my_new_car.get_descriptive_name())
my_new_car.describe_battery()
```
输出结果如下：
```python
2016 Tesla Model S
This car has a 80-KWh battery.
```

# <font color=#FF0000> - 8.4.4 重写父类的方法  </font>

  要重写父类的方法，只需要在子类中定义一个与要重写的父类方法同名的方法即可，这样，Python将不会考虑这个父类的方法，而只关心在子类中定义的相应方法，假设Car类有一个名为 fill_gas_tank() 的方法，对于电动车来说毫无意义，因此可以重写它：
```python
class ElectricCar(Car):
    --snip--

    def fill_gas_tank(self):
        print("This car doesn't need a gas tank!")
```

# <font color=#FF0000> - 8.4.5 将实例用作属性  </font>

```python
class Car():
    
    def __init__(self,make,model,year):
        self.make = make
        self.model = model
        self.year = year
        self.odometer_reading = 0
        
    def get_descriptive_name(self):
        long_name = str(self.year) + ' ' + self.make + ' ' +self.model
        return long_name.title()

class Battery():
    #一次模拟电动车电瓶的简单尝试
    def __init__(self,battery_size=70):
        #初始化电瓶的属性
        self.battery_size = battery_size

    def describe_battery(self):
        #打印一条描述电瓶容量的消息
        print("This car has a " + str(self.battery_size) + "-KWh battery.")

class ElectricCar(Car):
    def __init__(self,make,model,year):
        super().__init__(make,model,year)
        self.battery = Battery()

my_new_car = ElectricCar('tesla','model s','2016')
print(my_new_car.get_descriptive_name())
my_new_car.battery.describe_battery()
```
输出结果如下：

``` python
2016 Tesla Model S
This car has a 70-KWh battery.
```
看起来似乎做了多余的工作，但现在我们可以对电瓶添加更多的描述，而且不会导致 ElectricCar 类混乱不堪，下面再给Battery添加一个方法，使其能够根据电瓶容量报告汽车的续航里程：
```python
class Car():
    
    def __init__(self,make,model,year):
        self.make = make
        self.model = model
        self.year = year
        self.odometer_reading = 0
        
    def get_descriptive_name(self):
        long_name = str(self.year) + ' ' + self.make + ' ' +self.model
        return long_name.title()

class Battery():
    #一次模拟电动车电瓶的简单尝试
    def __init__(self,battery_size=70):
        #初始化电瓶的属性
        self.battery_size = battery_size

    def describe_battery(self):
        #打印一条描述电瓶容量的消息
        print("This car has a " + str(self.battery_size) + "-KWh battery.")

    def get_range(self):
        #打印一条消息，指出电瓶的续航里程
        if self.battery_size == 70:
            range = 240
        elif self.battery_size == 90:
            range = 280
        message = "This car can go approximately " + str(range)
        message += " miles on a full charge."
        print(message)
        
class ElectricCar(Car):
    def __init__(self,make,model,year):
        super().__init__(make,model,year)
        self.battery = Battery()

my_new_car = ElectricCar('tesla','model s','2016')
print(my_new_car.get_descriptive_name())
my_new_car.battery.describe_battery()
my_new_car.battery.get_range()
```
输出结果如下：
```python
2016 Tesla Model S
This car has a 70-KWh battery.
This car can go approximately 240 miles on a full charge.
```

# <font color=#FF0000> - 8.5 导入类  </font>

Python允许将类储存在模块中，然后在主程序中导入所需的模块
  
# <font color=#FF0000> - 8.5.1 导入单个类  </font>

```python
#car.py
#一个用于表示汽车的类

class Car():
    
    def __init__(self,make,model,year):
        #初始化描述汽车的属性
        self.make = make
        self.model = model
        self.year = year
        self.odometer_reading = 0
        
    def get_descriptive_name(self):
        #返回整洁的描述性名称
        long_name = str(self.year) + ' ' + self.make + ' ' +self.model
        return long_name.title()
    
    def read_odomter(self):
        #打印一条消息，指出汽车的里程
        print("This car has " + str(self.odometer_reading) + " miles on it.")

    def update_odometer(self):
        #将里程表读数设置为指定的值，拒绝将里程表往回拨
        if mileage >= self.odometer_reading:
            self.odometer_reading = mileage
        else:
            print("You can't roll back an odometer!")

    def increment_odometer(self,miles):
        #将里程表读数增加指定的量
        self.odometer_reading += miles
```
创建另一个文件——my_car.py，在其中导入Car类并创建其实例：
```python
#my_car.py

from car import Car

my_new_car = Car('audi','a9','2018')
print(my_new_car.get_descriptive_name())

my_new_car.odometer_reading = 23
my_new_car.read_odometer()
```
import语句让Python打开模块car，并导入其中的Car类，输出结果如下：

``` python
2018 Audi A9
This car has 23 miles on it.
```

# <font color=#FF0000> - 8.5.2 在一个模块中储存多个类  </font>

将类Battery和ElectricCar都加入到模块car.py中：
 ```python
#car.py
#一组用于表示燃油汽车和电动汽车的类

class Car():
    
    def __init__(self,make,model,year):
        #初始化描述汽车的属性
        self.make = make
        self.model = model
        self.year = year
        self.odometer_reading = 0
        
    def get_descriptive_name(self):
        #返回整洁的描述性名称
        long_name = str(self.year) + ' ' + self.make + ' ' +self.model
        return long_name.title()
    
    def read_odometer(self):
        #打印一条消息，指出汽车的里程
        print("This car has " + str(self.odometer_reading) + " miles on it.")

    def update_odometer(self):
        #将里程表读数设置为指定的值，拒绝将里程表往回拨
        if mileage >= self.odometer_reading:
            self.odometer_reading = mileage
        else:
            print("You can't roll back an odometer!")

    def increment_odometer(self,miles):
        #将里程表读数增加指定的量
        self.odometer_reading += miles

class Battery():
    #一次模拟电动车电瓶的简单尝试
    def __init__(self,battery_size=70):
        #初始化电瓶的属性
        self.battery_size = battery_size

    def describe_battery(self):
        #打印一条描述电瓶容量的消息
        print("This car has a " + str(self.battery_size) + "-KWh battery.")

    def get_range(self):
        #打印一条消息，指出电瓶的续航里程
        if self.battery_size == 70:
            range = 240
        elif self.battery_size == 90:
            range = 280
        message = "This car can go approximately " + str(range)
        message += " miles on a full charge."
        print(message)

class ElectricCar(Car):
    #模拟电动车的独特之处
    def __init__(self,make,model,year):
        #初始化父类的属性，再初始化电动车特有的属性
        super().__init__(make,model,year)
        self.battery = Battery()
```
新建一个my_electric_car.py的文件，导入ElectricCar类，并创建一辆电动车：
```python
#my_electric_car.py

from car import ElectricCar

my_tesla = ElectricCar('tesla','model s','2016')

print(my_tesla.get_descriptive_name())
my_tesla.battery.describe_battery()
my_tesla.battery.get_range()
```
输出结果如下：
```python
2016 Tesla Model S
This car has a 70-KWh battery.
This car can go approximately 240 miles on a full charge.
```

# <font color=#FF0000> - 8.5.3 从一个模块中导入多个类  </font>

  可根据需要在程序文件中导入任意数量的类，假如我们要在同一个程序中创建普通汽车和电动汽车，就需要将类Car和ElectricCar类都导入，多个类之间用逗号进行分隔：
 ```python
 #my_car.py

from car import Car,ElectricCar

my_audi = Car('audi','a9','2018')
print(my_audi.get_descriptive_name())

my_tesla = ElectricCar('tesla','model s','2016')
print(my_tesla.get_descriptive_name())
```
输出结果如下：
```python
2018 Audi A9
2016 Tesla Model S
```

# <font color=#FF0000> - 8.5.4 导入整个模块  </font>

导入整个模块后，需要使用句点表示法访问需要的类：
```python
#my_car.py

import car

my_audi = car.Car('audi','a9','2018')
print(my_audi.get_descriptive_name())

my_tesla = car.ElectricCar('tesla','model s','2016')
print(my_tesla.get_descriptive_name())
```
我们导入了整个car模块，需要使用语法 `module_name.class_name` 访问需要的类，程序输出结果与8.5.3一致：
```python
2018 Audi A9
2016 Tesla Model S
```

# <font color=#FF0000> - 8.5.5 导入模块中的所有类  </font>

  要导入模块中的所有类，可使用以下语法：
```python
from module_name import *
```
这种导入方法是不推荐的，没有明确指出你使用了模块中的哪些类，还可能引发名称方面的困惑，需要从一个模块中导入很多类时，最好导入整个模块，并使用 `module_name.class_name` 语法来访问类

# <font color=#FF0000> - 8.5.6 在一个模块中导入另一个模块  </font>

  有时候需要将类分散到多个模块当中，以免模块太大，或者在同一个模块中储存不相关的类，将类储存在多个模块中时，一个模块中的类可能会依赖于另一个模块中的类，这种情况下，我们可以在前一个模块中导入必要的类，以下例子中，将Car类储存在一个模块当中，并将ElectricCar和Battery类储存在另一个模块当中，将第二个模块命名为electric_car.py，并将ElectricCar和Battery类复制到这个模块中：
```python
#electric_car.py
#一组可用于表示电动汽车的类

from car import Car

class Battery():
    #一次模拟电动车电瓶的简单尝试
    def __init__(self,battery_size=70):
        #初始化电瓶的属性
        self.battery_size = battery_size

    def describe_battery(self):
        #打印一条描述电瓶容量的消息
        print("This car has a " + str(self.battery_size) + "-KWh battery.")

    def get_range(self):
        #打印一条消息，指出电瓶的续航里程
        if self.battery_size == 70:
            range = 240
        elif self.battery_size == 90:
            range = 280
        message = "This car can go approximately " + str(range)
        message += " miles on a full charge."
        print(message)

class ElectricCar(Car):
    #模拟电动车的独特之处
    def __init__(self,make,model,year):
        #初始化父类的属性，再初始化电动车特有的属性
        super().__init__(make,model,year)
        self.battery = Battery()
```

```python
#car.py
#一个可用于表示汽车的类

class Car():
    
    def __init__(self,make,model,year):
        #初始化描述汽车的属性
        self.make = make
        self.model = model
        self.year = year
        self.odometer_reading = 0
        
    def get_descriptive_name(self):
        #返回整洁的描述性名称
        long_name = str(self.year) + ' ' + self.make + ' ' +self.model
        return long_name.title()
    
    def read_odometer(self):
        #打印一条消息，指出汽车的里程
        print("This car has " + str(self.odometer_reading) + " miles on it.")

    def update_odometer(self):
        #将里程表读数设置为指定的值，拒绝将里程表往回拨
        if mileage >= self.odometer_reading:
            self.odometer_reading = mileage
        else:
            print("You can't roll back an odometer!")

    def increment_odometer(self,miles):
        #将里程表读数增加指定的量
        self.odometer_reading += miles
```
现在可以分别从每个模块中导入类：
```python
#my_car.py

from car import Car
from electric_car import ElectricCar

my_audi = Car('audi','a9','2018')
print(my_audi.get_descriptive_name())

my_tesla = ElectricCar('tesla','model s','2016')
print(my_tesla.get_descriptive_name())
```
输出结果如下：
```python
2018 Audi A9
2016 Tesla Model S
```

# <font color=#FF0000> - 8.6 Python标准库  </font>

Python标准库是一组模块，安装的Python都包含它，我们可以使用标准库中的任何函数和类，只需要在程序的开头包含一条简单的import语句，下面以模块collections中的一个类——OrderedDict（创建字典并记录其中的键-值对的添加顺序）为例：
```python
#favorite_languages.py

from collections import OrderedDict

favorite_languages = OrderedDict()

favorite_languages ['jen'] = 'python'
favorite_languages ['sarah'] = 'c'
favorite_languages ['edward'] = 'java'
favorite_languages ['anly'] = 'python'

for name,language in favorite_languages.items():
    print(name.title() + "'s favorite languages is " +
          language.title() + ".")
```
输出结果如下：
```python
Jen's favorite languages is Python.
Sarah's favorite languages is C.
Edward's favorite languages is Java.
Anly's favorite languages is Python.
```
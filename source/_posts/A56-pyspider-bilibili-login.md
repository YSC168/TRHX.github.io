---
title: Python3 爬虫实战 — 模拟登陆哔哩哔哩【滑动验证码对抗】
tags:
  - 爬虫
  - 哔哩哔哩
categories: 
  - Python3 学习笔记
  - 爬虫实战
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/combat.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---

> 登陆时间：2019-10-21
> 实现难度：★★★☆☆☆
> 请求链接：https://passport.bilibili.com/login
> 实现目标：模拟登陆哔哩哔哩，攻克滑动验证码
> 涉及知识：滑动验证码的攻克、自动化测试工具 Selenium 的使用
> 完整代码：https://github.com/TRHX/Python3-Spider-Practice/tree/master/bilibili-login
> 其他爬虫实战代码合集（持续更新）：https://github.com/TRHX/Python3-Spider-Practice
> 爬虫实战专栏（持续更新）：https://itrhx.blog.csdn.net/article/category/9351278

---

<!--more-->

# <font color=#ff0000>【1x00】思维导图</font>

<fancybox>
![01](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A56/01.png)
</fancybox>

- 利用自动化测试工具 Selenium 直接模拟人的行为方式来完成验证

- 分析页面，想办法找到滑动验证码的完整图片、带有缺口的图片和需要滑动的图片

- 对比原始的图片和带缺口的图片的像素，像素不同的地方就是缺口位置

- 计算出滑块缺口的位置，得到所需要滑动的距离

- 拖拽时要模仿人的行为，由于有个对准过程，所以要构造先快后慢的运动轨迹

- 最后利用 Selenium 进行对滑块的拖拽

<fancybox>
![02](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A56/02.png)
</fancybox>

---

# <font color=#ff0000>【2x00】登陆模块</font>

## <font color=#1BC3FB>【2x01】初始化函数</font>

```python
def init():
    global url, browser, username, password, wait
    url = 'https://passport.bilibili.com/login'
    # path是谷歌浏览器驱动的目录，如果已经将目录添加到系统变量，则不用设置此路径
    path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
    chrome_options = Options()
    chrome_options.add_argument('--start-maximized')
    browser = webdriver.Chrome(executable_path=path, chrome_options=chrome_options)
    # 你的哔哩哔哩用户名
    username = '155********'
    # 你的哔哩哔哩登陆密码
    password = '***********'
    wait = WebDriverWait(browser, 20)
```
`global` 关键字定义了发起请求的url、用户名、密码等全局变量，随后是登录页面url、谷歌浏览器驱动的目录path、实例化 Chrome 浏览器、设置浏览器分辨率最大化、用户名、密码、`WebDriverWait()` 方法设置等待超时

---

## <font color=#1BC3FB>【2x02】登陆函数</font>

```python
def login():
    browser.get(url)
    # 获取用户名输入框
    user = wait.until(EC.presence_of_element_located((By.ID, 'login-username')))
    # 获取密码输入框
    passwd = wait.until(EC.presence_of_element_located((By.ID, 'login-passwd')))
    # 输入用户名
    user.send_keys(username)
    # 输入密码
    passwd.send_keys(password)
    # 获取登录按钮
    login_btn = wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, 'a.btn.btn-login')))
    # 随机暂停几秒
    time.sleep(random.random() * 3)
    # 点击登陆按钮
    login_btn.click()

```

等待用户名输入框和密码输入框对应的 ID 节点加载出来

获取这两个节点，用户名输入框 `id="login-username"`，密码输入框 `id="login-passwd"`

调用 `send_keys()` 方法输入用户名和密码

获取登录按钮 `class="btn btn-login"`

随机产生一个数并将其扩大三倍作为暂停时间

最后调用 `click()` 方法实现登录按钮的点击

---

# <font color=#ff0000>【3x00】验证码处理模块</font>

## <font color=#1BC3FB>【3x01】验证码元素查找函数</font>

```python
def find_element():
    # 获取带有缺口的图片
    c_background = wait.until(
        EC.presence_of_element_located((By.CSS_SELECTOR, 'canvas.geetest_canvas_bg.geetest_absolute')))
    # 获取需要滑动的图片
    c_slice = wait.until(
        EC.presence_of_element_located((By.CSS_SELECTOR, 'canvas.geetest_canvas_slice.geetest_absolute')))
    # 获取完整的图片
    c_full_bg = wait.until(
        EC.presence_of_element_located((By.CSS_SELECTOR, 'canvas.geetest_canvas_fullbg.geetest_fade.geetest_absolute')))
    # 隐藏需要滑动的图片
    hide_element(c_slice)
    # 保存带有缺口的图片
    save_screenshot(c_background, 'back')
    # 显示需要滑动的图片
    show_element(c_slice)
    # 保存需要滑动的图片
    save_screenshot(c_slice, 'slice')
    # 显示完整的图片
    show_element(c_full_bg)
    # 保存完整的图片
    save_screenshot(c_full_bg, 'full')

```

获取验证码的三张图片，分别是完整的图片、带有缺口的图片和需要滑动的图片

分析页面代码，三张图片是由 3 个 canvas 组成，3 个 canvas 元素包含 CSS  `display` 属性，`display:block` 为可见，`display:none` 为不可见，在分别获取三张图片时要将其他两张图片设置为 `display:none`，这样做才能单独提取到每张图片

定位三张图片的 class 分别为：带有缺口的图片（c_background）：`geetest_canvas_bg geetest_absolute`、需要滑动的图片（c_slice）：`geetest_canvas_slice geetest_absolute`、完整图片（c_full_bg）：`geetest_canvas_fullbg geetest_fade geetest_absolute`

最后传值给 `save_screenshot()` 函数，进一步对验证码进行处理

<fancybox>
![03](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A56/03.png)
</fancybox>

---

## <font color=#1BC3FB>【3x02】元素可见性设置函数</font>

```python
# 设置元素不可见
def hide_element(element):
    browser.execute_script("arguments[0].style=arguments[1]", element, "display: none;")


# 设置元素可见
def show_element(element):
    browser.execute_script("arguments[0].style=arguments[1]", element, "display: block;")

```

---

## <font color=#1BC3FB>【3x03】验证码截图函数</font>

```python
def save_screenshot(obj, name):
    try:
        # 首先对出现验证码后的整个页面进行截图保存
        pic_url = browser.save_screenshot('.\\bilibili.png')
        print("%s:截图成功!" % pic_url)
        # 计算传入的obj，也就是三张图片的位置信息
        left = obj.location['x']
        top = obj.location['y']
        right = left + obj.size['width']
        bottom = top + obj.size['height']
        # 打印输出一下每一张图的位置信息
        print('图：' + name)
        print('Left %s' % left)
        print('Top %s' % top)
        print('Right %s' % right)
        print('Bottom %s' % bottom)
        print('')
        # 在整个页面截图的基础上，根据位置信息，分别剪裁出三张验证码图片并保存
        im = Image.open('.\\bilibili.png')
        im = im.crop((left, top, right, bottom))
        file_name = 'bili_' + name + '.png'
        im.save(file_name)
    except BaseException as msg:
        print("%s:截图失败!" % msg)

```

`location` 属性可以返回该图片对象在浏览器中的位置，坐标轴是以屏幕左上角为原点，x轴向右递增，y轴向下递增

`size` 属性可以返回该图片对象的高度和宽度，由此可以得到验证码的位置信息

首先调用 `save_screenshot()` 属性对整个页面截图并保存

然后向 `crop()` 方法传入验证码的位置信息，由位置信息再对验证码进行剪裁并保存

---

# <font color=#ff0000>【4x00】验证码滑动模块</font>

## <font color=#1BC3FB>【4x01】滑动主函数</font>

```python
def slide():
    distance = get_distance(Image.open('.\\bili_back.png'), Image.open('.\\bili_full.png'))
    print('计算偏移量为：%s Px' % distance)
    trace = get_trace(distance - 5)
    move_to_gap(trace)
    time.sleep(3)
```

向 `get_distance()` 函数传入完整的图片和缺口图片，计算滑块需要滑动的距离，再把距离信息传入 `get_trace()` 函数，构造滑块的移动轨迹，最后根据轨迹信息调用 `move_to_gap()` 函数移动滑块完成验证

---

## <font color=#1BC3FB>【4x02】缺口位置寻找函数</font>

```python
def is_pixel_equal(bg_image, fullbg_image, x, y):
    # 获取两张图片对应像素点的RGB数据
    bg_pixel = bg_image.load()[x, y]
    fullbg_pixel = fullbg_image.load()[x, y]
    # 设定一个阈值
    threshold = 60
    # 比较两张图 RGB 的绝对值是否均小于定义的阈值
    if (abs(bg_pixel[0] - fullbg_pixel[0] < threshold) and abs(bg_pixel[1] - fullbg_pixel[1] < threshold) and abs(
            bg_pixel[2] - fullbg_pixel[2] < threshold)):
        return True
    else:
        return False

```

将完整图片和缺口图片两个对象分别赋值给变量 `bg_image` 和 `fullbg_image`，接下来对比图片获取缺口。遍历图片的每个坐标点，获取两张图片对应像素点的 RGB 数据，判断像素的各个颜色之差，`abs()` 用于取绝对值，比较两张图 RGB 的绝对值是否均小于定义的阈值 threshold，如果绝对值均在阈值之内，则代表像素点相同，继续遍历，否则代表不相同的像素点，即缺口的位置

---

## <font color=#1BC3FB>【4x03】计算滑块移动距离函数</font>


```python
def get_distance(bg_image, fullbg_image):
    # 滑块的初始位置
    distance = 60
    # 遍历两张图片的每个像素
    for i in range(distance, fullbg_image.size[0]):
        for j in range(fullbg_image.size[1]):
            # 调用缺口位置寻找函数
            if not is_pixel_equal(fullbg_image, bg_image, i, j):
                return i

```

`get_distance()` 方法即获取缺口位置的方法，此方法的参数是两张图片，一张为完整的图片，另一张为带缺口的图片，`distance` 为滑块的初始位置，遍历两张图片的每个像素，利用 `is_pixel_equal()` 缺口位置寻找函数判断两张图片同一位置的像素是否相同，若不相同则返回该点的值

---

## <font color=#1BC3FB>【4x04】构造移动轨迹函数</font>

```python
def get_trace(distance):
    trace = []
    # 设置加速距离为总距离的4/5
    faster_distance = distance * (4 / 5)
    # 设置初始位置、初始速度、时间间隔
    start, v0, t = 0, 0, 0.1
    while start < distance:
        if start < faster_distance:
            a = 10
        else:
            a = -10
        # 位移
        move = v0 * t + 1 / 2 * a * t * t
        # 当前时刻的速度
        v = v0 + a * t
        v0 = v
        start += move
        trace.append(round(move))
    # trace 记录了每个时间间隔移动了多少位移
    return trace

```

`get_trace()` 方法传入的参数为移动的总距离，返回的是运动轨迹，运动轨迹用 trace 表示，它是一个列表，列表的每个元素代表每次移动多少距离，利用 Selenium 进行对滑块的拖拽时要模仿人的行为，由于有个对准过程，所以是先快后慢，匀速移动、随机速度移动都不会成功，因此要设置一个加速和减速的距离，这里设置加速距离 `faster_distance` 是总距离 `distance` 的4/5倍，滑块滑动的加速度用 a 来表示，当前速度用 v 表示，初速度用 v0 表示，位移用 move 表示，所需时间用 t 表示，它们之间满足以下关系：

```python
move = v0 * t + 0.5 * a * t * t 
v = v0 + a * t
```

设置初始位置、初始速度、时间间隔分别为0, 0, 0.1，加速阶段和减速阶段的加速度分别设置为10和-10，直到运动轨迹达到总距离时，循环终止，最后得到的 trace 记录了每个时间间隔移动了多少位移，这样滑块的运动轨迹就得到了

---

## <font color=#1BC3FB>【4x05】模拟拖动函数</font>

```python
def move_to_gap(trace):
    # 获取滑动按钮
    slider = wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, 'div.geetest_slider_button')))
    # 点击并拖动滑块
    ActionChains(browser).click_and_hold(slider).perform()
    # 遍历运动轨迹获取每小段位移距离
    for x in trace:
        # 移动此位移
        ActionChains(browser).move_by_offset(xoffset=x, yoffset=0).perform()
    time.sleep(0.5)
    # 释放鼠标
    ActionChains(browser).release().perform()

```

传入的参数为运动轨迹，首先查找到滑动按钮，然后调用 ActionChains 的 `click_and_hold()` 方法按住拖动底部滑块，`perform()` 方法用于执行，遍历运动轨迹获取每小段位移距离，调用 `move_by_offset()` 方法移动此位移，最后调用 `release()` 方法松开鼠标即可

---

# <font color=#FF0000>【5x00】完整代码</font>

```python
# =============================================
# --*-- coding: utf-8 --*--
# @Time    : 2019-10-21
# @Author  : TRHX
# @Blog    : www.itrhx.com
# @CSDN    : https://blog.csdn.net/qq_36759224
# @FileName: bilibili.py
# @Software: PyCharm
# =============================================

from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By
from selenium.webdriver import ActionChains
import time
import random
from PIL import Image


# 初始化函数
def init():
    global url, browser, username, password, wait
    url = 'https://passport.bilibili.com/login'
    # path是谷歌浏览器驱动的目录，如果已经将目录添加到系统变量，则不用设置此路径
    path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
    chrome_options = Options()
    chrome_options.add_argument('--start-maximized')
    browser = webdriver.Chrome(executable_path=path, chrome_options=chrome_options)
    # 你的哔哩哔哩用户名
    username = '155********'
    # 你的哔哩哔哩登录密码
    password = '***********'
    wait = WebDriverWait(browser, 20)


# 登录函数
def login():
    browser.get(url)
    # 获取用户名输入框
    user = wait.until(EC.presence_of_element_located((By.ID, 'login-username')))
    # 获取密码输入框
    passwd = wait.until(EC.presence_of_element_located((By.ID, 'login-passwd')))
    # 输入用户名
    user.send_keys(username)
    # 输入密码
    passwd.send_keys(password)
    # 获取登录按钮
    login_btn = wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, 'a.btn.btn-login')))
    # 随机暂停几秒
    time.sleep(random.random() * 3)
    # 点击登陆按钮
    login_btn.click()


# 验证码元素查找函数
def find_element():
    # 获取带有缺口的图片
    c_background = wait.until(
        EC.presence_of_element_located((By.CSS_SELECTOR, 'canvas.geetest_canvas_bg.geetest_absolute')))
    # 获取需要滑动的图片
    c_slice = wait.until(
        EC.presence_of_element_located((By.CSS_SELECTOR, 'canvas.geetest_canvas_slice.geetest_absolute')))
    # 获取完整的图片
    c_full_bg = wait.until(
        EC.presence_of_element_located((By.CSS_SELECTOR, 'canvas.geetest_canvas_fullbg.geetest_fade.geetest_absolute')))
    # 隐藏需要滑动的图片
    hide_element(c_slice)
    # 保存带有缺口的图片
    save_screenshot(c_background, 'back')
    # 显示需要滑动的图片
    show_element(c_slice)
    # 保存需要滑动的图片
    save_screenshot(c_slice, 'slice')
    # 显示完整的图片
    show_element(c_full_bg)
    # 保存完整的图片
    save_screenshot(c_full_bg, 'full')


# 设置元素不可见
def hide_element(element):
    browser.execute_script("arguments[0].style=arguments[1]", element, "display: none;")


# 设置元素可见
def show_element(element):
    browser.execute_script("arguments[0].style=arguments[1]", element, "display: block;")


# 验证码截图函数
def save_screenshot(obj, name):
    try:
        # 首先对出现验证码后的整个页面进行截图保存
        pic_url = browser.save_screenshot('.\\bilibili.png')
        print("%s:截图成功!" % pic_url)
        # 计算传入的obj，也就是三张图片的位置信息
        left = obj.location['x']
        top = obj.location['y']
        right = left + obj.size['width']
        bottom = top + obj.size['height']
        # 打印输出一下每一张图的位置信息
        print('图：' + name)
        print('Left %s' % left)
        print('Top %s' % top)
        print('Right %s' % right)
        print('Bottom %s' % bottom)
        print('')
        # 在整个页面截图的基础上，根据位置信息，分别剪裁出三张验证码图片并保存
        im = Image.open('.\\bilibili.png')
        im = im.crop((left, top, right, bottom))
        file_name = 'bili_' + name + '.png'
        im.save(file_name)
    except BaseException as msg:
        print("%s:截图失败!" % msg)


# 滑动模块的主函数
def slide():
    distance = get_distance(Image.open('.\\bili_back.png'), Image.open('.\\bili_full.png'))
    print('计算偏移量为：%s Px' % distance)
    trace = get_trace(distance - 5)
    move_to_gap(trace)
    time.sleep(3)


# 计算滑块移动距离函数
def get_distance(bg_image, fullbg_image):
    # 滑块的初始位置
    distance = 60
    # 遍历两张图片的每个像素
    for i in range(distance, fullbg_image.size[0]):
        for j in range(fullbg_image.size[1]):
            # 调用缺口位置寻找函数
            if not is_pixel_equal(fullbg_image, bg_image, i, j):
                return i


# 缺口位置寻找函数
def is_pixel_equal(bg_image, fullbg_image, x, y):
    # 获取两张图片对应像素点的RGB数据
    bg_pixel = bg_image.load()[x, y]
    fullbg_pixel = fullbg_image.load()[x, y]
    # 设定一个阈值
    threshold = 60
    # 比较两张图 RGB 的绝对值是否均小于定义的阈值
    if (abs(bg_pixel[0] - fullbg_pixel[0] < threshold) and abs(bg_pixel[1] - fullbg_pixel[1] < threshold) and abs(
            bg_pixel[2] - fullbg_pixel[2] < threshold)):
        return True
    else:
        return False


# 构造移动轨迹函数
def get_trace(distance):
    trace = []
    # 设置加速距离为总距离的4/5
    faster_distance = distance * (4 / 5)
    # 设置初始位置、初始速度、时间间隔
    start, v0, t = 0, 0, 0.1
    while start < distance:
        if start < faster_distance:
            a = 10
        else:
            a = -10
        # 位移
        move = v0 * t + 1 / 2 * a * t * t
        # 当前时刻的速度
        v = v0 + a * t
        v0 = v
        start += move
        trace.append(round(move))
    # trace 记录了每个时间间隔移动了多少位移
    return trace


# 模拟拖动函数
def move_to_gap(trace):
    # 获取滑动按钮
    slider = wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, 'div.geetest_slider_button')))
    # 点击并拖动滑块
    ActionChains(browser).click_and_hold(slider).perform()
    # 遍历运动轨迹获取每小段位移距离
    for x in trace:
        # 移动此位移
        ActionChains(browser).move_by_offset(xoffset=x, yoffset=0).perform()
    time.sleep(0.5)
    # 释放鼠标
    ActionChains(browser).release().perform()


if __name__ == '__main__':
    init()
    login()
    find_element()
    slide()

```

# <font color=#FF0000>【6x00】效果实现动图</font>

最终实现效果图：（关键信息已经过打码处理）

<fancybox>
![04](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A56/bilibili.gif)
</fancybox>

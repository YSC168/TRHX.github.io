---
title: Python3 爬虫学习笔记 C13
tags:
  - 爬虫
  - 滑动验证码
categories: 
  - Python3 学习笔记
  - 爬虫学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/spider.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 爬虫学习笔记第十三章 —— 【验证码对抗系列 — 滑动验证码】</font></center>

<!--more-->

---

# <font color=#ff0000>【13.1】关于滑动验证码</font>

滑动验证码属于行为式验证码，需要通过用户的操作行为来完成验证，一般是根据提示用鼠标将滑块拖动到指定的位置完成验证，此类验证码背景图片采用多种图像加密技术，且添加了很多随机效果，能有效防止OCR文字识别，另外，验证码上的文字采用了随机印刷技术，能够随机采用多种字体、多种变形的实时随机印刷，防止暴力破解；斗鱼、哔哩哔哩、淘宝等平台都使用了滑动验证码

<fancybox>
![01](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A43/01.png)
</fancybox>

---

# <font color=#ff0000>【13.2】滑动验证码攻克思路</font>

利用自动化测试工具 Selenium 直接模拟人的行为方式来完成验证，首先要分析页面，想办法找到滑动验证码的完整图片、带有缺口的图片和需要滑动的图片，通过对比原始的图片和带滑块缺口的图片的像素，像素不同的地方就是缺口位置，计算出滑块缺口的位置，得到所需要滑动的距离，最后利用 Selenium 进行对滑块的拖拽，拖拽时要模仿人的行为，由于有个对准过程，所以是先快后慢，匀速移动、随机速度移动都不会成功

以下以哔哩哔哩为例来做模拟登录练习

---

# <font color=#ff0000>【13.3】模拟登录 bilibili — 总体思路</font>
首先使用 Selenium 模拟登陆 bilibili，自动输入账号密码，查找到登陆按钮并点击，使其出现滑动验证码，此时分析页面，滑动验证组件是由3个 canvas 组成，分别代表完整图片、带有缺口的图片和需要滑动的图片，3个 canvas 元素包含 CSS  `display` 属性，`display:block` 为可见，`display:none` 为不可见，分别获取三张图片时要将其他两张图片设置为 `display:none`，获取元素位置后即可对图片截图并保存，通过图片像素对比，找到缺口位置即为滑块要移动的距离，随后构造滑动轨迹，按照先加速后减速的方式移动滑块完成验证。

整个程序包含的函数：

```python
def init(): 初始化函数，定义全局变量
def login(): 登录函数，输入账号密码并点击登录
def find_element(): 验证码元素查找函数，查找三张图的元素
def hide_element(): 设置元素不可见函数
def show_element(): 设置元素可见函数
def save_screenshot(): 验证码截图函数，截取三张图并保存
def slide(): 滑动函数
def is_pixel_equal(): 像素判断函数，寻找缺口位置
def get_distance(): 计算滑块移动距离函数
def get_track(): 构造移动轨迹函数
def move_to_gap(): 模拟拖动函数
```

整个程序用到的库：

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By
from selenium.webdriver import ActionChains
import time
import random
```

---

# <font color=#ff0000>【13.4】主函数</font>

```python
if __name__ == '__main__':
    init()
    login()
    find_element()
    slide()
```

---

# <font color=#ff0000>【13.5】初始化函数</font>

```python
def init():
    global url, browser, username, password, wait
    url = 'https://passport.bilibili.com/login'
    path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
    chrome_options = Options()
    chrome_options.add_argument('--start-maximized')
    browser = webdriver.Chrome(executable_path=path, chrome_options=chrome_options)
    username = '155********'
    password = '***********'
    wait = WebDriverWait(browser, 20)
```
global 关键字定义了全局变量，随后是登录页面url、谷歌浏览器驱动的目录path、实例化 Chrome 浏览器、设置浏览器分辨率最大化、用户名、密码、WebDriverWait() 方法设置等待超时

---

# <font color=#ff0000>【13.6】登录函数</font>

```python
def login():
    browser.get(url)
    user = wait.until(EC.presence_of_element_located((By.ID, 'login-username')))
    passwd = wait.until(EC.presence_of_element_located((By.ID, 'login-passwd')))
    user.send_keys(username)
    passwd.send_keys(password)
    login_btn = wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, 'a.btn.btn-login')))
    time.sleep(random.random() * 3)
    login_btn.click()
```

等待用户名输入框和密码输入框对应的 ID 节点加载出来，分析页面可知，用户名输入框 `id="login-username"`，密码输入框 `id="login-passwd"`，获取这两个节点，调用 `send_keys()` 方法输入用户名和密码，随后获取登录按钮，分析页面可知登录按钮 `class="btn btn-login"`，随机产生一个数并将其扩大三倍作为暂停时间，最后调用 `click()` 方法实现登录按钮的点击

---

# <font color=#ff0000>【13.7】验证码元素查找函数</font>

```python
def find_element():
    c_background = wait.until(
        EC.presence_of_element_located((By.CSS_SELECTOR, 'canvas.geetest_canvas_bg.geetest_absolute')))
    c_slice = wait.until(
        EC.presence_of_element_located((By.CSS_SELECTOR, 'canvas.geetest_canvas_slice.geetest_absolute')))
    c_full_bg = wait.until(
        EC.presence_of_element_located((By.CSS_SELECTOR, 'canvas.geetest_canvas_fullbg.geetest_fade.geetest_absolute')))
    hide_element(c_slice)
    save_screenshot(c_background, 'back')
    show_element(c_slice)
    save_screenshot(c_slice, 'slice')
    show_element(c_full_bg)
    save_screenshot(c_full_bg, 'full')
```

我们要获取验证码的三张图片，分别是完整的图片、带有缺口的图片和需要滑动的图片，分析页面代码，这三张图片是由 3 个 canvas 组成，3 个 canvas 元素包含 CSS  `display` 属性，`display:block` 为可见，`display:none` 为不可见，在分别获取三张图片时要将其他两张图片设置为 `display:none`，这样做才能单独提取到每张图片，定位三张图片的 class 分别为：带有缺口的图片（c_background）：`geetest_canvas_bg geetest_absolute`、需要滑动的图片（c_slice）：`geetest_canvas_slice geetest_absolute`、完整图片（c_full_bg）：`geetest_canvas_fullbg geetest_fade geetest_absolute`，随后传值给 `save_screenshot()` 函数，进一步对验证码进行处理

<fancybox>
![02](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A43/02.png)
</fancybox>

---

# <font color=#ff0000>【13.8】元素可见性设置函数</font>

```
# 设置元素不可见
def hide_element(element):
    browser.execute_script("arguments[0].style=arguments[1]", element, "display: none;")


# 设置元素可见
def show_element(element):
    browser.execute_script("arguments[0].style=arguments[1]", element, "display: block;")
```

---

# <font color=#ff0000>【13.9】验证码截图函数</font>

```python
def save_screenshot(obj, name):
    try:
        pic_url = browser.save_screenshot('.\\bilibili.png')
        print("%s:截图成功!" % pic_url)
        left = obj.location['x']
        top = obj.location['y']
        right = left + obj.size['width']
        bottom = top + obj.size['height']
        print('图：' + name)
        print('Left %s' % left)
        print('Top %s' % top)
        print('Right %s' % right)
        print('Bottom %s' % bottom)
        print('')
        im = Image.open('.\\bilibili.png')
        im = im.crop((left, top, right, bottom))
        file_name = 'bili_' + name + '.png'
        im.save(file_name)
    except BaseException as msg:
        print("%s:截图失败!" % msg)
```

`location` 属性可以返回该图片对象在浏览器中的位置，坐标轴是以屏幕左上角为原点，x轴向右递增，y轴向下递增，`size` 属性可以返回该图片对象的高度和宽度，由此可以得到验证码的位置信息，首先调用 `save_screenshot()` 属性对整个页面截图并保存，然后向 `crop()` 方法传入验证码的位置信息，由位置信息再对验证码进行剪裁并保存

---

# <font color=#ff0000>【13.10】滑动函数</font>

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

# <font color=#ff0000>【13.11】计算滑块移动距离函数</font>

```python
def get_distance(bg_image, fullbg_image):
    distance = 60
    for i in range(distance, fullbg_image.size[0]):
        for j in range(fullbg_image.size[1]):
            if not is_pixel_equal(fullbg_image, bg_image, i, j):
                return i
```

`get_distance()` 方法即获取缺口位置的方法，此方法的参数是两张图片，一张为完整的图片，另一张为带缺口的图片，distance 为滑块的初始位置，遍历两张图片的每个像素，利用 `is_pixel_equal()` 像素判断函数判断两张图片同一位置的像素是否相同，比较两张图 RGB 的绝对值是否均小于定义的阈值 threshold，如果绝对值均在阈值之内，则代表像素点相同，继续遍历，否则代表不相同的像素点，即缺口的位置

---

# <font color=#ff0000>【13.12】像素判断函数</font>

```python
def is_pixel_equal(bg_image, fullbg_image, x, y):
    bg_pixel = bg_image.load()[x, y]
    fullbg_pixel = fullbg_image.load()[x, y]
    threshold = 60
    if (abs(bg_pixel[0] - fullbg_pixel[0] < threshold) and abs(bg_pixel[1] - fullbg_pixel[1] < threshold) and abs(
            bg_pixel[2] - fullbg_pixel[2] < threshold)):
        return True
    else:
        return False
```

将完整图片和缺口图片两个对象分别赋值给变量 bg_image和 fullbg_image，接下来对比图片获取缺口。我们在这里遍历图片的每个坐标点，获取两张图片对应像素点的 RGB 数据，判断像素的各个颜色之差，`abs()` 用于取绝对值，如果二者的 RGB 数据差距在一定范围内，那就代表两个像素相同，继续比对下一个像素点，如果差距超过一定范围，则代表像素点不同，当前位置即为缺口位置

---

# <font color=#ff0000>【13.13】构造移动轨迹函数</font>

```python
def get_trace(distance):
    trace = []
    faster_distance = distance * (4 / 5)
    start, v0, t = 0, 0, 0.1
    while start < distance:
        if start < faster_distance:
            a = 20
        else:
            a = -20
        move = v0 * t + 1 / 2 * a * t * t
        v = v0 + a * t
        v0 = v
        start += move
        trace.append(round(move))
    return trace
```

`get_trace()` 方法传入的参数为移动的总距离，返回的是运动轨迹，运动轨迹用 trace 表示，它是一个列表，列表的每个元素代表每次移动多少距离，利用 Selenium 进行对滑块的拖拽时要模仿人的行为，由于有个对准过程，所以是先快后慢，匀速移动、随机速度移动都不会成功，因此要设置一个加速和减速的距离，这里设置加速距离 `faster_distance` 是总距离 `distance` 的4/5倍，滑块滑动的加速度用 a 来表示，当前速度用 v 表示，初速度用 v0 表示，位移用 move 表示，所需时间用 t 表示，它们之间满足以下关系：

```
move = v0 * t + 0.5 * a * t * t 
v = v0 + a * t
```

设置初始位置、初始速度、时间间隔分别为0, 0, 0.1，加速阶段和减速阶段的加速度分别设置为20和-20，直到运动轨迹达到总距离时，循环终止，最后得到的 trace 记录了每个时间间隔移动了多少位移，这样滑块的运动轨迹就得到了

---

# <font color=#ff0000>【13.14】模拟拖动函数</font>

```python
def move_to_gap(trace):
    slider = wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, 'div.geetest_slider_button')))
    ActionChains(browser).click_and_hold(slider).perform()
    for x in trace:
        ActionChains(browser).move_by_offset(xoffset=x, yoffset=0).perform()
    time.sleep(0.5)
    ActionChains(browser).release().perform()
```

传入的参数为运动轨迹，首先查找到滑动按钮，然后调用 ActionChains 的 `click_and_hold()` 方法按住拖动底部滑块，`perform()` 方法用于执行，遍历运动轨迹获取每小段位移距离，调用 `move_by_offset()` 方法移动此位移，最后调用 `release()` 方法松开鼠标即可

---

# <font color=#ff0000>【13.15】效果实现动图</font>

最终实现效果图：（关键信息已经过打码处理）

<fancybox>
![03](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A43/bilibili.gif)
</fancybox>

---

# <font color=#ff0000>【13.16】完整代码</font>

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By
from selenium.webdriver import ActionChains
import time
import random
from PIL import Image


def init():
    global url, browser, username, password, wait
    url = 'https://passport.bilibili.com/login'
    path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
    chrome_options = Options()
    chrome_options.add_argument('--start-maximized')
    browser = webdriver.Chrome(executable_path=path, chrome_options=chrome_options)
    username = '155********'
    password = '***********'
    wait = WebDriverWait(browser, 20)


def login():
    browser.get(url)
    user = wait.until(EC.presence_of_element_located((By.ID, 'login-username')))
    passwd = wait.until(EC.presence_of_element_located((By.ID, 'login-passwd')))
    user.send_keys(username)
    passwd.send_keys(password)
    login_btn = wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, 'a.btn.btn-login')))
    time.sleep(random.random() * 3)
    login_btn.click()


def find_element():
    c_background = wait.until(
        EC.presence_of_element_located((By.CSS_SELECTOR, 'canvas.geetest_canvas_bg.geetest_absolute')))
    c_slice = wait.until(
        EC.presence_of_element_located((By.CSS_SELECTOR, 'canvas.geetest_canvas_slice.geetest_absolute')))
    c_full_bg = wait.until(
        EC.presence_of_element_located((By.CSS_SELECTOR, 'canvas.geetest_canvas_fullbg.geetest_fade.geetest_absolute')))
    hide_element(c_slice)
    save_screenshot(c_background, 'back')
    show_element(c_slice)
    save_screenshot(c_slice, 'slice')
    show_element(c_full_bg)
    save_screenshot(c_full_bg, 'full')


def hide_element(element):
    browser.execute_script("arguments[0].style=arguments[1]", element, "display: none;")


def show_element(element):
    browser.execute_script("arguments[0].style=arguments[1]", element, "display: block;")


def save_screenshot(obj, name):
    try:
        pic_url = browser.save_screenshot('.\\bilibili.png')
        print("%s:截图成功!" % pic_url)
        left = obj.location['x']
        top = obj.location['y']
        right = left + obj.size['width']
        bottom = top + obj.size['height']
        print('图：' + name)
        print('Left %s' % left)
        print('Top %s' % top)
        print('Right %s' % right)
        print('Bottom %s' % bottom)
        print('')
        im = Image.open('.\\bilibili.png')
        im = im.crop((left, top, right, bottom))
        file_name = 'bili_' + name + '.png'
        im.save(file_name)
    except BaseException as msg:
        print("%s:截图失败!" % msg)


def slide():
    distance = get_distance(Image.open('.\\bili_back.png'), Image.open('.\\bili_full.png'))
    print('计算偏移量为：%s Px' % distance)
    trace = get_trace(distance - 5)
    move_to_gap(trace)
    time.sleep(3)


def get_distance(bg_image, fullbg_image):
    distance = 60
    for i in range(distance, fullbg_image.size[0]):
        for j in range(fullbg_image.size[1]):
            if not is_pixel_equal(fullbg_image, bg_image, i, j):
                return i


def is_pixel_equal(bg_image, fullbg_image, x, y):
    bg_pixel = bg_image.load()[x, y]
    fullbg_pixel = fullbg_image.load()[x, y]
    threshold = 60
    if (abs(bg_pixel[0] - fullbg_pixel[0] < threshold) and abs(bg_pixel[1] - fullbg_pixel[1] < threshold) and abs(
            bg_pixel[2] - fullbg_pixel[2] < threshold)):
        return True

    else:
        return False


def get_trace(distance):
    trace = []
    faster_distance = distance * (4 / 5)
    start, v0, t = 0, 0, 0.1
    while start < distance:
        if start < faster_distance:
            a = 20
        else:
            a = -20
        move = v0 * t + 1 / 2 * a * t * t
        v = v0 + a * t
        v0 = v
        start += move
        trace.append(round(move))
    return trace


def move_to_gap(trace):
    slider = wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, 'div.geetest_slider_button')))
    ActionChains(browser).click_and_hold(slider).perform()
    for x in trace:
        ActionChains(browser).move_by_offset(xoffset=x, yoffset=0).perform()
    time.sleep(0.5)
    ActionChains(browser).release().perform()


if __name__ == '__main__':
    init()
    login()
    find_element()
    slide()
```

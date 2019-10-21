---
title: Python3 爬虫实战 — 模拟登陆12306【点触验证码对抗】
tags:
  - 爬虫
  - 12306
categories: 
  - Python3 学习笔记
  - 爬虫实战
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/combat.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---

> 登陆时间：2019-10-21
> 实现难度：★★★☆☆☆
> 请求链接：https://kyfw.12306.cn/otn/resources/login.html
> 实现目标：模拟登陆中国铁路12306，攻克点触验证码
> 涉及知识：点触验证码的攻克、自动化测试工具 Selenium 的使用、对接在线打码平台
> 完整代码：https://github.com/TRHX/Python3-Spider-Practice/tree/master/12306-login
> 其他爬虫实战代码合集（持续更新）：https://github.com/TRHX/Python3-Spider-Practice
> 爬虫实战专栏（持续更新）：https://itrhx.blog.csdn.net/article/category/9351278

---

<!--more-->

# <font color=#ff0000>【1x00】思维导图</font>

<fancybox>
![01](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A57/01.png)
</fancybox>

- 利用自动化测试工具 Selenium 直接模拟人的行为方式来完成验证

- 发送请求，出现验证码后，剪裁并保存验证码图片

- 选择在线打码平台，获取其API，以字节流格式发送图片

- 打码平台人工识别验证码，返回验证码的坐标信息

- 解析返回的坐标信息，模拟点击验证码，完成验证后点击登陆

<fancybox>
![02](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A57/02.png)
</fancybox>

---

# <font color=#ff0000>【2x00】打码平台选择</font>

关于打码平台：在线打码平台全部都是人工在线识别，准确率非常高，原理就是先将验证码图片提交给平台，平台会返回识别结果在图片中的坐标位置，然后我们再解析坐标模拟点击即可，常见的打码平台有超级鹰、云打码等，打码平台是收费的，拿超级鹰来说，1元 = 1000题分，识别一次验证码将花费一定的题分，不同类型验证码需要的题分不同，验证码越复杂所需题分越高，比如 7 位中文汉字需要 70 题分，常见 4 ~ 6 位英文数字只要 10 题分，其他打码平台价格也都差不多，本次实战使用[超级鹰打码平台](http://www.chaojiying.com/)

使用打码平台：在超级鹰打码平台注册账号，官网：http://www.chaojiying.com/ ，充值一块钱得到 1000 题分，在用户中心里面申请一个软件 ID  ，在[价格体系](http://www.chaojiying.com/price.html)里面确定验证码的类型，先观察 12306 官网，发现验证码是要我们点击所有满足条件的图片，一般有 1 至 4 张图片满足要求，由此可确定在超级鹰打码平台的验证码类型为 9004（坐标多选,返回1~4个坐标,如:x1,y1|x2,y2|x3,y3）， 然后在[开发文档](http://www.chaojiying.com/api.html)里面获取其 [Python API](http://www.chaojiying.com/download/Chaojiying_Python.rar)，下载下来以备后用

---

# <font color=#ff0000>【3x00】初始化模块</font>

## <font color=#1BC3FB>【3x01】初始化函数</font>

```python
# 12306账号密码
USERNAME = '155********'
PASSWORD = '***********'

# 超级鹰打码平台账号密码
CHAOJIYING_USERNAME = '*******'
CHAOJIYING_PASSWORD = '*******'

# 超级鹰打码平台软件ID
CHAOJIYING_SOFT_ID = '********'
# 验证码类型
CHAOJIYING_KIND = '9004'


class CrackTouClick():
    def __init__(self):
        self.url = 'https://kyfw.12306.cn/otn/resources/login.html'
        # path是谷歌浏览器驱动的目录，如果已经将目录添加到系统变量，则不用设置此路径
        path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
        chrome_options = Options()
        chrome_options.add_argument('--start-maximized')
        self.browser = webdriver.Chrome(executable_path=path, chrome_options=chrome_options)
        self.wait = WebDriverWait(self.browser, 20)
        self.username = USERNAME
        self.password = PASSWORD
        self.chaojiying = ChaojiyingClient(CHAOJIYING_USERNAME, CHAOJIYING_PASSWORD, CHAOJIYING_SOFT_ID)

```

定义 12306 账号（`USERNAME`）、密码（`PASSWORD`）、超级鹰用户名（`CHAOJIYING_USERNAME`）、超级鹰登录密码（`CHAOJIYING_PASSWORD`）、超级鹰软件 ID（`CHAOJIYING_SOFT_ID`）、验证码类型（`CHAOJIYING_KIND`），登录页面 url ，谷歌浏览器驱动的目录（`path`），浏览器启动参数等，将超级鹰账号密码等相关参数传递给超级鹰 API

---

## <font color=#1BC3FB>【3x02】账号密码输入函数</font>

```python
def get_input_element(self):
    # 登录页面发送请求
    self.browser.get(self.url)
    # 登录页面默认是扫码登录，所以首先要点击账号登录
    login = self.wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, '.login-hd-account')))
    login.click()
    time.sleep(3)
    # 查找到账号密码输入位置的元素
    username = self.wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, 'input#J-userName')))
    password = self.wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, 'input#J-password')))
    # 输入账号密码
    username.send_keys(self.username)
    password.send_keys(self.password)

```

分析页面可知，登陆页面默认出现的是扫描二维码登陆，所以要先点击账号登录，找到该 CSS 元素为 `login-hd-account`，调用 `click()` 方法实现模拟点击，此时出现账号密码输入框，同样找到其 ID 分别为 `J-userName` 和 `J-password`，调用 `send_keys()` 方法输入账号密码

---

# <font color=#ff0000>【4x00】验证码处理模块</font>

```python
def crack(self):
    # 调用账号密码输入函数
    self.get_input_element()
    # 调用验证码图片剪裁函数
    image = self.get_touclick_image()
    bytes_array = BytesIO()
    image.save(bytes_array, format='PNG')
    # 利用超级鹰打码平台的 API PostPic() 方法把图片发送给超级鹰后台，发送的图像是字节流格式，返回的结果是一个JSON
    result = self.chaojiying.PostPic(bytes_array.getvalue(), CHAOJIYING_KIND)
    print(result)
    # 调用验证码坐标解析函数
    locations = self.get_points(result)
    # 调用模拟点击验证码函数
    self.touch_click_words(locations)
    # 调用模拟点击登录函数
    self.login()
    try:
        # 查找是否出现用户的姓名，若出现表示登录成功
        success = self.wait.until(EC.text_to_be_present_in_element((By.CSS_SELECTOR, '.welcome-name'), '谭先生'))
        print(success)
        cc = self.browser.find_element(By.CSS_SELECTOR, '.welcome-name')
        print('用户' + cc.text + '登录成功')
        # 若没有出现表示登录失败，继续重试，超级鹰会返回本次识别的分值
    except TimeoutException:
        self.chaojiying.ReportError(result['pic_id'])
        self.crack()

```

`crack()`  为验证码处理模块的主函数

调用账号密码输入函数 `get_input_element()`，等待账号密码输入完毕

调用验证码图片剪裁函数 `get_touclick_image()`，得到验证码图片

利用超级鹰打码平台的 API `PostPic()` 方法把图片发送给超级鹰后台，发送的图像是字节流格式，返回的结果是一个JSON，如果识别成功，典型的返回结果类似于：

```python
{'err_no': 0, 'err_str': 'OK', 'pic_id': '6002001380949200001', 'pic_str': '132,127|56,77', 'md5': 
'1f8e1d4bef8b11484cb1f1f34299865b'}
```

其中，`pic_str` 就是识别的文字的坐标，是以字符串形式返回的，每个坐标都以 `|` 分隔

调用 `get_points()` 函数解析超级鹰识别结果

调用 `touch_click_words()` 函数对符合要求的图片进行点击

调用模拟点击登录函数 `login()`，点击登陆按钮模拟登陆

使用 `try-except` 语句判断是否出现了用户信息，判断依据是是否有用户姓名的出现，出现的姓名和实际姓名一致则登录成功，如果失败了就重试，超级鹰会返回该分值

---

## <font color=#1BC3FB>【4x01】验证码图片剪裁函数</font>

```python
def get_touclick_image(self, name='12306.png'):
    # 获取验证码的位置
    element = self.wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, '.login-pwd-code')))
    time.sleep(3)
    location = element.location
    size = element.size
    top, bottom, left, right = location['y'], location['y'] + size['height'], location['x'], location['x'] + size['width']
    # 先对整个页面截图
    screenshot = self.browser.get_screenshot_as_png()
    screenshot = Image.open(BytesIO(screenshot))
    # 根据验证码坐标信息，剪裁出验证码图片
    captcha = screenshot.crop((left, top, right, bottom))
    captcha.save(name)
    return captcha

```

首先查找到验证码的坐标信息，先对整个页面截图，然后根据验证码坐标信息，剪裁出验证码图片

location 属性可以返回该图片对象在浏览器中的位置，坐标轴是以屏幕左上角为原点，x 轴向右递增，y 轴向下递增，size 属性可以返回该图片对象的高度和宽度，由此可以得到验证码的位置信息

---

## <font color=#1BC3FB>【4x02】验证码坐标解析函数</font>

```python
def get_points(self, captcha_result):
    # 超级鹰识别结果以字符串形式返回，每个坐标都以|分隔
    groups = captcha_result.get('pic_str').split('|')
    # 将坐标信息变成列表的形式
    locations = [[int(number) for number in group.split(',')] for group in groups]
    return locations

```

`get_points()` 方法将超级鹰的验证码识别结果变成列表的形式

---

## <font color=#1BC3FB>【4x03】模拟点击验证码函数</font>

```python
def touch_click_words(self, locations):
    element = self.wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, '.login-pwd-code')))
    # 循环点击正确验证码的坐标
    for location in locations:
        print(location)
        ActionChains(self.browser).move_to_element_with_offset(element, location[0], location[1]).click().perform()

```

循环提取正确的验证码坐标信息，依次点击验证码

---

# <font color=#ff0000>【5x00】登录模块</font>

```python
def login(self):
    submit = self.wait.until(EC.element_to_be_clickable((By.ID, 'J-login')))
    submit.click()
```

分析页面，找到登陆按钮的 ID 为 `J-login`，调用 `click()` 方法模拟点击按钮实现登录

---

# <font color=#ff0000>【6x00】完整代码</font>

## <font color=#1BC3FB>【6x01】12306.py</font>

```python
# =============================================
# --*-- coding: utf-8 --*--
# @Time    : 2019-10-21
# @Author  : TRHX
# @Blog    : www.itrhx.com
# @CSDN    : https://blog.csdn.net/qq_36759224
# @FileName: 12306.py
# @Software: PyCharm
# =============================================

import time
from io import BytesIO
from PIL import Image
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver import ActionChains
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from chaojiying import ChaojiyingClient
from selenium.common.exceptions import TimeoutException

# 12306账号密码
USERNAME = '155********'
PASSWORD = '***********'

# 超级鹰打码平台账号密码
CHAOJIYING_USERNAME = '********'
CHAOJIYING_PASSWORD = '********'

# 超级鹰打码平台软件ID
CHAOJIYING_SOFT_ID = '******'
# 验证码类型
CHAOJIYING_KIND = '9004'


class CrackTouClick():
    def __init__(self):
        self.url = 'https://kyfw.12306.cn/otn/resources/login.html'
        # path是谷歌浏览器驱动的目录，如果已经将目录添加到系统变量，则不用设置此路径
        path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
        chrome_options = Options()
        chrome_options.add_argument('--start-maximized')
        self.browser = webdriver.Chrome(executable_path=path, chrome_options=chrome_options)
        self.wait = WebDriverWait(self.browser, 20)
        self.username = USERNAME
        self.password = PASSWORD
        self.chaojiying = ChaojiyingClient(CHAOJIYING_USERNAME, CHAOJIYING_PASSWORD, CHAOJIYING_SOFT_ID)

    def crack(self):
        # 调用账号密码输入函数
        self.get_input_element()
        # 调用验证码图片剪裁函数
        image = self.get_touclick_image()
        bytes_array = BytesIO()
        image.save(bytes_array, format='PNG')
        # 利用超级鹰打码平台的 API PostPic() 方法把图片发送给超级鹰后台，发送的图像是字节流格式，返回的结果是一个JSON
        result = self.chaojiying.PostPic(bytes_array.getvalue(), CHAOJIYING_KIND)
        print(result)
        # 调用验证码坐标解析函数
        locations = self.get_points(result)
        # 调用模拟点击验证码函数
        self.touch_click_words(locations)
        # 调用模拟点击登录函数
        self.login()
        try:
            # 查找是否出现用户的姓名，若出现表示登录成功
            success = self.wait.until(EC.text_to_be_present_in_element((By.CSS_SELECTOR, '.welcome-name'), '谭先生'))
            print(success)
            cc = self.browser.find_element(By.CSS_SELECTOR, '.welcome-name')
            print('用户' + cc.text + '登录成功')
            # 若没有出现表示登录失败，继续重试，超级鹰会返回本次识别的分值
        except TimeoutException:
            self.chaojiying.ReportError(result['pic_id'])
            self.crack()

    # 账号密码输入函数
    def get_input_element(self):
        # 登录页面发送请求
        self.browser.get(self.url)
        # 登录页面默认是扫码登录，所以首先要点击账号登录
        login = self.wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, '.login-hd-account')))
        login.click()
        time.sleep(3)
        # 查找到账号密码输入位置的元素
        username = self.wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, 'input#J-userName')))
        password = self.wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, 'input#J-password')))
        # 输入账号密码
        username.send_keys(self.username)
        password.send_keys(self.password)

    # 验证码图片剪裁函数
    def get_touclick_image(self, name='12306.png'):
        # 获取验证码的位置
        element = self.wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, '.login-pwd-code')))
        time.sleep(3)
        location = element.location
        size = element.size
        top, bottom, left, right = location['y'], location['y'] + size['height'], location['x'], location['x'] + size[
            'width']
        # 先对整个页面截图
        screenshot = self.browser.get_screenshot_as_png()
        screenshot = Image.open(BytesIO(screenshot))
        # 根据验证码坐标信息，剪裁出验证码图片
        captcha = screenshot.crop((left, top, right, bottom))
        captcha.save(name)
        return captcha

    # 验证码坐标解析函数，分析超级鹰返回的坐标
    def get_points(self, captcha_result):
        # 超级鹰识别结果以字符串形式返回，每个坐标都以|分隔
        groups = captcha_result.get('pic_str').split('|')
        # 将坐标信息变成列表的形式
        locations = [[int(number) for number in group.split(',')] for group in groups]
        return locations

    # 模拟点击验证码函数
    def touch_click_words(self, locations):
        element = self.wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, '.login-pwd-code')))
        # 循环点击正确验证码的坐标
        for location in locations:
            print(location)
            ActionChains(self.browser).move_to_element_with_offset(element, location[0], location[1]).click().perform()

    # 模拟点击登录函数
    def login(self):
        submit = self.wait.until(EC.element_to_be_clickable((By.ID, 'J-login')))
        submit.click()


if __name__ == '__main__':
    crack = CrackTouClick()
    crack.crack()

```

---

## <font color=#1BC3FB>【6x02】chaojiying.py</font>

```python
import requests
from hashlib import md5


class ChaojiyingClient(object):
    def __init__(self, username, password, soft_id):
        self.username = username
        password = password.encode('utf8')
        self.password = md5(password).hexdigest()
        self.soft_id = soft_id
        self.base_params = {
            'user': self.username,
            'pass2': self.password,
            'softid': self.soft_id,
        }
        self.headers = {
            'Connection': 'Keep-Alive',
            'User-Agent': 'Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 5.1; Trident/4.0)',
        }

    def PostPic(self, im, codetype):
        """
        im: 图片字节
        codetype: 题目类型 参考 http://www.chaojiying.com/price.html
        """
        params = {
            'codetype': codetype,
        }
        params.update(self.base_params)
        files = {'userfile': ('ccc.jpg', im)}
        r = requests.post('http://upload.chaojiying.net/Upload/Processing.php', data=params, files=files, headers=self.headers)
        return r.json()

    def ReportError(self, im_id):
        """
        im_id:报错题目的图片ID
        """
        params = {
            'id': im_id,
        }
        params.update(self.base_params)
        r = requests.post('http://upload.chaojiying.net/Upload/ReportError.php', data=params, headers=self.headers)
        return r.json()

```

---

# <font color=#ff0000>【7x00】效果实现动图</font>

最终实现效果图：（关键信息已经过打码处理）

<fancybox>
![02](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A57/12306.gif)
</fancybox>

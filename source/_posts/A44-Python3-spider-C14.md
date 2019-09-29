---
title: Python3 爬虫学习笔记 C14
tags:
  - 爬虫
  - 点触验证码
categories: 
  - Python3 学习笔记
  - 爬虫学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/spider.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 爬虫学习笔记第十四章 —— 【验证码对抗系列 — 点触验证码】</font></center>

<!--more-->

---

# <font color=#ff0000>【14.1】关于点触验证码</font>

点触验证码是由杭州微触科技有限公司研发的新一代的互联网验证码，使用点击的形式完成验证，采用专利的印刷算法以及加密算法，保证每次请求到的验证图具有极高的安全性，常见的点触验证码如下：

<fancybox>
![01](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A44/01.png)
</fancybox>

---

# <font color=#ff0000>【14.2】点触验证码攻克思路</font>

点触验证码相对其他类型验证码比较复杂，如果依靠 OCR 图像识别点触验证码，则识别难度非常大，此时就要用到互联网的验证码服务平台，这些服务平台全部都是人工在线识别，准确率非常高，原理就是先将验证码图片提交给平台，平台会返回识别结果在图片中的坐标位置，然后我们再解析坐标模拟点击即可，常见的打码平台有超级鹰、云打码等，打码平台是收费的，拿超级鹰来说，1元 = 1000题分，识别一次验证码将花费一定的题分，不同类型验证码需要的题分不同，验证码越复杂所需题分越高，比如 7 位中文汉字需要 70 题分，常见 4 ~ 6 位英文数字只要 10 题分，其他打码平台价格也都差不多

以下以[超级鹰打码平台](http://www.chaojiying.com/)和[中国铁路12306官网](https://www.12306.cn/)来做练习

---

# <font color=#ff0000>【14.3】模拟登录 12306 — 总体思路</font>

首先在超级鹰打码平台注册账号并申请一个软件 ID，官网：http://www.chaojiying.com/ ，先充值一块钱得到 1000 题分，观察 12306 官网，发现验证码是要我们点击所有满足条件的图片，一般有 1~4 张图片满足要求，由此可确定在超级鹰打码平台的验证码类型为 9004（坐标多选,返回1~4个坐标,如:x1,y1|x2,y2|x3,y3）， 获取其 Python API：http://www.chaojiying.com/download/Chaojiying_Python.rar ，然后用 Selenium 模拟登陆，获取到验证码，并将验证码发送给超级鹰后台，返回识别图片的坐标，最后模拟点击即可，整个过程的实现由主程序 `12306.py` 和超级鹰 API `chaojiying.py` 组成

整个程序包含的函数：

```python
def __init__(): 初始化 WebDriver、Chaojiying 对象等
def crack(): 破解入口、获取、识别验证码、模拟登录
def open(): 账号密码输入
def get_screenshot(): 整个页面截图
def get_touclick_element(): 获取验证码位置
def get_position(): 获取验证码坐标
def get_touclick_image(): 剪裁验证码部分
def get_points(self, captcha_result): 分析超级鹰返回的坐标
def touch_click_words(self, locations): 模拟点击符合要求的图片
def login(self): 点击登陆按钮，完成模拟登录
```

整个程序用到的库：

```python
import time
from io import BytesIO
from PIL import Image
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver import ActionChains
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from chaojiying import Chaojiying
from selenium.common.exceptions import TimeoutException
```

---

# <font color=#ff0000>【14.4】主函数</font>

```python
if __name__ == '__main__':
    crack = CrackTouClick()
    crack.crack()
```

---

# <font color=#ff0000>【14.5】初始化函数</font>

```python
USERNAME = '155********'
PASSWORD = '***********'

CHAOJIYING_USERNAME = '*******'
CHAOJIYING_PASSWORD = '*******'
CHAOJIYING_SOFT_ID = '********'
CHAOJIYING_KIND = '9004'


class CrackTouClick():
    def __init__(self):
        self.url = 'https://kyfw.12306.cn/otn/resources/login.html'
        path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
        chrome_options = Options()
        chrome_options.add_argument('--start-maximized')
        self.browser = webdriver.Chrome(executable_path=path, chrome_options=chrome_options)
        self.wait = WebDriverWait(self.browser, 20)
        self.email = USERNAME
        self.password = PASSWORD
        self.chaojiying = Chaojiying_Client(CHAOJIYING_USERNAME, CHAOJIYING_PASSWORD, CHAOJIYING_SOFT_ID)
```

定义 12306 账号（`USERNAME`）、密码（`PASSWORD`）、超级鹰用户名（`CHAOJIYING_USERNAME`）、超级鹰登录密码（`CHAOJIYING_PASSWORD`）、超级鹰软件 ID（`CHAOJIYING_SOFT_ID`）、验证码类型（`CHAOJIYING_KIND`），登录链接 url：https://kyfw.12306.cn/otn/resources/login.html ，谷歌浏览器驱动的目录（`path`），浏览器启动参数，并将相关参数传递给超级鹰 API

---

# <font color=#ff0000>【14.6】破解入口函数</font>

```python
    def crack(self):
        self.open()
        image = self.get_touclick_image()
        bytes_array = BytesIO()
        image.save(bytes_array, format='PNG')
        result = self.chaojiying.PostPic(bytes_array.getvalue(), CHAOJIYING_KIND)
        print(result)
        locations = self.get_points(result)
        self.touch_click_words(locations)
        self.login()
        try:
            success = self.wait.until(EC.text_to_be_present_in_element((By.CSS_SELECTOR, '.welcome-name'), '用户姓名'))
            print(success)
            cc = self.browser.find_element(By.CSS_SELECTOR, '.welcome-name')
            print(cc.text)
        except TimeoutException:
            self.chaojiying.ReportError(result['pic_id'])
            self.crack()
```

调用 `open()` 函数输入账号密码

调用 `get_touclick_image()` 函数获取验证码图片

利用超级鹰 Python API `PostPic()` 方法即可把图片发送给超级鹰后台，发送的图像是字节流格式，返回的结果是一个 JSON，如果识别成功，典型的返回结果类似于：`{'err_no': 0, 'err_str': 'OK', 'pic_id': '6002001380949200001', 'pic_str': '132,127|56,77', 'md5': '1f8e1d4bef8b11484cb1f1f34299865b'}`，其中，pic_str 就是识别的文字的坐标，是以字符串形式返回的，每个坐标都以 | 分隔

调用 `get_points()` 函数解析超级鹰识别结果

调用 `touch_click_words()` 函数对符合要求的图片进行点击，然后点击登陆按钮模拟登陆

使用 `try-except` 语句判断是否出现了用户信息，判断依据是是否有用户姓名的出现，出现的姓名和实际姓名一致则登录成功，如果失败了就重试，超级鹰会返回该分值

---

# <font color=#ff0000>【14.7】账号密码输入函数</font>

```python
    def open(self):
        self.browser.get(self.url)
        login = self.wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, '.login-hd-account')))
        login.click()
        time.sleep(3)
        username = self.wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, 'input#J-userName')))
        password = self.wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, 'input#J-password')))
        username.send_keys(self.email)
        password.send_keys(self.password)
```

分析页面可知，登陆页面 URL 为：https://kyfw.12306.cn/otn/resources/login.html ，该页面默认出现的是扫描二维码登陆，所以要先点击账号登录，找到该 CSS 元素为 `login-hd-account`，调用 `click()` 方法实现模拟点击，此时出现账号密码输入框，同样找到其 ID 分别为 `J-userName` 和 `J-password`，调用 `send_keys()` 方法输入账号密码

---

# <font color=#ff0000>【14.8】页面截图函数</font>

```python
    def get_screenshot(self):
        screenshot = self.browser.get_screenshot_as_png()
        screenshot = Image.open(BytesIO(screenshot))
        return screenshot
```

对整个页面进行截图

---

# <font color=#ff0000>【14.9】验证码元素查找函数</font>

```python
    def get_touclick_element(self):
        element = self.wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, '.login-pwd-code')))
        return element
```

同样分析页面，验证码所在位置的 CSS 为 `login-pwd-code`

---

# <font color=#ff0000>【14.10】获取验证码坐标函数</font>

```python
    def get_position(self):
        element = self.get_touclick_element()
        time.sleep(3)
        location = element.location
        size = element.size
        top, bottom, left, right = location['y'], location['y'] + size['height'], location['x'], location['x'] + size['width']
        return (top, bottom, left, right)
```

location 属性可以返回该图片对象在浏览器中的位置，坐标轴是以屏幕左上角为原点，x 轴向右递增，y 轴向下递增，size 属性可以返回该图片对象的高度和宽度，由此可以得到验证码的位置信息

---

# <font color=#ff0000>【14.11】验证码剪裁函数</font>

```python
    def get_touclick_image(self, name='12306.png'):
        top, bottom, left, right = self.get_position()
        screenshot = self.get_screenshot()
        captcha = screenshot.crop((left, top, right, bottom))
        captcha.save(name)
        return captcha
```

根据验证码的坐标信息，对页面截图进行剪裁，得到验证码部分，将其保存为 12306.png

---

# <font color=#ff0000>【14.12】验证码坐标解析函数</font>

```python
    def get_points(self, captcha_result):
        groups = captcha_result.get('pic_str').split('|')
        locations = [[int(number) for number in group.split(',')] for group in groups]
        return locations
```

`get_points()` 方法将超级鹰的验证码识别结果变成列表的形式

---

# <font color=#ff0000>【14.13】验证码模拟点击函数</font>

```python
    def touch_click_words(self, locations):
        for location in locations:
            print(location)
            ActionChains(self.browser).move_to_element_with_offset(self.get_touclick_element(), location[0]/1.25, location[1]/1.25).click().perform()
```

`touch_click_words()` 方法通过调用 `move_to_element_with_offset()` 方法依次传入解析后的坐标，点击即可

---

# <font color=#ff0000>【14.14】模拟点击登陆函数</font>

```python
    def login(self):
        submit = self.wait.until(EC.element_to_be_clickable((By.ID, 'J-login')))
        submit.click()
```

分析页面，找到登陆按钮的 ID 为 `J-login`，调用 `click()` 方法模拟点击按钮实现登录

---

# <font color=#ff0000>【14.15】效果实现动图</font>

最终实现效果图：（关键信息已经过打码处理）

<fancybox>
![02](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A44/12306.gif)
</fancybox>

---

# <font color=#ff0000>【14.16】完整代码</font>

`12306.py`

```python
import time
from io import BytesIO
from PIL import Image
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver import ActionChains
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from chaojiying import Chaojiying_Client
from selenium.common.exceptions import TimeoutException

USERNAME = '155********'
PASSWORD = '***********'

CHAOJIYING_USERNAME = '***********'
CHAOJIYING_PASSWORD = '***********'
CHAOJIYING_SOFT_ID = '******'
CHAOJIYING_KIND = '9004'


class CrackTouClick():
    def __init__(self):     #登陆
        self.url = 'https://kyfw.12306.cn/otn/resources/login.html'
        path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
        chrome_options = Options()
        chrome_options.add_argument('--start-maximized')
        self.browser = webdriver.Chrome(executable_path=path, chrome_options=chrome_options)
        self.wait = WebDriverWait(self.browser, 20)
        self.email = USERNAME
        self.password = PASSWORD
        self.chaojiying = Chaojiying_Client(CHAOJIYING_USERNAME, CHAOJIYING_PASSWORD, CHAOJIYING_SOFT_ID)

    def crack(self):
        self.open()
        image = self.get_touclick_image()
        bytes_array = BytesIO()
        image.save(bytes_array, format='PNG')
        result = self.chaojiying.PostPic(bytes_array.getvalue(), CHAOJIYING_KIND)
        print(result)
        locations = self.get_points(result)
        self.touch_click_words(locations)
        self.login()
        try:
            success = self.wait.until(EC.text_to_be_present_in_element((By.CSS_SELECTOR, '.welcome-name'), '谭仁侯'))
            print(success)
            cc = self.browser.find_element(By.CSS_SELECTOR, '.welcome-name')
            print(cc.text)

        except TimeoutException:
            self.chaojiying.ReportError(result['pic_id'])
            self.crack()

    def open(self):
        self.browser.get(self.url)
        login = self.wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, '.login-hd-account')))
        login.click()
        time.sleep(3)
        username = self.wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, 'input#J-userName')))
        password = self.wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, 'input#J-password')))
        username.send_keys(self.email)
        password.send_keys(self.password)

    def get_screenshot(self):
        screenshot = self.browser.get_screenshot_as_png()
        screenshot = Image.open(BytesIO(screenshot))
        return screenshot

    def get_touclick_element(self):
        element = self.wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, '.login-pwd-code')))
        return element

    def get_position(self):
        element = self.get_touclick_element()
        time.sleep(3)
        location = element.location
        size = element.size
        top, bottom, left, right = location['y'], location['y'] + size['height'], location['x'], location['x'] + size['width']
        return (top, bottom, left, right)

    def get_touclick_image(self, name='12306.png'):
        top, bottom, left, right = self.get_position()
        screenshot = self.get_screenshot()
        captcha = screenshot.crop((left, top, right, bottom))
        captcha.save(name)
        return captcha

    def get_points(self, captcha_result):
        groups = captcha_result.get('pic_str').split('|')
        locations = [[int(number) for number in group.split(',')] for group in groups]
        return locations

    def touch_click_words(self, locations):
        for location in locations:
            print(location)
            ActionChains(self.browser).move_to_element_with_offset(self.get_touclick_element(), location[0]/1.25, location[1]/1.25).click().perform()

    def login(self):
        submit = self.wait.until(EC.element_to_be_clickable((By.ID, 'J-login')))
        submit.click()


if __name__ == '__main__':
    crack = CrackTouClick()
    crack.crack()
```

`chaojiying.py`

```python
import requests
from hashlib import md5


class Chaojiying_Client(object):

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

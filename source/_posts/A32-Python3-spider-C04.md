---
title: Python3 爬虫学习笔记 C04
tags:
  - 爬虫
  - Selenium
categories: 
  - Python3 学习笔记
  - 爬虫学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/spider.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 爬虫学习笔记第四章 —— 【自动化测试工具 Selenium】</font></center>

<!--more-->

Selenium 是一个用于 Web 应用程序测试的工具。Selenium 测试直接运行在浏览器中，就像真正的用户在操作一样。支持的浏览器包括IE（7, 8, 9, 10, 11），Mozilla Firefox，Safari，Google Chrome，Opera等。利用它可以驱动浏览器执行特定的动作，如点击、下拉等操作，同时还可以获取浏览器当前呈现的页面的源代码，做到可见即可爬。对于一些 JavaScript 动态渲染的页面来说，此种抓取方式非常有效。<font color=#FF0000>本文重点以 Selenium 使用谷歌浏览器的 Webdriver 为例。</font>

# <font color=#FF0000> 【4.1】下载驱动</font>
使用 Selenium 操作不同浏览器，需要不同浏览器相应的驱动支持：

|    浏览器     | 驱动名称     | 下载地址  |  备注 |
| ------------- | ------------ | ----------------------------- | -------------------|
| 谷歌浏览器 | chromedriver | [点击进入下载页面](http://chromedriver.storage.googleapis.com/index.html) |   需要根据自己浏览器的版本下载不同版本的驱动 |
| 火狐浏览器 | geckodriver  | [点击进入下载页面](https://github.com/mozilla/geckodriver/releases/)     | 需要根据自己的操作系统下载对应的驱动 |
|   IE    |   IEDriverServer  |      [点击进入下载页面](http://selenium-release.storage.googleapis.com/index.html)   | 根据自己 selenium 版本和系统版本下载对应版本的驱动， selenium 版本可以在cmd中输入`pip show selenium`查看 |

# <font color=#FF0000> 【4.2】声明浏览器对象</font>
不同浏览器的对象声明方法：
```python
from selenium import webdriver

browser = webdriver.Chrome()  # 谷歌浏览器
browser = webdriver.Firefox()  # 火狐浏览器
browser = webdriver.Edge()  # Edge
browser = webdriver.PhantomJS()  # PhantomJS无界面浏览器
browser = webdriver.Safari()  # Safari浏览器
```

# <font color=#FF0000> 【4.3】访问页面</font>
```python
from selenium import webdriver

path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
browser = webdriver.Chrome(executable_path=path)
browser.get('https://www.itrhx.com')
print(browser.page_source)
browser.close()
```
运行代码就会自动打开谷歌浏览器，实现了用 get() 方法访问 www.itrhx.com ，path 里面的内容是谷歌浏览器驱动的目录， r 表示不转义，使用真实字符。`print(browser.page_source)` 表示打印页面源代码

# <font color=#FF0000> 【4.4】启动参数</font>
Chrome Options 是一个 Chrome 的参数对象，在此对象中使用 add_argument() 方法可以添加启动参数，添加完毕后可以在初始化 Webdriver 对象时将此 Options 对象传入，则可以实现以特定参数启动Chrome。
示例：
```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'

# 实例化一个启动参数对象
chrome_options = Options()
# 添加启动参数
chrome_options.add_argument('--window-size=1366,768')
# 将参数对象传入Chrome，则启动了一个设置了窗口大小的Chrome
browser = webdriver.Chrome(executable_path=path, chrome_options=chrome_options)
browser.get('http://www.itrhx.com')
```
这样就启动了一个1366x768分辨率的浏览器
常见的启动参数：

| 启动参数               | 作用                                     |
| :----------------------: | :----------------------------------------: |
| --user-agent=""        | 设置请求头的 User-Agent                  |
| --window-size=xxx, xxx | 设置浏览器分辨率                         |
| --headless             | 无界面运行                               |
| --start-maximized      | 最大化运行                               |
| --incognito            | 隐身模式                                 |
| --disable-javascript   | 禁用javascript                           |
| --disable-infobars     | 禁用“浏览器正在被自动化程序控制”的提示 |
所有的启动参数：https://peter.sh/experiments/chromium-command-line-switches/

# <font color=#FF0000> 【4.5】查找节点</font>
Selenium 可以驱动浏览器完成各种操作，比如填充表单、模拟点击等。要完成这些操作，实现要知道在哪里点击，哪里填充，这就是 Selenium 节点查找

## <font color=#FF0000> 【4.5.1】查找单个节点</font>
所有获取单个节点的方法：
- <font color=#FF0000>find_element_by_id  【通过元素的 id 来选择】</font>
例：`<div id='bdy-inner'>test</div>`，查找：`driver.find_element_by_id('bdy-inner')`

- <font color=#FF0000>find_element_by_name  【通过元素的 name 来选择】</font>
例：`<input name="username" type="text" />`，查找：`driver.find_element_by_name('password')`

- <font color=#FF0000>find_element_by_xpath  【通过 xpath 选择】</font>
例：`<form id="loginForm">`，查找：`driver.find_element_by_xpath("//form[@id='loginForm']")`

- <font color=#FF0000>find_element_by_link_text  【通过链接地址选择】</font>
例：`<a href="continue.html">continue</a>`，查询：`driver.find_element_by_link_text('continue')`

- <font color=#FF0000>find_element_by_partial_link_text  【通过链接的部分地址选择】</font>
例：`<a href="continue.html">continue</a>`，查询：`driver.find_element_by_link_text('cont')`

- <font color=#FF0000>find_element_by_tag_name  【通过元素的名称选择】</font>
例：`<h1>welcome<h1>`，查询：`driver.find_element_by_tag_name('h1')`

- <font color=#FF0000>find_element_by_class_name  【通过元素的 class 选择】</font>
例：`<p class="content">welcome to TRHX'S BLOG!</p>`，查询：`driver.find_element_by_class_name('content')`

- <font color=#FF0000>find_element_by_css_selector  【通过元素的 class 选择】</font>
例：`<div class='bdy-inner'>test</div>`，查询：`driver.find_element_by_css_selector('div.bdy-inner')`

- <font color=#FF0000>find_element()  【通用方法，需要传递两个参数：查找方式 By 和值】</font>
例：`driver.find_element_by_id('inner')` 等价于 `find_element(By.ID, inner)`，使用时需要`from selenium.webdriver.common.by import By`

示例：
```python
from selenium import webdriver

path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
browser = webdriver.Chrome(executable_path=path)
browser.get('https://www.itrhx.com')
blog_title = browser.find_elements_by_class_name(('title'))
print(blog_title[0].text)
browser.close()
```
输出结果：
```python
TRHX'S BLOG
```

## <font color=#FF0000> 【4.5.2】查找多个节点</font>
所有获取多个节点的方法：（与查找单个节点的区别是 element 多加了个 s）
- find_elements_by_id
- find_elements_by_name
- find_elements_by_xpath
- find_elements_by_link_text
- find_elements_by_partial_link_text
- find_elements_by_tag_name
- find_elements_by_class_name
- find_elements_by_css_selector
- find_elements()

示例：
```python
from selenium import webdriver
from selenium.webdriver.common.by import By

path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
browser = webdriver.Chrome(executable_path=path)
browser.get('https://www.itrhx.com')
article_title = browser.find_elements(By.XPATH, "//h2[@class='title']")
print(article_title)
browser.close()
```

# <font color=#FF0000> 【4.6】节点交互</font>
Selenium 可以驱动浏览器来执行一些操作，也就是说可以让浏览器模拟执行一些动作。称为节点交互，比较常见的用法有：
 - send_keys：模拟按键输入
 - clear：清除元素的内容
 - click：单击元素
 - submit：提交表单

示例：
```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
browser = webdriver.Chrome(executable_path=path)
browser.get('https://www.itrhx.com')
search = browser.find_element_by_xpath('//div[@class="cover-wrapper"]/cover/div/form/input')
search.send_keys("Python")
search.send_keys(Keys.ENTER)
```
此处模拟了键盘，需要导入键盘类 Keys()，send_keys(Keys.ENTER)表示模拟回车键，程序首先打开 www.itrhx.com ，也就是我的博客，然后通过 xpath 找到搜索框，输入 Python 并回车，等待结果显示出来
更多节点交互动作：https://selenium-python.readthedocs.io/api.html#module-selenium.webdriver.remote.webelement

# <font color=#FF0000> 【4.7】动作链</font>
Selenium 还有另外一些操作，它们没有特定的执行对象，比如鼠标拖曳、键盘按键等，这些动作用另一种方式来执行，那就是动作链。以一个拖曳实例为例：http://www.runoob.com/try/try.php?filename=jqueryui-api-droppable
```python
from selenium import webdriver
from selenium.webdriver import ActionChains

path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
browser = webdriver.Chrome(executable_path=path)
url = 'http://www.runoob.com/try/try.php?filename=jqueryui-api-droppable'
browser.get(url)
browser.switch_to.frame('iframeResult')
source = browser.find_element_by_css_selector('#draggable')
target = browser.find_element_by_css_selector('#droppable')
actions = ActionChains(browser)
actions.drag_and_drop(source, target)
actions.perform()
```
依次选中要拖曳的节点和拖曳到的目标节点，接着声明 ActionChains 对象并将其赋值为 actions 变量，然后通过调用 actions 变量的 drag_and_drop() 方法，再调用 perform() 方法执行动作，此时就完成了拖曳操作，更多动作链操作：https://selenium-python.readthedocs.io/api.html#module-selenium.webdriver.common.action_chains

# <font color=#FF0000> 【4.8】执行 JavaScript</font>
Selenium API 并没有提供执行 JavaScript 的方法，但是实际上是可以实现的。比如，下拉进度条，它可以直接模拟运行 JavaScript，此时使用 execute_script() 方法即可实现
示例：
```python
from selenium import webdriver

path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
browser = webdriver.Chrome(executable_path=path)
browser.get('https://www.itrhx.com')
browser.execute_script('window.scrollTo(0, document.body.scrollHeight)')
browser.execute_script('alert("已到达最底端！")')
```
以上代码实现了利用 execute_script() 方法将进度条下拉到最底部，然后弹出 alert 提示框。


# <font color=#FF0000> 【4.9】禁用加载</font>
使用Selenium 时，限制图片和 Javascript 执行，从而提高网页加载速度。
```python
from selenium import webdriver

path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'

options = webdriver.ChromeOptions()
prefs = {
    'profile.default_content_setting_values': {
        'images': 2,
        'notifications' : 2,  # 禁用弹窗
        'javascript': 2  # 2即为禁用的意思
    }
}
options.add_experimental_option('prefs', prefs)
browser = webdriver.Chrome(executable_path=path, chrome_options=options)
browser.get('http://www.itrhx.com')
```

# <font color=#FF0000> 【4.10】获取节点信息</font>
通过 page_source 属性可以获取网页的源代码，然后可以使用解析库（如正则表达式、Beautiful Soup等）来提取相关信息，Selenium 已经提供了选择节点的方法，返回的是 WebElement 类型，它也有相关的方法和属性来直接提取节点信息，如属性、文本等。就不需要再次使用解析库来提取信息了
# <font color=#FF0000> 【4.10.1】获取属性</font>
使用 get_attribute() 方法来获取节点的属性：
```python
from selenium import webdriver

path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
browser = webdriver.Chrome(executable_path=path)
url = 'http://www.itrhx.com'
browser.get(url)
meta = browser.find_element_by_id('header-meta')
print(meta)
print(meta.get_attribute('class'))
```
输出结果：
```python
<selenium.webdriver.remote.webelement.WebElement (session="d03cdaa497441d2e2a5161139b4a7ea5", element="83f8fff9-60d7-4e9a-ade3-a8e97c9f0844")>
meta
```

# <font color=#FF0000> 【4.10.2】获取文本值</font>
每个 WebElement 节点都有 text 属性，直接调用这个属性就可以得到节点内部的文本信息，相当于 Beautiful Soup 的 get_text() 方法、pyquery 的 text() 方法
示例：
```python
from selenium import webdriver

path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
browser = webdriver.Chrome(executable_path=path)
url = 'http://www.itrhx.com'
browser.get(url)
footer_info = browser.find_element_by_id('footer')
print(footer_info.text)
```
输出结果：
```python
Copyright 2018-2019 TRHX'BLOG   |   鄂ICP备19003281号-4  |   本站已勉强存活了 376 天 20 小时 57 分 52 秒   |   站点地图  |   站长统计

PoweredHexo HostedGitHub DNRAliyun CDNjsDelivr ThemeMaterial X BY-NC-SA 4.0 Link996.ICU UV4898 PV22066 WordCount54.9k
```

# <font color=#FF0000> 【4.10.3】获取 ID、位置、标签名、大小</font>
其他属性，比如 id 属性可以获取节点 id，location 属性可以获取该节点在页面中的相对位置，tag_name 属性可以获取标签名称，size 属性可以获取节点的大小等
示例：
```python
from selenium import webdriver

path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
browser = webdriver.Chrome(executable_path=path)
url = 'http://www.itrhx.com'
browser.get(url)
readmore = browser.find_element_by_class_name('readmore')
print(readmore.id)
print(readmore.location)
print(readmore.tag_name)
print(readmore.size)
```
输出结果：
```python
7df561d3-7ea4-4b90-96aa-64044060bb47
{'x': 50, 'y': 1063}
div
{'height': 39, 'width': 465}
```

# <font color=#FF0000> 【4.11】延时等待</font>
在 Selenium 中，get() 方法会在网页框架加载结束后结束执行，某些页面有额外的 Ajax 请求，若此时立即获取 page_source，可能并不是浏览器完全加载完成的页面，这里需要延时等待一定时间，确保节点已经加载出来

## <font color=#FF0000> 【4.11.1】隐式等待</font>
当查找节点的时候，节点并没有立即出现，隐式等待将等待一段时间再查找该节点，使用 `implicitly_wait()` 方法可以实现隐式等待
```python
from selenium import webdriver

path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
browser = webdriver.Chrome(executable_path=path)
browser.implicitly_wait(10)
browser.get('https://www.itrhx.com')
readmore = browser.find_element_by_class_name('readmore')
print(readmore)
```

## <font color=#FF0000> 【4.11.2】显式等待</font>
指定要查找的节点，然后指定一个最长等待时间。如果在规定时间内加载出来了这个节点，就立即返回查找的节点，果到了规定时间依然没有加载出该节点，则抛出超时异常
```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
browser = webdriver.Chrome(executable_path=path)
browser.implicitly_wait(10)
browser.get('https://www.itrhx.com')
wait = WebDriverWait(browser, 10)
footer_info = wait.until(EC.presence_of_element_located((By.ID, 'footer')))
print(footer_info)
```
引入 WebDriverWait 对象，指定最长等待时间，调用它的 until() 方法，传入要等待条件 expected_conditions。比如，这里传入了 presence_of_element_located 这个条件，代表节点出现的意思，其参数是节点的定位元组，也就是 ID 为 footer 的节点。

这样可以做到的效果就是，在 10 秒内如果 ID 为 footer 的节点成功加载出来，就返回该节点；如果超过 10 秒还没有加载出来，就抛出异常。

加载成功时输出结果：
```python
<selenium.webdriver.remote.webelement.WebElement (session="4ca7015891fded627ab680d9462e9361", element="3a80235c-9824-420b-b827-662638422765")>
```
加载失败时输出结果：
```python
TimeoutException Traceback (most recent call last)
<ipython-input-4-f3d73973b223> in <module>()
      7 browser.get('https://www.itrhx.com')
      8 wait = WebDriverWait(browser, 10)
----> 9 input = wait.until(EC.presence_of_element_located((By.ID, 'footer')))
```

# <font color=#FF0000> 【4.12】Cookies</font>
使用 Selenium，可以方便地对 Cookies 进行获取、添加、删除等操作：
```python
from selenium import webdriver

path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
browser = webdriver.Chrome(executable_path=path)
browser.get('https://www.zhihu.com/explore')
print(browser.get_cookies())
browser.add_cookie({'name': 'TRHX', 'domain': 'www.zhihu.com', 'value': 'germey'})
print(browser.get_cookies())
browser.delete_all_cookies()
print(browser.get_cookies())
```
访问知乎，加载完成后，浏览器已经生成了 Cookies。调用 get_cookies() 方法获取所有的 Cookies。然后再添加一个 Cookie，传入一个字典，有 name、domain 和 value 等内容。接下来，再次获取所有的 Cookies。可以发现，结果就多了这一项新加的 Cookie。最后，调用 delete_all_cookies() 方法删除所有的 Cookies。再重新获取，发现结果就为空了
输出结果：
```python
[{'domain': 'zhihu.com', 'expiry': 1661065738.754333, 'httpOnly': False, 'name': 'd_c0', 'path': '/', 'secure': False, 'value': '"AODi_Lod7g-PTrrXUgXb1N4MkbStCrbNlD4=|1566457741"'}, {'domain': 'zhihu.com', 'httpOnly': False, 'name': '_xsrf', 'path': '/', 'secure': False, 'value': 'aba68431-9daf-4b62-a67a-023c1a24f0e8'}, {'domain': 'zhihu.com', 'expiry': 1629529738.75427, 'httpOnly': False, 'name': '_zap', 'path': '/', 'secure': False, 'value': 'b6f63cfc-a525-4ae6-a7bf-6384bd1e0548'}, {'domain': 'www.zhihu.com', 'expiry': 1566458637.754178, 'httpOnly': False, 'name': 'tgw_l7_route', 'path': '/', 'secure': False, 'value': '116a747939468d99065d12a386ab1c5f'}]
[{'domain': 'www.zhihu.com', 'httpOnly': False, 'name': 'TRHX', 'path': '/', 'secure': True, 'value': 'germey'}, {'domain': 'zhihu.com', 'expiry': 1661065738.754333, 'httpOnly': False, 'name': 'd_c0', 'path': '/', 'secure': False, 'value': '"AODi_Lod7g-PTrrXUgXb1N4MkbStCrbNlD4=|1566457741"'}, {'domain': 'zhihu.com', 'httpOnly': False, 'name': '_xsrf', 'path': '/', 'secure': False, 'value': 'aba68431-9daf-4b62-a67a-023c1a24f0e8'}, {'domain': 'zhihu.com', 'expiry': 1629529738.75427, 'httpOnly': False, 'name': '_zap', 'path': '/', 'secure': False, 'value': 'b6f63cfc-a525-4ae6-a7bf-6384bd1e0548'}, {'domain': 'www.zhihu.com', 'expiry': 1566458637.754178, 'httpOnly': False, 'name': 'tgw_l7_route', 'path': '/', 'secure': False, 'value': '116a747939468d99065d12a386ab1c5f'}]
[{'domain': 'zhihu.com', 'expiry': 1644217741.489889, 'httpOnly': False, 'name': '_xsrf', 'path': '/', 'secure': False, 'value': 'WNOjpDbNmz36B4nG1lzSAuPdTyORMX6J'}]
```

# <font color=#FF0000> 【4.13】前进与后退</font>
使用 back() 方法后退，使用 forward() 方法前进，与浏览器的前进后退一样
示例：
```python
from selenium import webdriver
import time

path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
browser = webdriver.Chrome(executable_path=path)
browser.get('https://www.itrhx.com/')
browser.get('https://www.baidu.com/')
browser.get('https://www.zhihu.com/')
browser.back()
time.sleep(1)
browser.forward()
browser.close()
```

# <font color=#FF0000> 【4.14】选项卡</font>
和浏览器一样，在 Selenium 中也可以新建一个选项卡
```python
from selenium import webdriver
import time

path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
browser = webdriver.Chrome(executable_path=path)
browser.get('https://www.itrhx.com')
browser.execute_script('window.open()')
print(browser.window_handles)
browser.switch_to.window(browser.window_handles[1])
browser.get('https://www.baidu.com')
time.sleep(1)
browser.switch_to.window(browser.window_handles[0])
browser.get('https://www.zhihu.com')
```
首先访问我的博客，然后调用了 `execute_script()` 方法，传入 `window.open()` 这个 JavaScript 语句开启一个新的选项卡。再调用 `window_handles` 属性获取当前开启的所有选项卡，返回的是选项卡的代号列表。调用 `switch_to_window()` 方法来切换选项卡，其中参数是选项卡的代号。
输出的选项卡代号列表：
```python
['CDwindow-C9CADF1ED28CE44970655238552A8DCF', 'CDwindow-538D7F81E467746B7BB2D9D82E2D036E']
```
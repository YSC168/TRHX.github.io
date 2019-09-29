---
title: Python3 爬虫学习笔记 C05
tags:
  - 爬虫
  - Selenium
  - 无界面浏览器
categories: 
  - Python3 学习笔记
  - 爬虫学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/spider.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 爬虫学习笔记第五章 —— 【Selenium + 无界面浏览器】</font></center>

<!--more-->

# <font color=#ff0000>【5.1】关于无界面浏览器</font>
无界面（headless）浏览器，会把网站加载到内存并执行页面上的 JavaScript，因为不会展示图形界面，所以运行起来比完整的浏览器更高效。Selenium 搭配无界面浏览器使用，被称为爬虫利器，常用的无界面浏览器有：PhantomJS、Headless Chrome、Headless Firefox，其中，18年3月，PhantomJS 的作者在 GitHub 上宣布暂停开发 PhantomJS，现在使用 PhantomJS 会出现警告：`UserWarning: Selenium support for PhantomJS has been deprecated, please use headless versions of Chrome or Firefox instead`，所以推荐使用谷歌或者火狐的无界面浏览器

# <font color=#ff0000>【5.2】PhantomJS</font>
下载 PhantomJS：https://phantomjs.org/download.html
path 为 PhantomJS 路径，如果系统配置了环境变量，就不用手动指定 executable_path 参数
```python
from selenium import webdriver

path = r'F:\PycharmProjects\Python3爬虫\phantomjs-2.1.1\bin\phantomjs.exe'
driver = webdriver.PhantomJS(executable_path=path)
driver.get("https://www.itrhx.com")
print(driver.page_source)
driver.close()
```

# <font color=#ff0000>【5.3】Headless Chrome</font>
下载 Chromedriver：http://chromedriver.storage.googleapis.com/index.html
需要本地有 Chrome 浏览器，path 为 Headless Chrome 路径，如果系统配置了环境变量，就不用手动指定 executable_path 参数
```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

chrome_options = Options()
chrome_options.add_argument('--headless')
chrome_options.add_argument('--disable-gpu')
path = 'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
driver = webdriver.Chrome(executable_path=path, chrome_options=chrome_options)
driver.get("https://www.itrhx.com")
print(driver.page_source)
driver.close()
```

# <font color=#ff0000>【5.4】Headless Firefox</font>
下载 geckodriver：https://github.com/mozilla/geckodriver/releases/
需要本地有 Firefox 浏览器，path 为 Headless Firefox 路径，如果系统配置了环境变量，就不用手动指定 executable_path 参数
```python
from selenium.webdriver import Firefox
from selenium.webdriver.firefox.options import Options

options = Options()
options.add_argument('-headless')
path = 'F:\PycharmProjects\Python3爬虫\geckodriver.exe'
driver = Firefox(executable_path=path, firefox_options=options)
driver.get("https://www.itrhx.com")
print(driver.page_source)
driver.close()
```
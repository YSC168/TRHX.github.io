---
title: Python3 爬虫学习笔记 C15
tags:
  - 爬虫
  - 代理
categories: 
  - Python3 学习笔记
  - 爬虫学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/spider.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 爬虫学习笔记第十五章 —— 【代理的基本使用】</font></center>

<!--more-->

---

# <font color=#ff0000>【15.1】代理初识</font>
大多数网站都有反爬虫机制，如果一段时间内同一个 IP 发送的请求过多，服务器就会拒绝访问，直接禁封该 IP，此时，设置代理即可解决这个问题，网络上有许多免费代理和付费代理，比如[西刺代理](https://www.xicidaili.com/)，[全网代理 IP](http://www.goubanjia.com/)，[快代理](https://www.kuaidaili.com/)等，设置代理需要用到的就是代理 IP 地址和端口号，如果电脑上装有代理软件（例如：酸酸乳SSR），软件一般会在本机创建 HTTP 或 SOCKS 代理服务，直接使用此代理也可以

# <font color=#ff0000>【15.2】urllib 库使用代理</font>

```python
from urllib.error import URLError
from urllib.request import ProxyHandler, build_opener

proxy = '127.0.0.1:1080'
proxy_handler = ProxyHandler({
    'http': 'http://' + proxy,
    'https': 'https://' + proxy
})
opener = build_opener(proxy_handler)
try:
    response = opener.open('http://httpbin.org/get')
    print(response.read().decode('utf8'))
except URLError as e:
    print(e.reason)
```

http://httpbin.org/get 是一个请求测试站点，借助 ProxyHandler 设置代理，参数为字典类型，键名为协议类型，键值为代理，代理的写法：`proxy = '127.0.0.1:1080'`，其中 127.0.0.1 为 IP 地址，1080 为端口号，这里表示本机的代理软件已经在本地 1080 端口创建了代理服务，代理前面需要加上 http 或者 https 协议，当请求的链接为 http 协议时，ProxyHandler 会自动调用 http 代理，同理，当请求的链接为 https 协议时，ProxyHandler 会自动调用 https 代理，`build_opener()` 方法传入 ProxyHandler 对象来创建一个 opener，调用 `open()` 方法传入一个 url 即可通过代理访问该链接，运行结果为一个 JSON，origin 字段为此时客户端的 IP

```python
{
  "args": {}, 
  "headers": {
    "Accept-Encoding": "identity", 
    "Host": "httpbin.org", 
    "User-Agent": "Python-urllib/3.6"
  }, 
  "origin": "168.70.60.141, 168.70.60.141", 
  "url": "https://httpbin.org/get"
}
```

如果是需要认证的代理，只需要在代理前面加入代理认证的用户名密码即可：

```python
from urllib.error import URLError
from urllib.request import ProxyHandler, build_opener

proxy = 'username:password@127.0.0.1:1080'
proxy_handler = ProxyHandler({
    'http': 'http://' + proxy,
    'https': 'https://' + proxy
})
opener = build_opener(proxy_handler)
try:
    response = opener.open('http://httpbin.org/get')
    print(response.read().decode('utf8'))
except URLError as e:
    print(e.reason)
```

如果代理是 SOCKS5 类型，需要用到 `socks` 模块，设置代理方法如下：

> 扩展：SOCKS5 是一个代理协议，它在使用TCP/IP协议通讯的前端机器和服务器机器之间扮演一个中介角色，使得内部网中的前端机器变得能够访问 Internet 网中的服务器，或者使通讯更加安全

```python
import socks
import socket
from urllib import request
from urllib.error import URLError

socks.set_default_proxy(socks.SOCKS5, '127.0.0.1', 1080)
socket.socket = socks.socksocket
try:
    response = request.urlopen('http://httpbin.org/get')
    print(response.read().decode('utf-8'))
except URLError as e:
    print(e.reason)
```

# <font color=#ff0000>【15.3】requests 库使用代理</font>

requests 库使用代理只需要传入 proxies 参数即可：

```python
import requests

proxy = '127.0.0.1:1080'
proxies = ({
    'http': 'http://' + proxy,
    'https': 'https://' + proxy
})
try:
    response = requests.get('http://httpbin.org/get', proxies=proxies)
    print(response.text)
except requests.exceptions.ChunkedEncodingError as e:
    print('Error', e.args)
```

输出结果：

```python
{
  "args": {}, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Host": "httpbin.org", 
    "User-Agent": "python-requests/2.22.0"
  }, 
  "origin": "168.70.60.141, 168.70.60.141", 
  "url": "https://httpbin.org/get"
}
```

同样的，如果是需要认证的代理，也只需要在代理前面加入代理认证的用户名密码即可：

```python
import requests

proxy = 'username:password@127.0.0.1:1080'
proxies = ({
    'http': 'http://' + proxy,
    'https': 'https://' + proxy
})
try:
    response = requests.get('http://httpbin.org/get', proxies=proxies)
    print(response.text)
except requests.exceptions.ChunkedEncodingError as e:
    print('Error', e.args)
```

如果代理是 SOCKS5 类型，需要用到  `requests[socks]` 模块或者 `socks` 模块，使用 `requests[socks]` 模块时设置代理方法如下：

```python
import requests

proxy = '127.0.0.1:1080'
proxies = {
    'http': 'socks5://' + proxy,
    'https': 'socks5://' + proxy
}
try:
    response = requests.get('http://httpbin.org/get', proxies=proxies)
    print(response.text)
except requests.exceptions.ConnectionError as e:
    print('Error', e.args)
```

使用 `socks` 模块时设置代理方法如下（此类方法为全局设置）：

```python
import requests
import socks
import socket

socks.set_default_proxy(socks.SOCKS5, '127.0.0.1', 1080)
socket.socket = socks.socksocket
try:
    response = requests.get('http://httpbin.org/get')
    print(response.text)
except requests.exceptions.ConnectionError as e:
    print('Error', e.args)
```

# <font color=#ff0000>【15.4】Selenium 使用代理</font>

## <font color=#1BC3FB>【15.4.1】Chrome</font>

```python
from selenium import webdriver

proxy = '127.0.0.1:1080'
chrome_options = webdriver.ChromeOptions()
chrome_options.add_argument('--proxy-server=http://' + proxy)
path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
browser = webdriver.Chrome(executable_path=path, chrome_options=chrome_options)
browser.get('http://httpbin.org/get')
```

通过 ChromeOptions 来设置代理，在创建 Chrome 对象的时候用 chrome_options 参数传递即可，访问目标链接后显示如下信息：

```python
{
  "args": {}, 
  "headers": {
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3", 
    "Accept-Encoding": "gzip, deflate", 
    "Accept-Language": "zh-CN,zh;q=0.9", 
    "Host": "httpbin.org", 
    "Upgrade-Insecure-Requests": "1", 
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.142 Safari/537.36"
  }, 
  "origin": "168.70.60.141, 168.70.60.141", 
  "url": "https://httpbin.org/get"
}
```

如果是认证代理，则设置方法如下：

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
import zipfile

ip = '127.0.0.1'
port = 1080
username = 'username'
password = 'password'

manifest_json = """{"version":"1.0.0","manifest_version": 2,"name":"Chrome Proxy","permissions": ["proxy","tabs","unlimitedStorage","storage","<all_urls>","webRequest","webRequestBlocking"],"background": {"scripts": ["background.js"]
    }
}
"""

background_js ="""
var config = {
        mode: "fixed_servers",
        rules: {
          singleProxy: {
            scheme: "http",
            host: "%(ip) s",
            port: %(port) s
          }
        }
      }

chrome.proxy.settings.set({value: config, scope: "regular"}, function() {});

function callbackFn(details) {
    return {
        authCredentials: {username: "%(username) s",
            password: "%(password) s"
        }
    }
}

chrome.webRequest.onAuthRequired.addListener(
            callbackFn,
            {urls: ["<all_urls>"]},
            ['blocking']
)
""" % {'ip': ip, 'port': port, 'username': username, 'password': password}

plugin_file = 'proxy_auth_plugin.zip'
with zipfile.ZipFile(plugin_file, 'w') as zp:
    zp.writestr("manifest.json", manifest_json)
    zp.writestr("background.js", background_js)
chrome_options = Options()
chrome_options.add_argument("--start-maximized")
path = r'F:\PycharmProjects\Python3爬虫\chromedriver.exe'
chrome_options.add_extension(plugin_file)
browser = webdriver.Chrome(executable_path=path, chrome_options=chrome_options)
browser.get('http://httpbin.org/get')
```

需要在本地创建一个 manifest.json 配置文件和 background.js 脚本来设置认证代理。运行代码之后本地会生成一个 proxy_auth_plugin.zip 文件来保存当前配置


## <font color=#1BC3FB>【15.4.1】PhantomJS</font>

借助 service_args 参数，也就是命令行参数即可设置代理：

```python
from selenium import webdriver

service_args = [
    '--proxy=127.0.0.1:1080',
    '--proxy-type=http'
]
path = r'F:\PycharmProjects\Python3爬虫\phantomjs-2.1.1\bin\phantomjs.exe'
browser = webdriver.PhantomJS(executable_path=path, service_args=service_args)
browser.get('http://httpbin.org/get')
print(browser.page_source)
```

运行结果：

```python
<html><head></head><body><pre style="word-wrap: break-word; white-space: pre-wrap;">{
  "args": {}, 
  "headers": {
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8", 
    "Accept-Encoding": "gzip, deflate", 
    "Accept-Language": "zh-CN,en,*", 
    "Host": "httpbin.org", 
    "User-Agent": "Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/538.1 (KHTML, like Gecko) PhantomJS/2.1.1 Safari/538.1"
  }, 
  "origin": "168.70.60.141, 168.70.60.141", 
  "url": "https://httpbin.org/get"
}
</pre></body></html>
```

如果是需要认证的代理，只需要在 service_args 参数加入 --proxy-auth 选项即可：

```python
from selenium import webdriver

service_args = [
    '--proxy=127.0.0.1:1080',
    '--proxy-type=http',
    '--proxy-auth=username:password'
]
path = r'F:\PycharmProjects\Python3爬虫\phantomjs-2.1.1\bin\phantomjs.exe'
browser = webdriver.PhantomJS(executable_path=path, service_args=service_args)
browser.get('http://httpbin.org/get')
print(browser.page_source)
```
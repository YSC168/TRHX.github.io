---
title: Python3 爬虫学习笔记 C02
tags:
  - 爬虫
  - requests
categories: 
  - Python3 学习笔记
  - 爬虫学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/spider.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 爬虫学习笔记第二章 ——【基本库 requests 的使用】</font></center>

<!--more-->

# <font color=#FF0000> 【2.1】 requests 简介</font>
在 Python 中有两种方式可以发送 HTTP 请求，分别是自带的 urllib 库和第三方的 requests 库

> requests 模块需要使用 pip install 命令安装安装，相比 urllib，它的 API 更加人性化，使用 requests 可以让 Cookies、登录验证、代理设置等操作更加简便，官网介绍：http://cn.python-requests.org

# <font color=#FF0000> 【2.2】 requests 基本用法</font>
示例：
```python
import requests
r = requests.get('https://www.itrhx.com/')
print(type(r))
print(r.encoding)
print(r.status_code)
print(r.cookies)
print(r.json)
print(r.text)
print(r.content)
```
输出结果：
```python
<class 'requests.models.Response'>
utf-8
200
<RequestsCookieJar[]>
<bound method Response.json of <Response [200]>>
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  ......
```
 - r.encoding：服务器内容使用的文本编码；
 - r.status_code：响应状态码，200 代表成功，4xx 代表客户端错误，5xx 服务器响应错误；
 - r.cookies：返回 Cookies；
 - r.json：Requests 内置 JSON 解码器；
 - r.text：服务器响应内容，根据响应头部的字符编码自动解码；
 - r.content：字节方式的响应体，自动解码 gzip 和 deflate 编码的响应。

# <font color=#FF0000> 【2.3】 requests 构建 GET 请求</font>
## <font color=#FF0000> 【2.3.1】 基本用法</font>
示例：
```python
import requests

data = {
    'name': 'TRHX',
    'age': '20'
}
r = requests.get("http://httpbin.org/get", params=data)
print('编码后的URL：', r.url)
print('字符串方式的响应体：', r.text)
```
输出结果：
```python
编码后的URL： http://httpbin.org/get?name=TRHX&age=20
字符串方式的响应体： {
  "args": {
    "age": "20", 
    "name": "TRHX"
  }, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Host": "httpbin.org", 
    "User-Agent": "python-requests/2.22.0"
  }, 
  "origin": "171.115.102.230, 171.115.102.230", 
  "url": "https://httpbin.org/get?name=TRHX&age=20"
}
```
## <font color=#FF0000> 【2.3.2】 二进制数据抓取</font>
以抓取 GitHub 站点图标为例：
```python
import requests

r = requests.get("https://github.com/favicon.ico")
with open('favicon.ico', 'wb') as f:
    f.write(r.content)
```
该代码将会保存站点图标到本地，其他的，比如音频，视频文件都是由二进制码组成的，皆可使用该方法

## <font color=#FF0000> 【2.3.3】 添加 headers</font>
headers 的作用：部分页面禁止 Python 爬虫对其进行爬取，而添加 headers 就可以模拟成浏览器取访问网站，实现数据的爬取，headers 可以在任意网页 F12 检查控制台里面找到，headers 最重要的是 "User-Agent" 字段

<fancybox>
![01](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A30/1.png)
</fancybox>

以为例知乎，只有加了 headers 才能正常爬取，否则会返回 400 Bad Request 没有任何数据

```python
import requests

headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.142 Safari/537.36'
}
r = requests.get("https://www.zhihu.com/explore", headers=headers)
print(r.text)
```

# <font color=#FF0000> 【2.4】 requests 构建 POST 请求</font>

示例：
```python
import requests

data = {'name': 'TRHX', 'age': '20'}
r = requests.post("http://httpbin.org/post", data=data)
print(r.text)
```

输出结果：

```python
{
  "args": {}, 
  "data": "", 
  "files": {}, 
  "form": {
    "age": "22", 
    "name": "germey"
  }, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Content-Length": "18", 
    "Content-Type": "application/x-www-form-urlencoded", 
    "Host": "httpbin.org", 
    "User-Agent": "python-requests/2.22.0"
  }, 
  "json": null, 
  "origin": "171.115.102.230, 171.115.102.230", 
  "url": "https://httpbin.org/post"
}
```

有关 POST 和 GET 两种请求的一些区别：

- POST 更加安全，不会作为 URL 的一部分，不会被缓存，保存在服务器日志、以及浏览器浏览记录中；
- POST 发送的数据更大，GET 有 URL 长度限制；
- POST 可以发送更多的数据类型，GET 只能发送 ASCII 字符；
- POST 比 GET 慢；
- POST 查询参数在 WebForms 保存，GET 查询参数在 QueryString 保存；
- POST 用数据的修改和写入，GET 一般用于搜索排序和筛选之类的操作。

# <font color=#FF0000> 【2.5】 requests 高级用法</font>

## <font color=#FF0000> 【2.5.1】 上传文件</font>

示例：

```python
import requests

files = {'file': open('test.png', 'rb')}
r = requests.post('http://httpbin.org/post', files=files)
print(r.text)
```

输出结果：

```python
{
  "args": {}, 
  "data": "", 
  "files": {
    "file": "data:application/octet-stream;base64,iVBOR......"
  }, 
  "form": {}, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Content-Length": "81383", 
    "Content-Type": "multipart/form-data; boundary=e36a8686cd77c79dc02bfe9d1b010f08", 
    "Host": "httpbin.org", 
    "User-Agent": "python-requests/2.22.0"
  }, 
  "json": null, 
  "origin": "171.115.102.230, 171.115.102.230", 
  "url": "https://httpbin.org/post"
}
```

## <font color=#FF0000> 【2.5.2】 使用 Cookies</font>

对于需要登录后才能获取数据的网页，可以将账号登录的 Cookies 添加到 headers 来实现网页登录爬取，Cookies 可以抓包获取，代码示例：

```python
import requests

headers = {
    'Cookie': 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx',
    'Host': 'www.zhihu.com',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.142 Safari/537.36',
}
r = requests.get('https://www.zhihu.com', headers=headers)
print(r.text)
```

## <font color=#FF0000> 【2.5.3】 会话维持</font>

- 背景介绍：利用 get() 或者 post() 方法来模拟网页请求，相当于是不同的会话，可以理解为用两个浏览器打开了不同的网页；

- 运用场景：首先使用 post() 方法登录网页，然后再使用 get() 方法请求某个页面信息，如果不利用会话维持，将无法获取页面数据

- 维持方法：①两次请求设置一样的 cookies，缺点：繁琐；②使用 Session 对象。

 Session 对象使用示例：

 ```python
 import requests

s = requests.Session()
s.get('http://httpbin.org/cookies/set/number/123456789')
r = s.get('http://httpbin.org/cookies')
print(r.text)
```

输出结果成功获取到设置的 cookies：

```python
{
  "cookies": {
    "number": "123456789"
  }
}
```

## <font color=#FF0000> 【2.5.4】 SSL 证书验证</font>

> SSL 证书是数字证书的一种，由受信任的数字证书颁发机构 CA 在验证服务器身份后颁发，具有服务器身份验证和数据传输加密功能，网站带有 HTTPS 就表明有 SSL 证书

requests 提供了证书验证的功能。当发送 HTTP 请求的时候，它会检查 SSL 证书，verify 参数可以控制是否检查此证书。如果不加 verify 参数，默认为 True，会自动验证。当一个页面的 SSL 证书没有被官方机构认证时，打开页面就会提示“您的连接不是私密连接”，如果没有设置 verify 参数，将会报以下错误：

```python
requests.exceptions.SSLError: ("bad handshake: Error([('SSL routines', 'tls_process_server_certificate', 'certificate verify failed')],)",)
```

设置 verify 参数代码示例：

```python
import requests

response = requests.get('https://www.itrhx.com', verify=False)
print(response.text)
```

## <font color=#FF0000> 【2.5.5】 设置代理</font>

为什么要设置代理：某些网页有反爬虫机制，频繁请求网页就会出现验证码等，还有可能直接封掉 IP，导致爬取失败；这种情况下就可以设置 proxies 参数。
示例：

```python
import requests

proxies = {
  'http': 'http://10.10.1.10:1010',
  'https': 'http://10.10.1.10:1020',
}

requests.get('https://www.itrhx.com', proxies=proxies)
```

免费代理可在[西刺代理](https://www.xicidaili.com/)找到

## <font color=#FF0000> 【2.5.6】 超时设置</font>

与 urllib.request.urlopen() 类似，requests 也可以设置 timeout 参数，请求分为两个阶段：连接和读取

设置连接和读取时间总和：

```python
import requests

r = requests.get('https://www.itrhx.com', timeout=1)
print(r.status_code)
```

分别设置连接和读取时间：

```python
import requests

r = requests.get('https://www.itrhx.com', timeout=(5, 10))
print(r.status_code)
```

永久等待：

```python
import requests

# 两种方法实现
# r = requests.get('https://www.itrhx.com')
r = requests.get('https://www.itrhx.com', timeout=None)
print(r.status_code)
```

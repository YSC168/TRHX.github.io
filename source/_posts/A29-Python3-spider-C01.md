---
title: Python3 爬虫学习笔记 C01
tags:
  - 爬虫
  - urllib
categories: 
  - Python3 学习笔记
  - 爬虫学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/spider.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 爬虫学习笔记第一章 ——【基本库 urllib 的使用】</font></center>

<!--more-->

# <font color=#FF0000> 【1.1】 urllib 简介</font>
在 Python 中有两种方式可以发送 HTTP 请求，分别是自带的 urllib 库和第三方的 requests 库

> urllib 库：Python 内置的 HTTP 请求库，无需额外安装即可使用；Python 2 中有 urllib 和 urllib2 两个库来实现请求的发送，Python 3 中统一为 urllib。官方文档：https://docs.python.org/3/library/urllib.html

<font color=#FF0000>urllib 所包含的常用模块：</font>

 - urllib.request：模拟发送请求；
 - urllib.error：异常处理模块，用于捕获异常；
 - urllib.parse：解析、拆分、合并URL；
 - urllib.robotparser：读取网站的 robots.txt 文件，判断哪些内容可以爬取。

<font color=#FF0000>urllib.request 所包含的常用方法：</font>

 - urllib.request.urlopen()：打开网址URL，这可以是一个字符串或一个 Request对象；
 - urllib.request.Request()：在请求的时候传入一些 headers 等信息；
 - urllib.request.urlretrieve()：将获取的URL的内容写到文件目录中去。

<font color=#FF0000>urllib.error 所包含的两个异常：</font>

- URLError：继承自 OSError 类，是 error 异常模块的基类，由 request 模块产生的异常都可以通过捕获这个类来处理。
- HTTPError：是 URLError 的子类，专门用来处理 HTTP 请求错误，比如认证请求失败等。

<font color=#FF0000>urllib.parse 所包含的常用方法：</font>

 - urllib.parse.urlencode()：将字典参数序列化为 GET 请求参数；
 - urllib.parse.parse_qs()：将 GET 请求参数反序列化转回字典；
 - urllib.parse.parse_qsl()：将参数转化为元组组成的列表；
 - urllib.parse.urlparse()：对 URL 进行分段（返回6个结果）；
 - urllib.parse.urlunparse()：对 URL 进行组合（长度必须为6）；
 - urllib.parse.urlsplit()：对 URL 进行分段（不单独解析params部分，返回5个结果）；
 - urllib.parse.urlunsplit()：对 URL 进行组合（长度必须为5）；
 - urllib.parse.urljoin()：对 URL 进行组合（没有长度限制，给定两个参数，自动分析 scheme、netloc 和 path 这 3 个内容并对新链接缺失的部分进行补充，最后返回结果）；
 - urllib.parse.quote()：将内容转化为 URL 编码格式；
 - urllib.parse.unquote()：对 URL 进行解码。

<font color=#FF0000>urllib.robotparser 所包含的类：</font>
 
 - RobotFileParser：根据网站的 robots.txt 文件来判断一个爬取爬虫是否有权限来爬取这个网页

# <font color=#FF0000> 【1.2】 urllib.request 发送请求</font>

## <font color=#FF0000>【1.2.1】 urllib.request.urlopen()</font>

### <font color=#FF0000>【1.2.1.1】 基本使用方法</font>
urlopen() 函数的 API：
```python
urllib.request.urlopen(url, data=None, [timeout,]*, cafile=None, capath=None, cadefault=False, context=None)
```
基本使用：运行以下代码可得到 https://www.itrhx.com/ 的网页源代码：
```python
import urllib.request

response = urllib.request.urlopen('https://www.itrhx.com/')
print(response.read().decode('utf-8'))
```
输出响应对象的类型和属性：
```python
import urllib.request

response = urllib.request.urlopen('https://www.itrhx.com/')
print(type(response))            # 响应类型
print(response.status)           # 返回结果的状态码，200代表请求成功
print(response.getheaders())       # 响应的头信息
print(response.getheader('Server'))  # 获取响应头的 server 值
```
运行结果：
```python
<class 'http.client.HTTPResponse'>
200
[('Content-Type', 'text/html; charset=utf-8'), ('Server', 'GitHub.com'), ('Last-Modified', 'Sat, 17 Aug 2019 12:16:48 GMT'), ('ETag', '"5d57f030-10863"'), ('Access-Control-Allow-Origin', '*'), ('Expires', 'Sat, 17 Aug 2019 19:41:25 GMT'), ('Cache-Control', 'max-age=600'), ('X-Proxy-Cache', 'MISS'), ('X-GitHub-Request-Id', 'C748:735D:5B7461:619B95:5D58560B'), ('Content-Length', '67683'), ('Accept-Ranges', 'bytes'), ('Date', 'Sun, 18 Aug 2019 13:28:44 GMT'), ('Via', '1.1 varnish'), ('Age', '228'), ('Connection', 'close'), ('X-Served-By', 'cache-tyo19931-TYO'), ('X-Cache', 'HIT'), ('X-Cache-Hits', '1'), ('X-Timer', 'S1566134924.190474,VS0,VE0'), ('Vary', 'Accept-Encoding'), ('X-Fastly-Request-ID', '25a69f8130fc9cae412d28990a724543d7d05e8b')]
GitHub.com
```
### <font color=#FF0000>【1.2.1.2】 添加参数</font>
根据 urlopen() 函数的 API 可知，除了最基本的 URL 参数以外，我们还可以传递其他内容，比如 data（附加数据）、timeout（超时时间）等，以下用 data 和 timeout 参数举例说明。

#### <font color=#FF0000>● data 参数</font>
如果要添加 data 参数，需要使用 bytes 方法将参数转化为字节流编码格式的内容，即 bytes 类型。另外，如果传递了这个参数，则它的请求方式就不再是 GET 方式，而是 POST 方式。代码示例：
```python
import urllib.parse
import urllib.request

data = bytes(urllib.parse.urlencode({'word': 'hello'}), encoding='utf8')
response = urllib.request.urlopen('http://httpbin.org/post', data=data)
print(response.read())
```
httpbin.org 站点提供 HTTP 请求测试，http://httpbin.org/post 用于测试 POST 请求，示例中传递一个值为 hello 的 word 参数。使用 bytes 方法，将其转码成 bytes（字节流）类型。该方法的第一个参数需要是 str（字符串）类型，需要用 urllib.parse 模块里的 urlencode 方法来将参数字典转化为字符串；第二个参数指定编码格式为 utf8，运行结果：
```python
b'{
	"args": {},
	"data": "", 
	"files": {},
	"form": {
		"word": "hello"
	},
	"headers": {
		"Accept-Encoding": "identity", 
		"Content-Length": "10",
		"Content-Type": "application/x-www-form-urlencoded",
		"Host": "httpbin.org",
		"User-Agent": "Python-urllib/3.6"
	}, 
	"json": null, 
	"origin": "171.115.101.10, 171.115.101.10", 
	"url": "https://httpbin.org/post"
}'
```
#### <font color=#FF0000>● timeout 参数</font>
举例：
```python
import urllib.request

response = urllib.request.urlopen('http://httpbin.org/get', timeout=0.1)  
print(response.read())
```
运行结果：
```python
...
During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "C:/Users/Lenovo/Desktop/1.py", line 2, in <module>
    response = urllib.request.urlopen('http://httpbin.org/get', timeout=0.1)
 ...
urllib.error.URLError: <urlopen error timed out>
```
timeout 设置为0.1，0.1秒过后服务器没有响应，便会抛出 URLError 异常
进阶：使用 try except 语句抛出异常

## <font color=#FF0000>【1.2.2】 urllib.request.Request()</font>
Request() 方法可以在请求的时候传入一些 data、headers 等信息
Request() 的构造方法：
```python
class urllib.request.Request(url, data=None, headers={}, origin_req_host=None, unverifiable=False, method=None)
```
构造方法各个参数的解释：

 - url：用于请求 URL，这是必传参数，其他都是可选参数。

 - data：如果要传，必须传 bytes（字节流）类型的。如果它是字典，可以先用 urllib.parse 模块里的 urlencode() 编码。

 - headers：是一个字典，它就是请求头，可以在构造请求时通过 headers 参数直接构造，也可以通过调用请求实例的 add_header() 方法添加。添加请求头最常用的用法就是通过修改 User-Agent 来伪装浏览器，默认的 User-Agent 是 Python-urllib，我们可以通过修改它来伪装浏览器。

 - origin_req_host：指的是请求方的 host 名称或者 IP 地址。

 - unverifiable：表示这个请求是否是无法验证的，默认是 False，意思就是说用户没有足够权限来选择接收这个请求的结果。例如，我们请求一个 HTML 文档中的图片，但是我们没有自动抓取图像的权限，这时 unverifiable 的值就是 True。

 - method：是一个字符串，用来指示请求使用的方法，比如 GET、POST 和 PUT 等。

简单举例：
```python
import urllib.request
import urllib.parse

url = 'http://www.baidu.com/'

# 定制要伪装的头部
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.86 Safari/537.36'
}
# 构建请求对象
request = urllib.request.Request(url=url, headers=headers)
# 发送请求
response = urllib.request.urlopen(request)
print(response.read().decode())
```

## <font color=#FF0000>【1.2.3】 urllib.request.urlretrieve()</font>
将获取到的 URL 内容保存到当前文件夹，简单举例：
```python
import urllib.request

url = 'https://www.itrhx.com/images/trhx.png'

# response = urllib.request.urlopen(image_url)
# with open('trhx.png', 'wb') as fp:
#    fp.write(response.read())

urllib.request.urlretrieve(url, 'trhx.png')
```
# <font color=#FF0000> 【1.3】 urllib.error 异常处理</font>
## <font color=#FF0000>【1.3.1】 URLError</font>
如果打开一个不存在的页面，就会出现 URLError 错误，该错误有一个 reason 属性，用于返回错误的原因。简单举例：
```python
from urllib import request, error  
try:  
    response = request.urlopen('https://www.itrhx.com/index/')  
except error.URLError as e:  
    print(e.reason)
```
输出结果：
```python
Not Found
```

## <font color=#FF0000>【1.3.2】 HTTPError</font>
URLError 的子类，专门用来处理 HTTP 请求错误，比如认证请求失败等。它有如下3个属性：

 - code：返回 HTTP 状态码，比如 404 表示网页不存在，500 表示服务器内部错误等。
 - reason：同父类一样，用于返回错误的原因。
 - headers：返回请求头。

简单举例：
```python
from urllib import request, error  
try:  
    response = request.urlopen('https://www.itrhx.com/index/')  
except error.HTTPError as e:  
    print(e.code, e.reason, e.headers)
```
输出结果：
```python
404 Not Found Content-Type: text/html; charset=utf-8
Server: GitHub.com
ETag: "5d57f030-7f2"
Access-Control-Allow-Origin: *
X-Proxy-Cache: MISS
X-GitHub-Request-Id: 4B46:2F5D:6DE0F1:755BB2:5D5964C5
Content-Length: 2034
Accept-Ranges: bytes
Date: Sun, 18 Aug 2019 14:50:41 GMT
Via: 1.1 varnish
Age: 252
Connection: close
X-Served-By: cache-tyo19951-TYO
X-Cache: HIT
X-Cache-Hits: 1
X-Timer: S1566139842.563134,VS0,VE0
Vary: Accept-Encoding
X-Fastly-Request-ID: e9eb0a507be66a866bfaa7c5cc2e1c53b1f7ccab
```
## <font color=#FF0000>【1.3.3】 进阶用法</font>
因为 URLError 是 HTTPError 的父类，所以可以先选择捕获子类的错误，再去捕获父类的错误，前面的代码改进：
```python
from urllib import request, error  
​
try:  
    response = request.urlopen('https://www.itrhx.com/index/')  
except error.HTTPError as e:  
    print(e.reason, e.code, e.headers)  
except error.URLError as e:  
    print(e.reason)  
else:  
    print('Request Successfully')
```

# <font color=#FF0000> 【1.4】 urllib.parse 解析 URL</font>
## <font color=#FF0000>【1.4.1】 urllib.parse.urlencode()</font>
将字典参数序列化为 GET 请求参数，示例：
```python
from urllib.parse import urlencode
data = {
    'ie': 'utf-8',
    'wd': 'TRHX',
}
base_url = 'http://www.baidu.com?'
url = base_url + urlencode(data)
print(url)
```
输出结果：
```python
http://www.baidu.com?ie=utf-8&wd=TRHX
```

## <font color=#FF0000>【1.4.2】 urllib.parse.parse_qs()</font>
与 urlencode() 相反，将 GET 请求参数反序列化转回字典，示例：
```python
from urllib.parse import parse_qs
query = 'name=TRHX&age=20'
print(parse_qs(query))
```
输出结果：
```python
{'name': ['TRHX'], 'age': ['20']}
```

## <font color=#FF0000>【1.4.3】 urllib.parse.parse_qsl()</font>
将参数转化为元组组成的列表，示例：
```python
from urllib.parse import parse_qsl
query = 'name=TRHX&age=20'
print(parse_qsl(query))
```
输出 结果：
```python
[('name', 'TRHX'), ('age', '20')]
```
## <font color=#FF0000>【1.4.4】 urllib.parse.urlparse()</font>
对 URL 进行分段，返回 6 个结果，示例：
```python
from urllib.parse import urlparse
result = urlparse('http://www.baidu.com/index.html;user?id=5#comment')
print(type(result), result)
```
输出结果：
```python
<class 'urllib.parse.ParseResult'> ParseResult(scheme='http', netloc='www.baidu.com', path='/index.html', params='user', query='id=5', fragment='comment')
```
返回结果为 ParseResult 类型的对象，含 scheme、netloc、path、params、query 和 fragment 6 个部分，依次代表协议、域名、路径、参数、查询条件、锚点

## <font color=#FF0000>【1.4.5】 urllib.parse.urlunparse()</font>
与 urlparse() 相反，对 URL 进行组合，传入的参数是一个可迭代对象，长度必须是 6，否则会抛出参数数量不足或者过多的问题，示例：
```python
from urllib.parse import urlunparse  
data = ['http', 'www.baidu.com', 'index.html', 'user', 'a=6', 'comment']  
print(urlunparse(data))
```
输出结果：
```python
http://www.baidu.com/index.html;user?a=6#comment
```

## <font color=#FF0000>【1.4.6】 urllib.parse.urlsplit()</font>
与 urlparse() 方法相似，但是它不再单独解析 params 部分，只返回 5 个结果。params 会合并到 path 中，示例：
```python
from urllib.parse import urlsplit  
result = urlsplit('http://www.baidu.com/index.html;user?id=5#comment')  
print(result)
```
输出结果：
```python
SplitResult(scheme='http', netloc='www.baidu.com', path='/index.html;user', query='id=5', fragment='comment')
```

## <font color=#FF0000>【1.4.7】 urllib.parse.urlunsplit()</font>
与 urlunparse() 方法类似，对 URL 进行组合，传入的参数也是一个可迭代对象，长度必须为 5，示例：
```python
from urllib.parse import urlunsplit  
data = ['http', 'www.baidu.com', 'index.html', 'a=6', 'comment']  
print(urlunsplit(data))
```
输出结果：
```python
http://www.baidu.com/index.html?a=6#comment
```

## <font color=#FF0000>【1.4.8】 urllib.parse.urljoin()</font>
对 URL 进行组合，提供两个 URL 作为两个参数，将会自动分析 URL 的 scheme、netloc 和 path 这 3 个内容并对新链接缺失的部分进行补充，最后返回结果，示例：
```python
from urllib.parse import urljoin  
print(urljoin('http://www.baidu.com', 'friends.html'))  
print(urljoin('http://www.baidu.com', 'https://www.itrhx.com/friends.html'))  
print(urljoin('http://www.baidu.com/friends.html', 'https://www.itrhx.com/friends.html'))  
print(urljoin('http://www.baidu.com/friends.html', 'https://www.itrhx.com/friends.html?id=2'))  
print(urljoin('http://www.baidu.com?wd=trhx', 'https://www.itrhx.com/index.html'))  
print(urljoin('http://www.baidu.com', '?category=2#comment'))  
print(urljoin('www.baidu.com', '?category=2#comment'))  
print(urljoin('www.baidu.com#comment', '?category=2'))
```
输出结果：
```python
http://www.baidu.com/friends.html
https://www.itrhx.com/friends.html
https://www.itrhx.com/friends.html
https://www.itrhx.com/friends.html?id=2
https://www.itrhx.com/index.html
http://www.baidu.com?category=2#comment
www.baidu.com?category=2#comment
www.baidu.com?category=2
```

## <font color=#FF0000>【1.4.9】 urllib.parse.quote()</font>
将内容转化为 URL 编码的格式。当 URL 中带有中文参数时，可以将中文字符转化为 URL 编码，示例：
```python
from urllib.parse import quote
keyword = '中国'  
url = 'https://www.baidu.com/s?wd=' + quote(keyword)  
print(url)
```
输出结果：
```python
https://www.baidu.com/s?wd=%E4%B8%AD%E5%9B%BD
```

## <font color=#FF0000>【1.4.10】 urllib.parse.unquote()</font>
与 quote() 方法相反，对 URL 进行解码，示例：
```python
from urllib.parse import unquote  
url = 'https://www.baidu.com/s?wd=%E4%B8%AD%E5%9B%BD'  
print(unquote(url))
```
输出结果：
```python
https://www.baidu.com/s?wd=中国
```

# <font color=#FF0000> 【1.5】 urllib.robotparser 爬取权限判断</font>
## <font color=#FF0000>【1.5.1】 Robots 协议简介</font>

> Robots 协议即爬虫协议，用来告诉爬虫和搜索引擎哪些页面可以抓取，哪些不可以抓取。它通常是一个叫作 robots.txt 的文本文件，一般放在网站的根目录下。

robots.txt 基本格式：
```text
User-agent:
Disallow:
Allow:
```
 - User-agent 为搜索爬虫的名称，设置为 * 则表示对任何爬虫皆有效；
 - Disallow 指定了不允许抓取的目录，设置为 / 则代表不允许抓取所有页面；
 - Allow 指定了允许抓取的目录，一般和 Disallow 一起使用，一般不会单独使用，用来排除某些限制。

一些常见的搜索爬虫名称及其对应的网站：

| 爬虫名称    | 网站名称  | 网站地址          |
| ----------- | --------- | ----------------- |
| BaiduSpider | 百度      | www.baidu.com     |
| Googlebot   | 谷歌      | www.google.com    |
| 360Spider   | 360   | www.so.com        |
|  Sogouspider | 搜狗    | www.sogou.com |
| YodaoBot    | 有道      | www.youdao.com    |
| Bingbot       | 必应      | www.bing.com       |
| Yahoo!  Slurp| 雅虎  | www.yahoo.com   |
| ia_archiver | Alexa     | www.alexa.cn      |
| Scooter     | altavista | www.altavista.com |

## <font color=#FF0000>【1.5.2】 RobotFileParser 类常用方法</font>
RobotFileParser 类的声明：
```python
urllib.robotparser.RobotFileParser(url='')
```
常用方法及其解释：
 - set_url：用来设置 robots.txt 文件的链接。如果在创建 RobotFileParser
   对象时传入了链接，那么就不需要再用这种方法了。

 - read：读取 robots.txt 文件并进行分析。此方法执行一个读取和分析操作，若不调用此方法，接下来的判断都会为 False，这个方法不会返回任何内容，但是执行了读取操作。

 - parse：解析 robots.txt 文件，传入的参数是 robots.txt 某些行的内容，它会按照 robots.txt 的语法规则来分析这些内容。

 - can_fetch：该方法传入两个参数，第一个是 User-agent，第二个是要抓取的 URL。返回的内容是该搜索引擎是否可以抓取这个 URL，返回结果是 True 或 False。

 - mtime：返回的是上次抓取和分析 robots.txt 的时间，此方法可以定期检查来抓取最新的 robots.txt。

 - modified：将当前时间设置为上次抓取和分析 robots.txt 的时间。

以简书为例：
```python
from urllib.robotparser import RobotFileParser
rp = RobotFileParser()
rp.set_url('http://www.jianshu.com/robots.txt')
rp.read()
print(rp.can_fetch('*', 'https://www.jianshu.com/p/6d9527300b4c'))
print(rp.can_fetch('*', "http://www.jianshu.com/search?q=python&page=1&type=collections"))
```
输出结果：
```python
False
False
```
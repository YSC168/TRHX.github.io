---
title: Python3 爬虫学习笔记 C18
tags:
  - 爬虫
  - pyspider
categories: 
  - Python3 学习笔记
  - 爬虫学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/spider.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 爬虫学习笔记第十八章 —— 【爬虫框架 pyspider — 深入理解】</font></center>

<!--more-->

---

# <font color=#ff0000>【18.1】启动参数</font>

常用启动命令：`pyspider all`，完整命令结构为：`pyspider [OPTIONS] COMMAND [ARGS]`，OPTIONS 为可选参数，包含以下参数：

- <font color=#ff0000>**-c, --config FILENAME**</font>：指定配置文件名称
- <font color=#ff0000>**--logging-config TEXT**</font>：日志配置文件名称，默认: pyspider/pyspider/logging.conf
- <font color=#ff0000>**--debug**</font>：开启调试模式
- <font color=#ff0000>**--queue-maxsize INTEGER**</font>：队列的最大长度
- <font color=#ff0000>**--taskdb TEXT**</font>：taskdb 的数据库连接字符串，默认: sqlite
- <font color=#ff0000>**--projectdb TEXT**</font>：projectdb 的数据库连接字符串，默认: sqlite
- <font color=#ff0000>**--resultdb TEXT**</font>：resultdb 的数据库连接字符串，默认: sqlite
- <font color=#ff0000>**--message-queue TEXT**</font>：消息队列连接字符串，默认: multiprocessing.Queue
- <font color=#ff0000>**--phantomjs-proxy TEXT**</font>：PhantomJS 使用的代理，ip:port 的形式
- <font color=#ff0000>**--data-path TEXT**</font>：数据库存放的路径
- <font color=#ff0000>**--add-sys-path / --not-add-sys-path**</font>：将当前工作目录添加到python lib搜索路径
- <font color=#ff0000>**--version**</font>：显示 pyspider 的版本信息
- <font color=#ff0000>**--help**</font>：显示帮助信息

配置文件为一个 JSON 文件，一般为 config.json 文件，常用配置如下：

```python
{
  "taskdb": "mysql+taskdb://username:password@host:port/taskdb",
  "projectdb": "mysql+projectdb://username:password@host:port/projectdb",
  "resultdb": "mysql+resultdb://username:password@host:port/resultdb",
  "message_queue": "amqp://username:password@host:port/%2F",
  "webui": {
  	"port": 5000,
    "username": "some_name",
    "password": "some_passwd",
    "need-auth": true
  }
}
```

可以设置对应的用户名，密码，端口等信息，使用命令 `pyspider -c config.json all` 即可运行

---

# <font color=#ff0000>【18.2】运行单个组件</font>

pyspider 的架构主要分为 Scheduler（调度器）、Fetcher（抓取器）、Processer（处理器）三个部分，都可以单独运行，基本命令： `pyspider [component_name] [options]`

---

## <font color=#1BC3FB>【18.2.1】运行 Scheduler</font>

```python
pyspider scheduler [OPTIONS]
```

```python
Options:
  --xmlrpc /--no-xmlrpc
  --xmlrpc-host TEXT
  --xmlrpc-port INTEGER
  --inqueue-limit INTEGER  任务队列的最大长度，如果满了则新的任务会被忽略
  --delete-time INTEGER    设置为 delete 标记之前的删除时间
  --active-tasks INTEGER   当前活跃任务数量配置
  --loop-limit INTEGER     单轮最多调度的任务数量
  --fail-pause-num INTEGER 上次失败时自动暂停项目暂停次数，任务失败，将0设置为禁用
  --scheduler-cls TEXT     Scheduler 使用的类
  --threads TEXT           ThreadBaseScheduler 的线程号，默认值：4
  --help                   显示帮助信息
```

---

## <font color=#1BC3FB>【18.2.2】运行 Fetcher</font>

```python
pyspider fetcher [OPTIONS]
```

```python
Options:
  --xmlrpc /--no-xmlrpc
  --xmlrpc-host TEXT
  --xmlrpc-port INTEGER
  --poolsize INTEGER         同时请求的个数
  --proxy TEXT               使用的代理
  --user-agent TEXT          使用的 User-Agent
  --timeout TEXT             超时时间
  --phantomjs-endpoint TEXT  phantomjs 的端点，通过 pyspider 启动 phantomjs
  --splash-endpoint TEXT     执行 splash 的端点：http://splash.readthedocs.io/en/stable/api.html execut
  --fetcher-cls TEXT         Fetcher 使用的类
  --help                     显示帮助信息
```

---

## <font color=#1BC3FB>【18.2.3】运行 Processer</font>

```python
pyspider processor [OPTIONS]
```

```python
Options:
  --processor-cls TEXT  Processor 使用的类
  --process-time-limit INTEGER    脚本处理时间限制
  --help                          显示帮助信息
```

---

## <font color=#1BC3FB>【18.2.4】运行 WebUI</font>

```python
pyspider webui [OPTIONS]
```

```python
Options:
  --host TEXT                   运行地址
  --port INTEGER                运行端口
  --cdn TEXT                    JS 和 CSS 的 CDN 服务器
  --scheduler-rpc TEXT          Scheduler 的 xmlrpc 路径
  --fetcher-rpc TEXT            Fetcher 的 xmlrpc 路径
  --max-rate FLOAT              每个项目最大的 rate 值
  --max-burst FLOAT             每个项目最大的 burst 值
  --username TEXT               Auth 验证的用户名
  --password TEXT               Auth 验证的密码
  --need-auth                   是否需要验证
  --webui-instance TEXT         运行时使用的 Flask 应用
  --process-time-limit INTEGER  调试中的脚本处理时间限制
  --help                        显示帮助信息
```

---

# <font color=#ff0000>【18.3】crawl() 方法各参数</font>

参数文档：http://docs.pyspider.org/en/latest/apis/self.crawl/

---

- <font color=#ff0000>**url**</font>：爬取目标 URL，可以定义为单个 URL 字符串，也可以定义成 URL 列表

---

- <font color=#ff0000>**callback**</font>：回调函数，指定了该 URL 对应的响应内容用哪个方法来解析，示例：

```python
  def on_start(self):
    self.crawl('http://www.itrhx.com/', callback=self.index_page)
```

代码解释：指定 `callback` 为 `index_page`，代表爬取 http://www.itrhx.com/ 得到的响应会用 `index_page()` 方法来解析，而 `index_page()` 方法的第一个参数就是响应对象，如下所示：

```python
  def index_page(self, response):
    pass
```

---

- <font color=#ff0000>**age**</font>：任务的有效时间，如果某个任务在有效时间内且已经被执行，则它不会重复执行，有如下两种设置方法：

```python
  def on_start(self):
    self.crawl('http://www.itrhx.com/', callback=self.callback, age=10*24*60*60)
```

```python
  @config(age=10 * 24 * 60 * 60)
  def callback(self):
      pass
```

---

- <font color=#ff0000>**priority**</font>：爬取任务的优先级，其值默认是 0，priority 的数值越大，对应的请求会越优先被调度，如下所示，`2.html` 页面将会优先爬取：

```python
  def index_page(self):
    self.crawl('http://www.itrhx.com/1.html', callback=self.index_page)
    self.crawl('http://www.itrhx.com/2.html', callback=self.detail_page, priority=1)
```

---

- <font color=#ff0000>**exetime**</font>：设置定时任务，其值是时间戳，默认是 0，即代表立即执行，如下所示表示该任务会在 30 分钟之后执行：

```python
  import time
  def on_start(self):
    self.crawl('http://www.itrhx.com/', callback=self.callback, exetime=time.time()+30*60)
```

---

- <font color=#ff0000>**retries**</font>：定义重试次数，其值默认是 3

---

- <font color=#ff0000>**itag**</font>：设置判定网页是否发生变化的节点值，在爬取时会判定次当前节点是否和上次爬取到的节点相同。如果节点相同，则证明页面没有更新，就不会重复爬取，如下所示：

```python
  def index_page(self, response):
    for item in response.doc('.item').items():
        self.crawl(item.find('a').attr.url, callback=self.detail_page, itag=item.find('.update-time').text())
```

代码解释：设置 `update-time` 这个节点的值为 itag，在下次爬取时就会首先检测这个值有没有发生变化，如果没有变化，则不再重复爬取，否则执行爬取

---

- <font color=#ff0000>**auto_recrawl**</font>：开启时，爬取任务在过期后会重新执行，循环时间即定义的 age 时间长度，如下所示：

```python
  def on_start(self):
    self.crawl('http://www.itrhx.com/', callback=self.callback, age=5*60*60, auto_recrawl=True)
```

代码解释：定义 `age` 有效期为 5 小时，设置了 `auto_recrawl` 为 `True`，这样任务就会每 5 小时执行一次

---

- <font color=#ff0000>**method**</font>：HTTP 请求方式，默认为 GET，如果想发起 POST 请求，可以将 method 设置为 POST

---

- <font color=#ff0000>**params**</font>：定义 GET 请求参数，如下所示表示两个等价的爬取任务：

```python
  def on_start(self):
    self.crawl('http://httpbin.org/get', callback=self.callback, params={'a': 123, 'b': 'c'})
    self.crawl('http://httpbin.org/get?a=123&b=c', callback=self.callback)
```

---

- <font color=#ff0000>**data**</font>：POST 表单数据，当请求方式为 POST 时，我们可以通过此参数传递表单数据，如下所示：

```python
  def on_start(self):
    self.crawl('http://httpbin.org/post', callback=self.callback, method='POST', data={'a': 123, 'b': 'c'})
```

---

- <font color=#ff0000>**files**</font>：上传的文件，需要指定文件名，如下所示：

```python
  def on_start(self):
    self.crawl('http://httpbin.org/post', callback=self.callback, method='POST', files={field: {filename: 'content'}})
```

---

- <font color=#ff0000>**user_agent**</font>：爬取使用的 User-Agent

---

- <font color=#ff0000>**headers**</font>：爬取时使用的 Headers，即 Request Headers

---

- <font color=#ff0000>**cookies**</font>：爬取时使用的 Cookies，为字典格式

---

- <font color=#ff0000>**connect_timeout**</font>：在初始化连接时的最长等待时间，默认为 20 秒

---

- <font color=#ff0000>**timeout**</font>：抓取网页时的最长等待时间，默认为 120 秒

---

- <font color=#ff0000>**allow_redirects**</font>：确定是否自动处理重定向，默认为 True

---

- <font color=#ff0000>**validate_cert**</font>：确定是否验证证书，此选项对 HTTPS 请求有效，默认为 True

---

- <font color=#ff0000>**proxy**</font>：爬取时使用的代理，支持用户名密码的配置，格式为 `username:password@hostname:port`，如下所示：

```python
  def on_start(self):
    self.crawl('http://httpbin.org/get', callback=self.callback, proxy='127.0.0.1:9743')
```

也可以设置 `craw_config` 来实现全局配置，如下所示：

```python
  class Handler(BaseHandler):
    crawl_config = {'proxy': '127.0.0.1:9743'}
```

---

- <font color=#ff0000>**fetch_type**</font>：开启 PhantomJS 渲染，如果遇到 JavaScript 渲染的页面，指定此字段即可实现 PhantomJS 的对接，pyspider 将会使用 PhantomJS 进行网页的抓取，如下所示：

```python
  def on_start(self):
    self.crawl('https://www.taobao.com', callback=self.index_page, fetch_type='js')
```

---

- <font color=#ff0000>**js_script**</font>：页面加载完毕后执行的 JavaScript 脚本，如下所示，页面加载成功后将执行页面混动的 JavaScript 代码，页面会下拉到最底部：

```python
  def on_start(self):
    self.crawl('http://www.example.org/', callback=self.callback,
               fetch_type='js', js_script='''
               function() {window.scrollTo(0,document.body.scrollHeight);
                   return 123;
               }
               ''')
```

---

- <font color=#ff0000>**js_run_at**</font>：代表 JavaScript 脚本运行的位置，是在页面节点开头还是结尾，默认是结尾，即 `document-end`

---

- <font color=#ff0000>**js_viewport_width/js_viewport_height**</font>：JavaScript 渲染页面时的窗口大小

---

- <font color=#ff0000>**load_images**</font>：在加载 JavaScript 页面时确定是否加载图片，默认为否

---

- <font color=#ff0000>**save**</font>：在不同的方法之间传递参数，如下所示：

```python
  def on_start(self):
    self.crawl('http://www.example.org/', callback=self.callback,
               save={'page': 1})

  def callback(self, response):
    return response.save['page']
```

---

- <font color=#ff0000>**cancel**</font>：取消任务，如果一个任务是 `ACTIVE` 状态的，则需要将 `force_update` 设置为 `True`

---

- <font color=#ff0000>**force_update**</font>：即使任务处于 ACTIVE 状态，那也会强制更新状态

---

# <font color=#ff0000>【18.4】任务区分</font>

pyspider 判断两个任务是否是重复的是使用的是该任务对应的 URL 的 MD5 值作为任务的唯一 ID，如果 ID 相同，那么两个任务就会判定为相同，其中一个就不会爬取了

某些情况下，请求的链接是同一个，但是 POST 的参数不同，这时可以重写 `task_id()` 方法，利用 URL 和 POST 的参数来生成 ID，改变这个 ID 的计算方式来实现不同任务的区分：

```python
import json
from pyspider.libs.utils import md5string
def get_taskid(self, task):
    return md5string(task['url']+json.dumps(task['fetch'].get('data', '')))
```

---

# <font color=#ff0000>【18.5】全局配置</font>

pyspider 可以使用 `crawl_config` 来指定全局的配置，配置中的参数会和 `crawl()` 方法创建任务时的参数合并：

```python
class Handler(BaseHandler):
    crawl_config = {
        'headers': {'User-Agent': 'GoogleBot',}
        'proxy': '127.0.0.1:9743'
    }
 ```

---

# <font color=#ff0000>【18.6】定时爬取</font>

通过 `every` 属性来设置爬取的时间间隔，如下代码表示每天执行一次爬取：

```python
@every(minutes=24 * 60)
def on_start(self):
    for url in urllist:
        self.crawl(url, callback=self.index_page)
```

注意事项：如果设置了任务的有效时间（age 参数），因为在有效时间内爬取不会重复，所以要把有效时间设置得比重复时间更短，这样才可以实现定时爬取

错误举例：设定任务的过期时间为 5 天，而自动爬取的时间间隔为 1 天，当第二次尝试重新爬取的时候，pyspider 会监测到此任务尚未过期，便不会执行爬取：

```python
@every(minutes=24 * 60)
def on_start(self):
    self.crawl('http://www.itrhx.com/', callback=self.index_page)

@config(age=5 * 24 * 60 * 60)
def index_page(self):
    pass
```

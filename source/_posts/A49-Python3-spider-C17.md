---
title: Python3 爬虫学习笔记 C17
tags:
  - 爬虫
  - pyspider
categories: 
  - Python3 学习笔记
  - 爬虫学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/spider.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 爬虫学习笔记第十七章 —— 【爬虫框架 pyspider — 基本使用】</font></center>

<!--more-->

---

# <font color=#ff0000>【17.1】初识 pyspider</font>

pyspider 是由国人 Binux 编写的一个 Python 爬虫框架

- GitHub：https://github.com/binux/pyspider 
- 官方文档（英文）：http://docs.pyspider.org/
- 非官方文档（中文）：http://book.crifan.com/books/python_spider_pyspider/website/
- 非官方文档（中文）：https://www.cntofu.com/book/156/index.md

pyspider 特性：

- python 脚本控制，可以使用任何 html 解析包（内置 pyquery）
- WEB 界面编写调试脚本，起停脚本，监控执行状态，查看活动历史，获取结果产出
- 支持 MySQL、MongoDB、Redis、SQLite、Elasticsearch、PostgreSQL
- 对接了 PhantomJS，支持抓取 JavaScript 的页面
- 组件可替换，支持单机和分布式部署，支持 Docker 部署
- 提供优先级控制、失败重试、定时抓取等功能

Windows 系统安装 pyspider：

使用命令 `pip install pyspider` 安装，若报 PyCurl 相关错误，可访问 https://www.lfd.uci.edu/~gohlke/pythonlibs/#pycurl 下载对应 wheel 文件并使用命令 `pip install whl文件名` 安装即可

如果要爬取 JavaScrip 渲染的页面，还要下载 PhantomJS，并将 PhantomJS 的路径配置到环境变量里，或者直接复制到 Python 安装目录的 Scripts 文件夹，需要用到数据库储存的话，同样要安装好相应的数据库

准备就绪后，使用 `pyspider all` 命令可启动 pyspider，浏览器打开：http://localhost:5000/ 可以看到 pyspider 的 WebUI 管理界面

---

# <font color=#ff0000>【17.2】使用 pyspider</font>

---

## <font color=#1BC3FB>【17.2.1】主界面</font>

当成功创建了一个爬虫项目后，主界面如下所示：

<fancybox>
![01](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A49/01.png)
</fancybox>

 - <font color=#ff0000>Recent Active Tasks</font>：查看最近活动的任务，会跳转到一个页面有列表显示

 - <font color=#ff0000>Create</font>：创建一个新的爬虫项目

 - <font color=#ff0000>group</font>：定义项目的分组，以方便管理，若 group 设置为 delete，则该项目将会在24小时之后删除

 - <font color=#ff0000>project name</font>：爬虫项目名称

 - <font color=#ff0000>status</font>：项目状态，各状态如下：
	<font color=#1BC3FB>TODO</font>：一个爬虫项目刚刚创建时的状态，此状态下可以编辑 Python 代码
	<font color=#1BC3FB>STOP</font>：中止项目的运行
	<font color=#1BC3FB>CHECKING</font>：当一个运行中的项目被编辑时项目状态会被自动设置成此状态并中止运行
	<font color=#1BC3FB>DEBUG</font>：会运行爬虫，顾名思义找 BUG，一般来说用于调试阶段
	<font color=#1BC3FB>RUNNING</font>：运行爬虫项目
	<font color=#1BC3FB>PAUSED</font>：项目暂停运行，默认没有这个状态，但是当你在运行过程中突然断网就会出现此状态

 - <font color=#ff0000>rate/burst</font>：当前的爬取速率，rate 代表 1 秒发出多少个请求，burst 相当于流量控制中的令牌桶算法的令牌数，rate 和 burst 设置的越大，爬取速率越快，速率的设定需要考虑本机性能和爬取过快被封的问题

 - <font color=#ff0000>avg time</font>：任务平均时间

 - <font color=#ff0000>process</font>：5m、1h、1d 分别指的是最近 5 分、1 小时、1 天内的请求情况，all 代表所有的请求情况，请求由不同颜色表示，蓝色的代表等待被执行的请求，绿色的代表成功的请求，黄色的代表请求失败后等待重试的请求，红色的代表失败次数过多而被忽略的请求

 - <font color=#ff0000>actions</font>：对爬虫项目的操作，各操作如下：
	<font color=#1BC3FB>Run</font>：立即执行任务，需要 status 为 RUNNING 或者 DEBUG 状态；假如在配置的调度执行时间内已经执行过，再点 run 是无效的，需要删除 task.db 里的数据才行
	<font color=#1BC3FB>Active Tasks</font>：查看当前爬虫项目的活动任务
	<font color=#1BC3FB>Results</font>：查看项目运行结果

---

## <font color=#1BC3FB>【17.2.2】项目界面</font>

创建一个爬虫项目，界面如下所示：

<fancybox>
![02](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A49/02.png)
</fancybox>

- <font color=#ff0000>创建项目</font>：点击 Create 即可新建一个爬虫项目
- <font color=#ff0000>Project Name</font>：爬虫项目名称
- <font color=#ff0000>Start URL(s)</font> ：爬虫入口地址，选填，可在项目中更改

项目创建完成进入调试界面：

<fancybox>
![03](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A49/03.png)
</fancybox>

- <font color=#ff0000>调试界面右边</font>：编写代码的区域

- <font color=#ff0000>调试界面左边</font>：调试的区域，用于执行代码，显示输出信息等用途

- <font color=#ff0000>run</font>：单步调试爬虫程序，点击就可运行当前任务

- <font color=#ff0000>< > 箭头</font>：上一步、下一步，用于调试过程中切换到上一步骤或者下一步骤

- <font color=#ff0000>save</font>：保存当前代码，当代码变更后只有保存了再运行才能得到最新结果

- <font color=#ff0000>enable css selector helper</font>： CSS 选择器辅助程序

- <font color=#ff0000>web</font>：页面预览

- <font color=#ff0000>html</font>：可以查看页面源代码

- <font color=#ff0000>follows</font>：表示爬取请求，点击可查看所有的请求

在新建一个爬虫项目的时候，pyspider 已经自动生成了如下代码：

```python
#!/usr/bin/env python
# -*- encoding: utf-8 -*-
# Created on 2019-09-17 21:18:13
# Project: 2

from pyspider.libs.base_handler import *


class Handler(BaseHandler):
    crawl_config = {
    }

    @every(minutes=24 * 60)
    def on_start(self):
        self.crawl('__START_URL__', callback=self.index_page)

    @config(age=10 * 24 * 60 * 60)
    def index_page(self, response):
        for each in response.doc('a[href^="http"]').items():
            self.crawl(each.attr.href, callback=self.detail_page)

    @config(priority=2)
    def detail_page(self, response):
        return {
            "url": response.url,
            "title": response.doc('title').text(),
        }
```

- <font color=#ff0000>class Handler()：</font>pyspider 爬虫的主类，可以在此处定义爬取、解析、存储的逻辑。整个爬虫的功能只需要一个 Handler 即可完成

- <font color=#ff0000>crawl_config 属性：</font>项目的所有爬取配置将会统一定义到这里，如定义 headers、设置代理等，配置之后全局生效

- <font color=#ff0000>on_start() 方法：</font>爬取入口，初始的爬取请求会在这里产生，该方法通过调用 `crawl()` 方法即可新建一个爬取请求，第一个参数是爬取的 URL，另一个参数 `callback` 指定了这个页面爬取成功后用哪个方法进行解析，默认指定为 `index_page()` 方法，即如果这个 URL 对应的页面爬取成功了，那 Response 将交给 `index_page()` 方法解析

- <font color=#ff0000>index_page() 方法：</font>接收 Response 参数，Response 对接了 pyquery。直接调用 `doc()` 方法传入相应的 CSS 选择器，就可以像 pyquery 一样解析此页面，代码中默认是 `a[href^="http"]`，即解析页面的所有链接，然后将链接遍历，再次调用了 `crawl()` 方法生成了新的爬取请求，同时再指定了 callback 为 detail_page，表示这些页面爬取成功了就调用 `detail_page()` 方法解析。`index_page()` 实现了两个功能，一是将爬取的结果进行解析，二是生成新的爬取请求

- <font color=#ff0000>detail_page() 方法：</font>同样接收 Response 作为参数。`detail_page()` 抓取的就是详情页的信息，就不会生成新的请求，只对 Response 对象做解析，解析之后将结果以字典的形式返回。当然也可以进行后续处理，如将结果保存到数据库等操作

PS：pyspider 默认的 web 预览页面窗口较小，可以找到 pyspider 文件夹有个 debug.min.css 文件（如：E:\Python\Lib\site-packages\pyspider\webui\static\debug.min.css），搜索 iframe，将原样式：`iframe{border-width:0;width:100%}` 改为 `iframe{border-width:0;width:100%;height:400px !important}` 即可，清除浏览器缓存后就会生效！

---

# <font color=#ff0000>【17.3】使用 pyspider 爬取去哪儿网</font>

爬取地址：http://travel.qunar.com/travelbook/list.htm
爬取目标：去哪儿网旅游攻略，发帖作者、标题、正文等

---

## <font color=#1BC3FB>【17.3.1】爬取首页</font>

创建一个名为 qunar 的爬虫项目，Start URL 设置为 http://travel.qunar.com/travelbook/list.htm ，点击 run 出现一个爬取请求

<fancybox>
![04](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A49/04.png)
</fancybox>

左边调试区域出现以下代码：

```python
{
  "process": {
    "callback": "on_start"
  },
  "project": "qunar",
  "taskid": "data:,on_start",
  "url": "data:,on_start"
}
```
callback 为 on_start，表示此时执行了 `on_start()` 方法。在 `on_start()` 方法中，利用 `crawl()` 方法即可生成一个爬取请求，点击 index_page 链接后面的箭头会出现许多新的爬取请求，即首页所包含的所有链接

<fancybox>
![05](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A49/05.png)
</fancybox>

此时左边调试区域代码变为：

```python
{
  "fetch": {},
  "process": {
    "callback": "index_page"
  },
  "project": "qunar",
  "schedule": {
    "age": 864000
  },
  "taskid": "73a789f99528a2bdc3ab83a13902962a",
  "url": "http://travel.qunar.com/travelbook/list.htm"
}
```

callback 变为了 index_page，表示此时执行了 `index_page()` 方法。传入 `index_page()` 方法的 response 参数为刚才生成的第一个爬取请求的 response 对象，然后调用 `doc()` 方法，传入提取所有 a 节点的 CSS 选择器，获取 a 节点的属性 href，实现了页面所有链接的提取，随后遍历所有链接，调用 `crawl()` 方法，把每个链接构造成新的爬取请求，可以看到 follows 新生成了 229 个爬取请求。点击 web 按钮可以直接预览当前页面，点击 html 按钮可以查看此页面源代码

---

## <font color=#1BC3FB>【17.3.2】信息匹配</font>

代码 `for each in response.doc('a[href^="http"]').items():` 实现了对整个页面链接的获取，我们需要提取网页的攻略的标题，内容等信息，那么直接替换 `doc()` 方法里的匹配语句即可，pyspider 提供了非常方便的 CSS 选择器，点击 `enable css selector helper` 按钮后，选择要匹配的信息并点击，再点击箭头 add to editor 即可得到匹配语句

<fancybox>
![06](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A49/06.png)
</fancybox>

完成了 CSS 选择器的替换，点击 `save` 保存，再次点击 `run` 重新执行 `index_page()` 方法，可以看到 follows 变为了 10 个，即抓取到了 10 篇攻略

---

## <font color=#1BC3FB>【17.3.3】抓取下一页数据</font>

每一页只有 10 篇攻略，想要爬取所有页面的攻略，必须要得到下一页的数据，优化 `index_page()` 方法：

```python
    @config(age=10 * 24 * 60 * 60)
    def index_page(self, response):
        for each in response.doc('li > .tit > a').items():
            self.crawl(each.attr.href, callback=self.detail_page)
        next = response.doc('.next').attr.href
        self.crawl(next, callback=self.index_page)
```

匹配下一页按钮，获取下一页按钮的 URL 并赋值给 next，将该 URL 传给 `crawl()` 方法，指定回调函数为 `index_page()` 方法，这样会再次调用 `index_page()` 方法，提取下一页的攻略标题

---

## <font color=#1BC3FB>【17.3.4】抓取JS渲染数据</font>

随便点击一个获取到的攻略，预览该页面，可以观察到头图一直在加载中，切换到 html 查看源代码页面，可以观察到没有 img 节点，那么此处就是后期经过 JavaScript 渲染后才出现的

<fancybox>
![07](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A49/07.png)
</fancybox>

针对 JavaScript 渲染页面，可以通过 PhantomJS 来实现，具体到 pyspider 中，只需要在 `index_page()` 的 `crawl()` 抓取方法中添加一个参数 `fetch_type` 即可：

```python
    @config(age=10 * 24 * 60 * 60)
    def index_page(self, response):
        for each in response.doc('li > .tit > a').items():
            self.crawl(each.attr.href, callback=self.detail_page, fetch_type='js')
        next = response.doc('.next').attr.href
        self.crawl(next, callback=self.index_page)
```

保存之后再次运行即可看到正常页面

---

## <font color=#1BC3FB>【17.3.5】抓取所有数据</font>

改写 `detail_page()` 方法，同样通过 CSS 选择器提取 URL、标题、日期、作者、正文、图片等信息：

```python
    @config(priority=2)
    def detail_page(self, response):
        return {
            'url': response.url,
            'title': response.doc('#booktitle').text(),
            'date': response.doc('.when .data').text(),
            'day': response.doc('.howlong .data').text(),
            'who': response.doc('.who .data').text(),
            'text': response.doc('#b_panel_schedule').text(),
            'image': response.doc('.cover_img').attr.src
        }
```

---

## <font color=#1BC3FB>【17.3.6】启动爬虫项目</font>

该爬虫项目完整代码如下：

```python
#!/usr/bin/env python
# -*- encoding: utf-8 -*-
# Created on 2019-09-18 09:48:29
# Project: qunar

from pyspider.libs.base_handler import *


class Handler(BaseHandler):
    crawl_config = {
    }

    @every(minutes=24 * 60)
    def on_start(self):
        self.crawl('http://travel.qunar.com/travelbook/list.htm', callback=self.index_page)

    @config(age=10 * 24 * 60 * 60)
    def index_page(self, response):
        for each in response.doc('li > .tit > a').items():
            self.crawl(each.attr.href, callback=self.detail_page, fetch_type='js')
        next = response.doc('.next').attr.href
        self.crawl(next, callback=self.index_page)

    @config(priority=2)
    def detail_page(self, response):
        return {
            'url': response.url,
            'title': response.doc('#booktitle').text(),
            'date': response.doc('.when .data').text(),
            'day': response.doc('.howlong .data').text(),
            'who': response.doc('.who .data').text(),
            'text': response.doc('#b_panel_schedule').text(),
            'image': response.doc('.cover_img').attr.src
        }
```

保存代码后，回到主界面，将项目 status 修改为 RUNNING ，点击 actions 的 run 按钮即可启动爬虫

<fancybox>
![08](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A49/08.png)
</fancybox>

点击 Active Tasks，即可查看最近请求的详细状况：

<fancybox>
![09](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A49/09.png)
</fancybox>

点击 Results，即可查看所有的爬取结果：

<fancybox>
![10](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A49/10.png)
</fancybox>

另外，右上角还可以选择 JSON、CSV 格式

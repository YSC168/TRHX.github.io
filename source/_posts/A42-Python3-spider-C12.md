---
title: Python3 爬虫学习笔记 C12
tags:
  - 爬虫
  - 图形验证码
categories: 
  - Python3 学习笔记
  - 爬虫学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/spider.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---
> <center><font color=#1BC3FB size=4>Python3 爬虫学习笔记第十二章 —— 【验证码对抗系列 — 图形验证码】</font></center>

<!--more-->

# <font color=#ff0000>【12.1】关于普通图形验证码</font>

普通图形验证码一般由四位纯数字、纯字母或者字母数字组合构成，是最常见的验证码，也是最简单的验证码，利用 tesserocr 或者 pytesseract 库即可识别此类验证码，前提是已经安装好 Tesseract-OCR 软件

<fancybox>
![01](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A42/01.png)
</fancybox>

# <font color=#ff0000>【12.2】tesserocr 库识别验证码</font>

简单示例：

```python
import tesserocr
from PIL import Image

image = Image.open('code.png')
result = tesserocr.image_to_text(image)
print(result)
```

新建一个 Image 对象，调用 tesserocr 的 `image_to_text()` 方法，传入 Image 对象即可完成识别，另一种方法：

```python
import tesserocr
print(tesserocr.file_to_text('code.png'))
```

# <font color=#ff0000>【12.3】pytesseract 库识别验证码</font>

简单示例：

```python
import pytesseract
from PIL import Image

img = Image.open('code.png')
img = img.convert('RGB')
img.show()
print(pytesseract.image_to_string(img))
```

pytesseract 的各种方法：

- get_tesseract_version：返回 Tesseract 的版本信息；
- image_to_string：将图像上的 Tesseract OCR 运行结果返回到字符串；
- image_to_boxes：返回包含已识别字符及其框边界的结果；
- image_to_data：返回包含框边界，置信度和其他信息的结果。需要 Tesseract 3.05+；
- image_to_osd：返回包含有关方向和脚本检测的信息的结果。

有关参数：

`image_to_data(image, lang='', config='', nice=0, output_type=Output.STRING)`

- image：图像对象；
- lang：Tesseract 语言代码字符串；
- config：任何其他配置为字符串，例如：config='--psm 6'；
- nice：修改 Tesseract 运行的处理器优先级。Windows不支持。尼斯调整了类似 unix 的流程的优点；
- output_type：类属性，指定输出的类型，默认为string。

lang 参数，常见语言代码如下：

- chi_sim：简体中文
- chi_tra：繁体中文
- eng：英文
- rus：俄罗斯语
- fra：法语
- deu：德语
- jpn：日语

# <font color=#ff0000>【12.4】验证码处理</font>

利用 Image 对象的 `convert()` 方法传入不同参数可以对验证码做一些额外的处理，如转灰度、二值化等操作，经过处理过后的验证码会更加容易被识别，识别准确度更高，各种参数及含义：

- 1：1位像素，黑白，每字节一个像素存储；
- L：8位像素，黑白；
- P：8位像素，使用调色板映射到任何其他模式；
- RGB：3x8位像素，真彩色；
- RGBA：4x8位像素，带透明度掩模的真彩色；
- CMYK：4x8位像素，分色；
- YCbCr：3x8位像素，彩色视频格式；
- I：32位有符号整数像素；
- F：32位浮点像素。

示例：

```python
import pytesseract
from PIL import Image

image = Image.open('code.png')
image = image.convert('L')
image.show()
result = pytesseract.image_to_string(image)
print(result)
```

Image 对象的 `convert()` 方法参数传入 L，即可将图片转化为灰度图像，转换前后对比：

<fancybox>
![02](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A42/02.png)
</fancybox>

```python
import pytesseract
from PIL import Image

image = Image.open('code.png')
image = image.convert('1')
image.show()
result = pytesseract.image_to_string(image)
print(result)
```

Image 对象的 `convert()` 方法参数传入 1，即可将图片进行二值化处理，处理前后对比：

<fancybox>
![03](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A42/03.png)
</fancybox>

# <font color=#ff0000>【12.5】tesserocr 与 pytesserocr 相关资料</font>

- tesserocr GitHub：https://github.com/sirfz/tesserocr

- tesserocr PyPI：https://pypi.python.org/pypi/tesserocr

- pytesserocr GitHub：https://github.com/madmaze/pytesseract

- pytesserocr PyPI：https://pypi.org/project/pytesseract/

- Tesseract-OCR 下载地址：http://digi.bib.uni-mannheim.de/tesseract

- tesseract GitHub：https://github.com/tesseract-ocr/tesseract

- tesseract 语言包：https://github.com/tesseract-ocr/tessdata

- tesseract 文档：https://github.com/tesseract-ocr/tesseract/wiki/Documentation
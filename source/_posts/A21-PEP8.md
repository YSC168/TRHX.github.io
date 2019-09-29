---
title: Python PEP8 代码规范常见问题及解决方法
tags: 
  - Python
  - PEP8
categories: 
  - Python3 学习笔记
  - 学习经验
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/python.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---

之前一直用 Python  IDLE 写代码，最近换成 PyCharm 写代码总是会出现波浪号，这才了解到 Python 的 PEP8 代码规范，所以将常见的 PEP8 代码规范问题和解决方法记录一下，学习一下，遇到了再持续更新，<font color=#FF0000>养成良好的习惯，编写规范的代码！</font>

<!-- more -->

---

 - <font color=#FF0000>PEP 8: no newline at end of file</font>
解决方法：代码末尾需要另起一行，光标移到最后回车即可

 - <font color=#FF0000>PEP 8: indentation is not a multiple of four</font>
 解决方法：缩进不是4的倍数，检查缩进
 
 - <font color=#FF0000>PEP 8: over-indented</font>
 解决方法：过度缩进，检查缩进

 - <font color=#FF0000>PEP 8: missing whitespace after','</font>
 解决方法：逗号后面少了空格，添加空格即可，类似还有分号或者冒号后面少了空格

 - <font color=#FF0000>PEP 8: multiple imports on one line</font>
 解决方法：不要在一句 import 中引用多个库，举例：``import socket, urllib.error``最好写成：`import socket`   `import urllib.error`

 - <font color=#FF0000>PEP 8: blank line at end of line</font>
解决方法：代码末尾行多了空格，删除空格即可

 - <font color=#FF0000>PEP 8: at least two spaces before inline comment</font>
解决方法：代码与注释之间至少要有两个空格

 - <font color=#FF0000>PEP 8: block comment should start with '#'</font>
解决方法：注释要以#加一个空格开始

 - <font color=#FF0000>PEP 8: inline comment should start with '#'</font>
解决方法：注释要以#加一个空格开始

 - <font color=#FF0000>PEP 8: module level import not at top of file</font>
解决方法：import不在文件的最上面，可能之前还有其它代码

 - <font color=#FF0000>PEP 8: expected 2 blank lines，found 0</font>
解决方法：需要两条空白行，添加两个空白行即可

 - <font color=#FF0000>PEP 8: function name should be lowercase</font>
解决方法：函数名改成小写即可

 - <font color=#FF0000>PEP 8: missing whitespace around operator</font>
解决方法：操作符（'='、'>'、'<'等）前后缺少空格，加上即可

 - <font color=#FF0000>PEP 8: unexpected spaces around keyword / parameter equals</font>
解决方法：关键字/参数等号周围出现意外空格，去掉空格即可

 - <font color=#FF0000>PEP 8: multiple statements on one line (colon)</font>
解决方法：多行语句写到一行了，比如：`if x == 2: print('OK')`要分成两行写

 - <font color=#FF0000>PEP 8: line too long (82 > 79 characters)</font>
 解决方法：超过了每行的最大长度限制79

---

如果想要选择性忽略PEP8代码风格的警告信息可以使用以下方法：（养成良好的习惯，编写规范的代码！不推荐忽略！）

①将鼠标移到出现警告信息的地方，按 <font color=#FF0000>alt+Enter</font>，选择忽略（Ignore）这个错误即可：
![01](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A21/01.png)
②依次选择 <font color=#FF0000>File - Settings - Editor - Inspections</font>，在 Python下找到 <font color=#FF0000>PEP8 coding style violation</font> 选项，在右下角的 <font color=#FF0000>Ignore errors </font>里点击加号可以添加需要忽略的警告信息ID（ID信息见后面附录），例如想要忽略`indentation contains mixed spaces and tabs`这个警告，只需要添加其ID：<font color=#FF0000>E101</font> 即可
![02](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A21/02.png)
附录：全部警告信息以及对应的ID，官方地址：https://pep8.readthedocs.io/en/latest/intro.html#error-codes

code | sample message
:-: | :-:
<font color=#FF0000>E1</font> | <font color=#FF0000>Indentation</font>
E101 | indentation contains mixed spaces and tabs
E111 | indentation is not a multiple of four
E112 | expected an indented block
E113 | unexpected indentation
E114 | indentation is not a multiple of four (comment)
E115 | expected an indented block (comment)
E116 | unexpected indentation (comment)
E117 | over-indented
E121 (*^) | continuation line under-indented for hanging indent
E122 (^) | continuation line missing indentation or outdented
E123 (*) | closing bracket does not match indentation of opening bracket’s line
E124 (^) | closing bracket does not match visual indentation
E125 (^) | continuation line with same indent as next logical line
E126 (*^) | continuation line over-indented for hanging indent
E127 (^) | continuation line over-indented for visual indent
E128 (^) | continuation line under-indented for visual indent
E129 (^) | visually indented line with same indent as next logical line
E131 (^) | continuation line unaligned for hanging indent
E133 (*) | closing bracket is missing indentation
<font color=#FF0000>E2</font> | <font color=#FF0000>Whitespace</font>
E201 | whitespace after ‘(‘
E202 | whitespace before ‘)’
E203 | whitespace before ‘:’
E211 | whitespace before ‘(‘
E221 | multiple spaces before operator
E222 | multiple spaces after operator
E223 | tab before operator
E224 | tab after operator
E225 | missing whitespace around operator
E226 (*) | missing whitespace around arithmetic operator
E227 | missing whitespace around bitwise or shift operator
E228 | missing whitespace around modulo operator
E231 | missing whitespace after ‘,’, ‘;’, or ‘:’
E241 (*) | multiple spaces after ‘,’
E242 (*) | tab after ‘,’
E251 | unexpected spaces around keyword / parameter equals
E261 | at least two spaces before inline comment
E262 | inline comment should start with ‘# ‘
E265 | block comment should start with ‘# ‘
E266 | too many leading ‘#’ for block comment
E271 | multiple spaces after keyword
E272 | multiple spaces before keyword
E273 | tab after keyword
E274 | tab before keyword
E275 | missing whitespace after keyword
<font color=#FF0000>E3</font> | <font color=#FF0000>Blank line</font>
E301 | expected 1 blank line, found 0
E302 | expected 2 blank lines, found 0
E303 | too many blank lines (3)
E304 | blank lines found after function decorator
E305 | expected 2 blank lines after end of function or class
E306 | expected 1 blank line before a nested definition
<font color=#FF0000>E4</font> | <font color=#FF0000>Import</font>
E401 | multiple imports on one line
E402 | module level import not at top of file
<font color=#FF0000>E5</font> | <font color=#FF0000>Line length</font>
E501 (^) | line too long (82 > 79 characters)
E502 | the backslash is redundant between brackets
<font color=#FF0000>E7</font> | <font color=#FF0000>Statement</font>
E701 | multiple statements on one line (colon)
E702 | multiple statements on one line (semicolon)
E703 | statement ends with a semicolon
E704 (*) | multiple statements on one line (def)
E711 (^) | comparison to None should be ‘if cond is None:’
E712 (^) | comparison to True should be ‘if cond is True:’ or ‘if cond:’
E713 | test for membership should be ‘not in’
E714 | test for object identity should be ‘is not’
E721 (^) | do not compare types, use ‘isinstance()’
E722 | do not use bare except, specify exception instead
E731 | do not assign a lambda expression, use a def
E741 | do not use variables named ‘l’, ‘O’, or ‘I’
E742 | do not define classes named ‘l’, ‘O’, or ‘I’
E743 | do not define functions named ‘l’, ‘O’, or ‘I’
<font color=#FF0000>E9</font> | <font color=#FF0000>Runtime</font>
E901 | SyntaxError or IndentationError
E902 | IOError
<font color=#FF0000>W1</font> | <font color=#FF0000>Indentation warning</font>
W191 | indentation contains tabs
<font color=#FF0000>W2</font> | <font color=#FF0000>Whitespace warning</font>
W291 | trailing whitespace
W292 | no newline at end of file
W293 | blank line contains whitespace
<font color=#FF0000>W3</font> | <font color=#FF0000>Blank line warning</font>
W391 | blank line at end of file
<font color=#FF0000>W5</font> | <font color=#FF0000>Line break warning</font>
W503 (*) | line break before binary operator
W504 (*) | line break after binary operator
W505 (*^) | doc line too long (82 > 79 characters)
<font color=#FF0000>W6</font> | <font color=#FF0000>Deprecation warning</font>
W601 | .has_key() is deprecated, use ‘in’
W602 | deprecated form of raising exception
W603 | ‘<>’ is deprecated, use ‘!=’
W604 | backticks are deprecated, use ‘repr()’
W605 | invalid escape sequence ‘x’
W606 | ‘async’ and ‘await’ are reserved keywords starting with Python 3.7
---
title: Python3 爬虫学习笔记 C10
tags:
  - 爬虫
  - MySQL
categories: 
  - Python3 学习笔记
  - 爬虫学习
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/spider.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---

> <center><font color=#1BC3FB size=4>Python3 爬虫学习笔记第十章 —— 【MySQL数据储存】</font></center>

<!--more-->

# <font color=#ff0000>【10.1】MySQL 基本操作语句</font>

安装完 MySQL 后，打开 MySQL x.x Command Line Client - Unicode，输入密码即可登录 MySQL，也可在 MySQL 安装目录下打开 cmd 使用命令登录数据库

## 数据库操作

```sql
# 连接数据库
mysql -u root -p

# 退出数据库
exit

# 查看所有的数据库
SHOW DATABASES;

# 创建一个数据库
CREATE DATABASE X;

# 删除一个数据库
DROP DATABASE IF EXISTS X;

# 使用这个数据库
USE X;
```

## 表操作

```sql
# 查看所有的表
SHOW TABLES ;

# 创建一个表
CREATE TABLE n(id INT, name VARCHAR(10));
CREATE TABLE m(id INT, name VARCHAR(10), PRIMARY KEY (id), FOREIGN KEY (id) REFERENCES n(id), UNIQUE (name));
CREATE TABLE m(id INT, name VARCHAR(10));

# 直接将查询结果导入或复制到新创建的表
CREATE TABLE n SELECT * FROM m;

# 新创建的表与一个存在的表的数据结构类似
CREATE TABLE m LIKE n;

# 创建一个临时表
# 临时表将在你连接MySQL期间存在。当断开连接时，MySQL将自动删除表并释放所用的空间。也可手动删除。
CREATE TEMPORARY TABLE l(id INT, name VARCHAR(10));

# 直接将查询结果导入或复制到新创建的临时表
CREATE TEMPORARY TABLE tt SELECT * FROM n;

# 删除一个存在表
DROP TABLE IF EXISTS m;

# 更改存在表的名称
ALTER TABLE n RENAME m;
RENAME TABLE n TO m;

# 查看表的结构(以下五条语句效果相同）
DESC n;
DESCRIBE n;
SHOW COLUMNS IN n;
SHOW COLUMNS FROM n;
EXPLAIN n;

# 查看表的创建语句
SHOW CREATE TABLE n;
```

## 表的结构

```sql
# 添加字段
ALTER TABLE n ADD age VARCHAR(2);

# 添加字段时设定位置
ALTER TABLE n ADD age VARCHAR(2)  FIRST;
ALTER TABLE n ADD age VARCHAR(2)  AFTER name;

# 修改字段在表中的位置
ALTER TABLE n MODIFY age VARCHAR(2) AFTER name;

# 删除字段
ALTER TABLE n DROP age;

# 更改字段属性和属性
ALTER TABLE n CHANGE age a INT;

# 只更改字段属性
ALTER TABLE n MODIFY age VARCHAR(7) ;

# 改变表的存储引擎
ALTER TABLE t ENGINE myisam;
ALTER TABLE t ENGINE innodb;

# 设定自增 初始为1，只能一个字段使用，该字段为主键的一部分
ALTER TABLE t AUTO_INCREMENT = 0;
```

## 表的数据

```sql
# 增加数据
INSERT INTO n VALUES (1, 'tom', '23'), (2, 'john', '22');
INSERT INTO n SELECT * FROM n;  # 把数据复制一遍重新插入

# 删除数据
DELETE FROM n WHERE id = 2;

# 更改数据
UPDATE n SET name = 'tom' WHERE id = 2;

# 数据查找
SELECT * FROM n WHERE name LIKE '%h%';

# 数据排序(反序)
SELECT * FROM n ORDER BY name, id DESC ;
```

# <font color=#ff0000>【10.2】Python 连接 MySQL</font>

```python
import pymysql

db = pymysql.connect(host='localhost', user='root', password='000000', port=3306)
cursor = db.cursor()
cursor.execute('SELECT VERSION()')
data = cursor.fetchone()
print('Database version:', data)
cursor.execute("CREATE DATABASE spiders DEFAULT CHARACTER SET utf8mb4")
db.close()
```

通过 PyMySQL 的 connect 方法声明一个 MySQL 连接对象 db，当前 MySQL 数据库运行在本地，设定 `host='localhost'`，用户名为 `root`，登录密码为 `000000`，运行在 `3306` 端口，调用 `cursor()` 方法获得 MySQL 的操作游标，该游标用来执行 SQL 语句，通过游标操作 `execute()` 方法写入 SQL 语句，第一条 SQL 语句获取 MySQL 的版本信息，调用 `fetchone()` 方法获得第一条数据，即 MySQL 的版本号。第二条 SQL 语句执行创建 spiders 数据库的操作，编码为 utf8mb4，运行该段代码将输出 MySQL 的版本号：

```python
Database version: ('8.0.17',)
```

# <font color=#ff0000>【10.3】创建表</font>

```python
import pymysql

db = pymysql.connect(host='localhost', user='root', password='000000', port=3306, db='spiders')
cursor = db.cursor()
sql = 'CREATE TABLE IF NOT EXISTS students (id VARCHAR(255) NOT NULL, name VARCHAR(255) NOT NULL, age VARCHAR(255) NOT NULL, PRIMARY KEY (id))'
cursor.execute(sql)
db.close()
```

该段代码实现了在 spiders 数据库里创建了一个名为 students 的表，包含 id、name、age 三个字段，类型依次为 varchar、varchar、int

# <font color=#ff0000>【10.4】插入数据</font>

```python
import pymysql

id = '17110105'
user = 'TRH'
age = 20
db = pymysql.connect(host='localhost', user='root', password='000000', port=3306, db='spiders')
cursor = db.cursor()
sql = 'INSERT INTO students(id, name, age) values(%s, %s, %s)'
try:
    cursor.execute(sql, (id, user, age))
    db.commit()
except:
    db.rollback()
db.close()
```

`commit()` 方法的作用是实现数据插入，是真正将语句提交到数据库执行的方法，使用 `try except` 语句实现异常处理，如果执行失败，则调用 `rollback()` 方法执行数据回滚，保证原数据不被破坏，使用查询语句可以看到已经插入的数据：

<fancybox>
![01](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A40/01.jpg)
</fancybox>

进阶操作：将需要插入的数据构造成一个字典，这样的做法可以让插入方法无需改动，只需要传入一个动态变化的字典就行了，改写原来的代码如下：

```python
import pymysql

data = {
    'id': '17110105',
    'name': 'TRH',
    'age': 20
}
table = 'students'
keys = ', '.join(data.keys())
values = ', '.join(['%s']*len(data))
db = pymysql.connect(host='localhost', user='root', password='000000', port=3306, db='spiders')
cursor = db.cursor()
sql = 'INSERT INTO {table}({keys}) VALUES ({values})'.format(table=table, keys=keys, values=values)
try:
    cursor.execute(sql, tuple(data.values()))
    print('数据插入成功！')
    db.commit()
except:
    print('数据插入失败！')
    db.rollback()
db.close()
```

传入的数是字典，将其定义为 data 变量，表名定义成变量 table，构造插入的字段 id、name 和 age。` ', '.join(data.keys())`  的结果就是 id, name, age，接着需要构造多个 `%s` 当作占位符，有三个字段，就需要构造 `%s, %s, %s`。首先定义长度为 1 的数组 `['%s']`，然后用乘法将其扩充为 `['%s', '%s', '%s']`，再调用 `join()` 方法，最终变成 `%s, %s, %s`。再利用字符串的 `format()` 方法将表名、字段名和占位符构造出来。最终的 SQL 语句就被动态构造成了如下语句：

```python
INSERT INTO students(id, name, age) VALUES (%s, %s, %s)
```

# <font color=#ff0000>【10.5】更新数据</font>

```python
import pymysql

data = {
    'id': '17110105',
    'name': 'TRH',
    'age': 21
}
table = 'students'
keys = ', '.join(data.keys())
values = ', '.join(['%s']*len(data))
db = pymysql.connect(host='localhost', user='root', password='000000', port=3306, db='spiders')
cursor = db.cursor()
sql = 'INSERT INTO {table}({keys}) VALUES ({values}) ON DUPLICATE KEY UPDATE'.format(table=table, keys=keys, values=values)
update = ','.join(["{key} = % s".format(key=key) for key in data])
sql += update
try:
    if cursor.execute(sql, tuple(data.values())*2):
        print('数据插入成功！')
        db.commit()
except:
    print('数据插入失败！')
    db.rollback()
db.close()
```

`ON DUPLICATE KEY UPDATE` 表示如果主键已经存在，就执行更新操作，最终被构造成如下语句：

```python
INSERT INTO students(id, name, age) VALUES (% s, % s, % s) ON DUPLICATE KEY UPDATE id = % s, name = % s, age = % s
```

# <font color=#ff0000>【10.6】删除数据</font>

```python
import pymysql

table = 'students'
condition = 'age = 20'
db = pymysql.connect(host='localhost', user='root', password='000000', port=3306, db='spiders')
cursor = db.cursor()
sql = 'DELETE FROM  {table} WHERE {condition}'.format(table=table, condition=condition)
try:
    cursor.execute(sql)
    print('数据删除成功！')
    db.commit()
except:
    print('数据删除失败！')
    db.rollback()
db.close()
```

删除操作直接使用 `DELETE` 语句，指定要删除的目标表名和删除条件即可

# <font color=#ff0000>【10.7】查询数据</font>

```python
import pymysql

table = 'students'
db = pymysql.connect(host='localhost', user='root', password='000000', port=3306, db='spiders')
cursor = db.cursor()
sql = 'SELECT * FROM students WHERE age >= 20'
try:
    cursor.execute(sql)
    print('Count:', cursor.rowcount)
    one = cursor.fetchone()
    print('One:', one)
    results = cursor.fetchall()
    print('Results:', results)
    print('Results Type:', type(results))
    for row in results:
        print(row)
except:
    print('查询失败！')
```

`sql = 'SELECT * FROM students WHERE age >= 20'`：构造一条 SQL 语句，将年龄 大于等于20 岁的学生查询出来

`cursor.rowcount`：调用 cursor 的 rowcount 属性获取查询结果的条数

`cursor.fetchone()`：调用 cursor 的 fetchone() 方法，获取结果的第一条数据，返回结果是元组形式，元组的元素顺序跟字段一一对应，即第一个元素就是第一个字段 id，第二个元素就是第二个字段 name，以此类推

`cursor.fetchall()`：调用 cursor 的 fetchall() 方法，得到结果的所有数据，它是二重元组，每个元素都是一条记录，本例中显示的是 3 条数据而不是 4 条，这是因为它的内部实现有一个偏移指针用来指向查询结果，最开始偏移指针指向第一条数据，取一次之后，指针偏移到下一条数据，这样再取的话，就会取到下一条数据了。我们最初调用了一次 fetchone 方法，这样结果的偏移指针就指向下一条数据，fetchall 方法返回的是偏移指针指向的数据一直到结束的所有数据，所以该方法获取的结果就只剩 3 个了

# <font color=#ff0000>【10.8】实战训练 — 爬取CSDN博客标题和地址保存到 MySQL</font>

利用 requests 库构建请求，BeautifulSoup 解析库解析网页，获取自己博客文章的标题和地址，将其储存到本地 MySQL 数据库中，事先已经创建好了一个 blog 数据库，并创建了一个名为 article 的数据表，数据表包含 id、title、url 三个字段，其中 id 的 `AUTO_INCREMENT` 属性可以使 id 自己增加，`PRIMARY KEY` 关键字用于将 id 定义为主键

创建 article 数据表：

```python
import pymysql

db = pymysql.connect(host='localhost', user='root', password='000000', port=3306, db='blog')
cursor = db.cursor()
sql = 'CREATE TABLE IF NOT EXISTS article (id INT NOT NULL AUTO_INCREMENT, title VARCHAR(255) NOT NULL, url VARCHAR(255) NOT NULL, PRIMARY KEY (id))'
cursor.execute(sql)
db.close()
```

获取文章标题和对应的 URL 并将其储存到 MySQL 中：

```python
import requests
import pymysql
from bs4 import BeautifulSoup

db = pymysql.connect(host='localhost', user='root', password='000000', port=3306, db='blog')
cursor = db.cursor()

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.86 Safari/537.36',
}
url = "https://blog.csdn.net/qq_36759224"

request = requests.get(url, headers=headers)
soup = BeautifulSoup(request.text, 'lxml')
title_list = soup.find_all('h4')
for list in title_list:
    s = list.a.text.strip()
    title = s.replace('原', '')
    url = list.a['href'].strip()
    # print(title + url)
    cursor.execute('INSERT INTO article (title, url) VALUES (%s, %s)', (title, url))
db.commit()
print('数据写入完毕！')
db.close()
```

在命令行中使用 ` SELECT * FROM article;` 命令可以查看到数据已经成功获取并储存到了数据库中：
<fancybox>
![02](https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/A40/02.jpg)
</fancybox>

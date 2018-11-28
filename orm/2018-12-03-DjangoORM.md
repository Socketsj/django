# Django ORM


### 一、Django ORM 简介

ORM即使是对象关系映射，通过对对象的操作映射到相对应数据持久化。ORM是一种程序技术，用于实现面向对象编程语言中不同类型系统数据之间转换。使得程序员在编码过程中，不必关注数据持久化问题，读取和写入仿佛都是在内存中进行的一样。

ORM解决了：面向对象是从软件工程基本原则（如耦合、聚合、封装）的基础上发展起来的，而关系数据库则是从数学理论发展而来的，两套理论存在显著的区别。为了解决这个不匹配的现象，对象关系映射技术应运而生

使用ORM好处：Django的orm操作本质上会根据对接的数据库引擎，翻译成对应的sql语句；所有使用Django开发的项目无需关心程序底层使用的是MySQL、Oracle、sqlite....，如果数据库迁移，只需要更换Django的数据库引擎即可

ORM三个原则：**简单性、传达性、精确性**。

简单性: 以最基本的形式建模数据。

传达性：数据库结构被任何人都能理解的语言文档化。

精确性：基于数据模型创建正确标准化的结构。

![Django ORM关系图](https://www.e-learn.cn/sites/default/files/ueditor/1/upload/image/20180717/1531833163476008.png)

### 二、Django ORM 使用

Django ORM 使用方法大致如下:

(1)在settings.py文件中配置目标数据库的信息

(2)在model.py中定义model模型

(3)根据model在目标数据库中构建数据表

(4)在view中通过对模型的操作实现对目标数据库的增删查改

#### 1. setting配置。
在settings.py中的DATABASES 定义数据库配置信息，默认会以sqlite作为数据库，例子以mysql为例子。
```python
DATABASES = {
	'default': {
		'ENGINE': 'django.db.backends.mysql',
		'NAME':'MyDjango',
		'USER': 'root',
		'PASSWORD': 'root',
		'HOST': '127.0.0.1',
		'PORT': '3306',
	},
}
```

#### 2. 模型定义。
模型以类的形式进行定义，并且继承Django的model.Model。

在ORM映射关系中一个类对应一个表，类的属性对应表的字段。类以及类属性定义有以下规定

(1) 类名一般以大写字母开头

(2) 类名和属性名不能是关键字，类的属性名不能有连续两个以上下划线(因为模型查询操作会用到)

(3) 定义属性时需要指定字段类型，通过字段类型的参数指定选项,语法: 属性名=models.字段类型(选项)

字段类型
```python
from django.db import models
class Book(models.Model):
    name = models.CharField(max_length=100)   # 书名
	pub_date = models.DateField()     # 发行日期
	read = models.IntegerField()    # 借阅数
	commit = models.IntegerField()      # 归还数

```
以上的代码通过定义一个book类来定义一个模型，

> * **tuple**：元组，和list非常类似，区别在于tuple一旦初始化就不能修改了。tuple的"不变性"：tuple中的每个元素，指向永远不变。
```Python
>>> t1 = (1, 'a', True)
>>> t1[0]
1
>>> t2 = (1,)
```

```python
>>> t = (1, 'a', [1,2])
>>> t[2] = [1, 2, 3]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
>>> t[2].append(3)
>>> t
(1, 'a', [1, 2, 3])
```

> * **dict**：字典，使用键值对存储，具有极快的查找速度。它是根据key通过哈希算法算出value的存放位置的，所以dict的key必须是**不可变对象**。
```python
>>> d = {1: 'a', 2: 'b', 3:'c'}
>>> d[1]
'a'
>>> d[4]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 4
>>> d.get(4, -1)
-1
```

> * **set**：是一个无序的集合，元素不重复、不可变。set可以做数学意义上的交集、并集等操作。
```python
>>> s = set([1, 2, 2, 3])
>>> s
{1, 2, 3}
```

|       | 是否有序  | 能否读写 | 能否重复  | 获取元素方式 |
|-------|----------|---------|-----------|-------------|
| list  | 有序     | 读写     | 是        | 索引        |
| tuple | 有序     | 只读     | 是        | 索引        |
| dict  | 无序     | 读写     | 键不能重复 | 不支持索引  |
| set   | 无序     | 读写     | 否        | 键          |

更多细节可参考[Python官方文档](https://docs.python.org/3.7/library/stdtypes.html)

#### 3. 高级特性

> * **列表生成式**：Python内置的简单却强大的可以快速创建list的生成式。
> * [列表生成式的基本使用](https://github.com/Klay-Lin/python_notes/blob/master/Python%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/%E5%88%97%E8%A1%A8%E7%94%9F%E6%88%90%E5%BC%8F.md)

> * **切片**:获取集合的指定索引范围内的元素，使用循环比较繁琐，Python提供了切片操作符，可以大大简化这种操作。
> * [切片的基本使用](https://github.com/Klay-Lin/python_notes/blob/master/Python%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/%E5%88%87%E7%89%87.md)

> * 如果要创建一个包含10万个元素的列表,使用列表生成式是不现实的。
> * 解决方案：不必创建出完整的list,可以在循环的过程中按照某种算法不断地推算出后面的元素,这样可以节省大量的内存空间。这种一边循环一边计算元素的机制，称为**生成器**。
> * **生成器(generator)**：一个**惰性计算**的有序数据流，在需要返回下一个数据时才会计算元素的值。
> * 创建一个generator主要有两种方法：第一种是把一个列表生成式的[]改成()，第二种是在函数定义中包含*yield*关键字。
> * **注意**：包含yield关键字的函数不是普通函数，而是生成器。它在每次调用next()时执行，遇到yield语句就返回，再次执行时从上次返回的yield语句处再继续执行。
> * [生成器的创建和使用](https://github.com/Klay-Lin/python_notes/blob/master/Python%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/%E7%94%9F%E6%88%90%E5%99%A8.md)

> * **高阶函数**：可以接收函数作为参数，也可以把函数作为返回值返回。
> * **装饰器**：如果我们想增强一个函数的功能，又不想修改该函数的定义，可以在代码运行期间动态增加功能，这种方式称为“装饰器”。decorator是一个返回函数的高阶函数。
> * [装饰器的定义](https://github.com/Klay-Lin/python_notes/blob/master/Python%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/%E8%A3%85%E9%A5%B0%E5%99%A8.md)

> * **定制类**：Python的class允许自定义许多定制方法来生成具备特定行为的类。
> * [常用的定制方法](https://github.com/Klay-Lin/python_notes/blob/master/Python%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/%E5%AE%9A%E5%88%B6%E7%B1%BB.md)
> * 更多的定制方法，可以参考[Python官方文档](https://docs.python.org/3/reference/datamodel.html#special-method-names)


### 二、Python常用工具

#### 1. pip

#### 什么是 pip

[PyPI](https://pypi.org/)(Python Package Index)是Python官方的第三方库网站，可以在上面搜索和下载开发所需的库，PyPI 推荐使用 pip 来下载第三方库。

**pip**(python install packages)是Python包管理工具，它提供了对Python包的查找、下载、安装、卸载的功能。

#### 安装 pip

Mac或Linux本身一般已安装 pip，Windows可以在安装Python时勾选 pip选项同时安装 pip。如果没有安装，可以使用以下命令进行安装：

    $ curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
    $ python get-pip.py

#### pip 常用命令

|   命令                          |   描述                 |
|---------------------------------|------------------------|
| pip --version                   | 显示版本和路径          |
| pip --help                      | 获取帮助信息            |
| pip install django==2.0.1       | 安装指定版本的包        |
| pip install --upgrade django    | 升级包                 |
| pip install –r requirements.txt | 安装指定文本中的包      |
| pip freeze > requirements.txt   | 导出当前已安装包到文本中 |
| pip show django                 | 显示安装包信息          |
| pip uninstall django            | 卸载包                 |
| pip list                        | 列出已安装的包          |

更多详细命令可参考[pip官方文档](https://pip.pypa.io/en/stable/reference/)

#### 2. virtualenv / virtualenvwrapper

#### 什么是 virtualenv

Python所有的第三方库都会被 pip 安装到Python的 **site-packages** 目录下。多个项目应用共享同一份Python运行环境，容易造成不同应用间的多版本冲突及难以维护的问题等。

**解决方案**：每个应用单独拥有一份独立的运行环境，互不干扰。

**virtualenv** 就是用来做这件事的，它是一个为某个应用创建一份独立的Python环境的工具。

[virtualenv的基本使用](https://github.com/Klay-Lin/python_notes/blob/master/Python%E5%B8%B8%E7%94%A8%E5%B7%A5%E5%85%B7/virtualenv.md)

#### virtualenv 的原理及缺点

**原理**：把系统Python环境复制一份到virtualenv环境下，当进入一个virtualenv环境时，virtualenv会修改相关的环境变量，让命令python和pip均指向当前的virtualenv环境。

**缺点**：virtualenv 创建的虚拟环境都是分散的，每次切换一个环境都需要进入到相应的目录并执行source命令来激活环境，不便于集中管理。

#### virtualenvwrapper 的使用

virtualenvwrapper 是管理虚拟环境的工具，它对 virtualenv 进行了包装，可以把所有虚拟环境都放到一个目录中，该目录通过 **WORKON_HOME** 变量进行设置。实际开发中建议使用 **virtualenvwrapper**。

[virtualenvwrapper的基本使用](https://github.com/Klay-Lin/python_notes/blob/master/Python%E5%B8%B8%E7%94%A8%E5%B7%A5%E5%85%B7/virtualenvwrapper.md)

[virtualenvwrapper详细文档](https://virtualenvwrapper.readthedocs.io/en/latest/)


### 三、WSGI接口

#### 1. 网关接口的发展历程

> * CGI：全称为Common Gateway Interface，适用于所有的脚本语言。当有请求时，web服务器会启动一个CGI进程并把请求信息传递给该CGI进程，CGI进程执行完后返回数据给web服务器并销毁该进程。
> * FastCGI：顾名思义，它是CGI的快速版本。它会在web服务器刚启动的时候预先启动若干常驻进程，当有请求时，直接从常驻进程中选取一个来执行，因为它无需反复启动和销毁进程，所以在效率上比CGI快。
> * WSGI：Python专有的接口规范协议。它支持多进程、多线程等方式来处理用户请求，从而进一步提高了并发效率。

|         | 优点                                 |                                |
|---------|--------------------------------------|--------------------------------|
| CGI     | 支持所有脚本语言、跨平台               | 开销大、并发处理能力低           |
| FastCGI | 处理能力提高，支持跨服务器通信          | 并发能力有限，没有统一的接口规范  |
| WSGI    | 达到了程序解耦的目的，支持多进程，多线程 | 不支持跨服务器通信              |

#### 2. WSGI接口
从客户端发送一个http请求到web应用程序处理请求，分别经过了web服务器、WSGI、web应用程序三个层次。

> * web服务器：接收请求、处理请求、返回响应
> * WSGI：连接web服务器和web应用程序，实现两者的通信
> * web应用程序：实现业务逻辑，返回动态数据

![WSGI](http://static.zybuluo.com/rainybowe/qmuk0e449mawtyfzs8brgm6g/wsgi.png)

WSGI：全称为Web Server Gateway Interface。WSGI协议主要包括server和application两部分，它定义了web服务器和web应用之间通信的接口规范。也就是说，只要web服务器和web应用都实现WSGI协议，那么web服务器和web应用就可以随意组合使用。

WSGI server和WSGI application的交互过程如下：
> * 1.WSGI server接收并处理客户端请求，将客户端信息、服务器信息和请求信息等都全部封装到环境变量environ中
> * 2.WSGI server将environ和回调函数传递给WSGI application
> * 3.WSGI application从environ变量中获取信息，通过调用回调函数返回状态码和响应头，同时返回响应体给WSGI server
> * 4..WSGI server将响应信息返回给客户端

Python内置了一个WSGI服务器，这个模块叫wsgiref。通过WSGI和wsgiref实现[最简单的web app](https://github.com/Klay-Lin/python_notes/blob/master/WSGI/WSGI.md)

#### 3. Java中的实现-Servlet



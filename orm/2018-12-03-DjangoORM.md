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

| 字段       | 描述            |
| :--------:   | :----------------:|
| **AutoField**  | 自动增长的IntegerField，通常不用指定，不指定时Django会自动创建属性名为id的自动增长属性。|
|**BooleanField**|布尔字段，值为True或False。|
|**NullBooleanField**|支持Null、True、False三种值。|
|**CharField**(max_length=最大长度)|字符串。参数max_length表示最大字符个数。必须设置该属性|
|**TextField**|大文本字段，一般超过4000个字符时使用。|
|**IntegerField**|整数|
|**DecimalField**(max_digits=None, decimal_places=None)|十进制浮点数。参数max_digits表示总位。参数decimal_places表示小数位数。|
|**FloatField**|浮点数。参数同上|
|**DateField**([auto_now=False, auto_now_add=False])|日期。 1)参数auto_now表示每次保存对象时，自动设置该字段为当前时间，用于"最后一次修改"的时间戳，它总是使用当前日期，默认为false。 2) 参数auto_now_add表示当对象第一次被创建时自动设置当前时间，用于创建的时间戳，它总是使用当前日期，默认为false。 3)参数auto_now_add和auto_now是相互排斥的，组合将会发生错误。|
|**TimeField**|时间，参数同DateField。|
|**DateTimeField**|日期时间，参数同DateField。|

选项

通过选项实现对字段的约束，选项如下：

| 选项       | 描述            |
| :--------:   | :----------------:|
| **default**  | 默认值。设置默认值。|
|**primary_key**|若为True，则该字段会成为模型的主键字段，默认值是False，一般作为AutoField的选项使用。|
|**unique**|如果为True, 这个字段在表中必须有唯一值，默认值是False。|
|**db_index**|若值为True, 则在表中会为此字段创建索引，默认值是False。|
|**db_column**|字段的名称，如果未指定，则使用属性的名称。|
|**null**|如果为True，表示允许为空，默认值是False|
|**blank**|如果为True，则该字段允许为空白，默认值是False。|
|**verbose_name**|在Admin站点管理设置字段的显示名称|
|**related_name**|关联对象反向引用描述符，用于多表查询，可解决一个数据表有两个外键指向同一个数据表出现重名问题|

**对比：null是数据库范畴的概念，blank是后台管理(admin)页面表单验证范畴的。**

在model.py中根据定义类变成模型，以下是以图书管理系统图书模型编写。
```python
from django.db import models
class Book(models.Model):
    name = models.CharField(max_length=100)   # 书名
	pub_date = models.DateField()     # 发行日期
	read = models.IntegerField()    # 借阅数
	commit = models.IntegerField()      # 归还数
    price = models.FloatField()      # 图书价格

```

#### 3. 构建模型。

完成模型的定义后，在目的数据库中使用Django管理工具manage.py完成

(1)初始化和检查模型更改命令
```Shell
python manage.py makemigrations
```
执行该命令后会在项目下的app目录生成migrations文件夹，并生成001_initial.py文件，该文件记录mode.py内容生成的数据表脚本代码。

(2)构建数据表
```Shell
python manage.py migrate
```
执行数据表的脚本代码生成数据表


#### 4. 模型操作

> * **查询操作**：通过模型类.objects属性可以调用如下函数，实现对模型类对应的数据表的查询。
> * [查询操作的基本使用](https://github.com/Socketsj/django/blob/master/orm/django%E6%9F%A5%E8%AF%A2%E5%87%BD%E6%95%B0.md)

> * **F对象**: F对象，为模型字段Filed的对象，代表模型操作。
> * [F对象的基本使用](https://github.com/Socketsj/django/blob/master/orm/djangoF%E5%AF%B9%E8%B1%A1.md)

> * **Q对象**: Q对象，用于查询时条件之间的逻辑关系。not and or，可以对Q对象进行 & | ~ 操作。。
> * [Q对象的基本使用](https://github.com/Socketsj/django/blob/master/orm/djangoQ%E5%AF%B9%E8%B1%A1.md)

> * **聚合函数**: Q对象，用于查询时条件之间的逻辑关系。not and or，可以对Q对象进行 & | ~ 操作。。
> * [聚合函数的基本使用](https://github.com/Socketsj/django/blob/master/orm/django%E8%81%9A%E5%90%88%E5%87%BD%E6%95%B0.md)

> * 更多django使用方法，可以参考[django官方文档](https://docs.djangoproject.com/en/1.11/ref/databases/)


### 三、Django ORM 进阶

#### 1. QuerySet

all, filter, exclude, order_by调用这些函数会产生一个查询集，QuerySet类对象可以继续调用上面的所有函数。

#### QuerySet特性

1）惰性查询：只有在实际使用查询集中的数据的时候才会发生对数据库的真正查询。

2）缓存：当使用的是同一个查询集时，第一次使用的时候会发生实际数据库的查询，然后把结果缓存起来，之后再使用这个查询集时，使用的是缓存中的结果。

#### QuerySet查询限制

可以对一个查询集进行取下标或者切片操作来限制查询集的结果。

对一个查询集进行切片操作会产生一个新的查询集，下标不允许为负数。

取出查询集第一条数据的两种方式:

| 方法      | 说明            |
| :--------:   | :----------------:|
|b[0]|如果b[0]不存在，会抛出IndexError异常|
|b[0:1].get()|如果b[0:1].get()不存在，会抛出DoesNotExist异常。|

使用Exists：判断Queryset中是否有数据

#### 2. 模型类关系

#### 一对多关系
例如: 作者和图书关系，一个作者可以编写多本书

```python
models.ForeignKey() # 定义在多的类中，例如定义在book中
```
#### 多对多关系
例如: 读者和图书关系，一个读者可以借多本书，一种图书也可以被多个读者借阅

```python
models.ManyToManyField() # 定义任一个类，会更加多对多关系生成关系表
```

#### 一对一关系
例如：图书和图书详细信息

```python
models.OneToOneField() # 定义任一个类
```

#### 3. 关联查询（一对多）
查询和对象关联的数据

在一对多关系中，一对应的类我们把它叫做一类，多对应的那个类我们把它叫做多类，我们把多类中定义的建立关联的类属性叫做关联属性。

例如通过作者来查询作者编写的书

```python
a = Author.objects.get(id=3)
a.book_set.all() # 获取作者所有图书
```

通过图书查询作者

```python
b = Book.objects.get(id=5)
b.author.name # 获取作者的名字
```

查询作者id为3的图书

```python
b = Book.objects.filter(author_id=3)
```

#### 总结

由一类的对象查询多类的时候：

    一类的对象.多类名小写_set.all() #查询所用数据

由多类的对象查询一类的时候：

    多类的对象.关联属性  #查询多类的对象对应的一类的对象

由多类的对象查询一类对象的id时候：

    多类的对象. 关联属性_id

#### 4. 管理器
在查询例子中，我们每次查询模型都会使用objects.get,.filter。objects是Django帮我自动生成的管理器对象，通过这个管理器可以实现对数据的查询。

objects是models.Manger类的一个对象。自定义管理器之后Django不再帮我们生成默认的objects管理器，通过继承models.Manger我们可以自定义一个管理

例如自定义图书信息管理，只筛选书名不为空的图书信息
```python
class BookInfoManager(models.Manager):
    '''图书模型管理器类'''

    # 1 改变查询的结果集
    def all(self):
        # 1 调用父类的all方法，获取所有数据
        books = super().all()
        # 2.对数据进行过滤
        books = books.filter(name_isnull=False)
        # 3 返回对象
        return books
```

添加额外方法

管理器类中定义一个方法帮我们操作模型类对应的数据表。

使用self.model()就可以创建一个跟自定义管理器对应的模型类对象。
```python
class BookInfoManager(models.Manager):
    '''图书模型管理器类'''

    # 1 改变查询的结果集
    def all(self):
        # 1 调用父类的all方法，获取所有数据
        books = super().all()
        # 2.对数据进行过滤
        books = books.filter(isDelete=False)
        # 3 返回对象
        return books

    # 2 封装函数：操作模型类对应的数据表(增删改查)

    def create_book(self, btitle, bpub_data):
        # 1 创建一个图书对象
        #  获取self所在的模型类
        model_class = self.model  # self.model 指向所属模型类的类名
        book = model_class()
        book.btitle = btitle
        book.bpub_data = bpub_data
        # 保存
        book.save()
        # 返回
        return book
```

#### 小结
![管理器关系图](https://www.e-learn.cn/sites/default/files/ueditor/1/upload/image/20180717/1531833166468725.png)

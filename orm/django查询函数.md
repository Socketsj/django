# django管理器查询基本使用

### 1. get函数
get函数返回数据表中符合条件的一条记录并组装成模型实例

参数可以是筛选条件，返回的记录必须只有一条且存在，否则会抛出MultipleObjectsReturned和DoesNotExist异常

获取图书id为3的图书
```python
>>> Book.objects.get(id=1)
<Book: Book.object>
```

### 2. all函数
返回模型类对应表格中的所有数据。返回值为QuerySet类型

获取所有图书信息
```python
>>> Book.objects.all()
QuerySet<[<Book: Book.object>]>
```

### 3. filter
返回满足条件的数据。返回值为QuerySet类型

参数可以组合如返回数据 
```python
>>> Book.objects.filter(id=1)
返回id为1的图书信息
>>> Book.objects.filter(id__in=[1,3,6])
返回id为1或3或6的图书信息
>>> Book.objects.filter(read__gt=3)
返回借阅数大于3的图书信息,小于用lt
>>> Book.objects.filter(name__contains='计算机')
返回书名中有计算机字的图书信息
>>> Book.objects.filter(name__endswith='实战')
返回以实战结尾书名的图书信息,开头用start_with
>>> Book.objects.filter(name__isnull=False)
返回书名不为空的图书信息
>>> Book.objects.filter(pub_date__year=1996)
返回出版日期为1996年的图书信息
```

### 4. exclude
返回不满足条件的数据。返回值为QuerySet类型

```python
>>> Book.objects.exclude(id=1) # 返回id不为3的图书信息
QuerySet<[<Book: Book.object>]>
```

### 5. order_by
操作QuerySet，对结果进行排序返回Queryset

```python
>>> Book.objects.all().order_by('id')
按id从小到大排序
```


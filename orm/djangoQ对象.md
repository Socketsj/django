# django F对象基本使用
F对象是代表模型字段的值，也就是说对于一些特殊的字段的操作，有以下使用场景

使用之前需要先导入：from django.db.models import F

### 1. update操作
一般orm操作中修改一个模型数据，先将模型数据取出来修改执行save()如下
```python
book = Book.objects.get(name='计算机科学概论')
book.read += 1
book.save()
```

如果想要使用update table set col = a where col2 = 3可以使用F对象如下
```python
book = Book.objects.filter(name='计算机科学概论').update(read=F('read')+1)
```

### 2. 用于类属性(字段)之间的比较
返回模型类对应表格中的所有数据。返回值为QuerySet类型

返回查询借阅数大于还书数的图书信息
```python
>>> Book.objects.filter(read__gt=F('commit'))
QuerySet<[<Book: Book.object>]>
```


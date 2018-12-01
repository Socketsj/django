# 聚合函数
aggregate：操作QuerySet获取聚合结果

使用之前需要先导入：from django.db.models import Sum,Count,Max,Min,Avg

### 查询所有图书的数目。
```python
>>> Book.objects.all().aggregate(book_number=Count('id'))
{'book_number': 13}
```

### 查询所有图书阅读量的总和。
```python
>>> Book.objects.all().aggregate(read_sum=Sum('read'))
{'read_sum': 65}
```

### 查询最高价格图书
```python
>>> Book.objects.all().aggregate(read_sum=Sum('read'))
{'read_sum': 65}
```

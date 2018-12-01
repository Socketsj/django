# django Q对象基本使用
Q对象是代表模型query对象，用于查询时条件之间的逻辑关系。not and or，可以对Q对象进行 & | ~ 操作

使用之前需要先导入：from django.db.models import Q

### and 逻辑
查询1996-01-01年后出版和借阅数大于3
```python
Book.objects.filter(id__gt=3, bread__gt=30)
Book.objects.filter(Q(id__gt=3)&Q(bread__gt=30))
```

### or 逻辑
查询借阅数大于5或还数大于5的图书信息
```python
Book.objects.filter(Q(read__gt=5)|Q(commit__gt=5))
```

### not 逻辑
查询id不为3的图书信息
```python
Book.objects.filter(~Q(id=3))
```

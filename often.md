# 数据库的'逻辑与(&)'查询

- 在Django源代码中的search中的数据库查询
- 用到的内置包 operator & functools
- 数据库的'逻辑与(&)'查询 -- Q
    - Model.objects.filter(Q() & Q())
- '逻辑或(|)'
    - Model.objects.filter(Q() | Q())

    ```python

    import operator
    # 注意: map/filter/reduce常用, 但是reduce被收录到functools包中
    from functools import reduce

    from django.db import models
    from django.db.models import Q

    class Info(models.Model):
        id = models.IntegerField(unique=True)
        sign = models.CharField(max_length=10, verbose_name='签名')

    def find_db():
        query_data = {
            'id': 1,
            'sign': 'ok'
        }
        queries = []
        for key, value in query_data.items():
            # 结果: orm_lookup='id__icontains'
            # queries = ['id__icontains=1', ]
            orm_lookup = '__'.join((key, 'icontains'))
            queries.append(Q(**{orm_lookup: value}))

        # operator.and_  ==> 逻辑与
        results = Info.objects.filter(reduce(operator.and_, queries)
    ```

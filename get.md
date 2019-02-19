### GET 方法


1. 后端代码

    ```python
    # schema/get.py
    class Query(object):
        all_info = graphene.List(InfoType)

        # List == Field: 
        # List 返回结果会是遍历所有查询结果
        # Field 返回结果只存在单个 (其中可添加参数, ex. pk)
        single_info = graphene.Field(InfoType, pk=graphene.Int())

        # 定义函数名的格式: resolve_字段 
        # **kwargs 传递参数
        # pk: 如果在字段中定义, 则方法参数中必含
        def resolve_all_info(self, info, **kwargs):
            return Info.objects.all()

        def resolve_single_info(self, info, pk):
            return Info.objects.get(id=pk)
    ```

    ```python
    # 项目下的总 schema.py
    from app.schema.get import Query
    class TQuery(Query, graphene.ObjectType):
        pass

    schema = graphene.Schema(query=Query)
    ```

2. 传递参数

    ```javascript
    type query {
        allInfo {
            id,
            nickname
        }
    }
    ```
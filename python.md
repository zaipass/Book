# Python & Graphql

1. 安装 graphene 包

    ```shell
    $ pip install graphene
    ```

2. 原生python([官方例子](https://graphql.cn/code/#python))

    ```python
    import graphene

    class Query(graphene.ObjectType):
        hello = graphene.String(name=graphene.String(default_value="World"))

        def resolve_hello(self, info, name):
            return 'Hello ' + name

    schema = graphene.Schema(query=Query)
    result = schema.execute('{ hello }')
    print(result.data['hello']) # "Hello World"
    ```
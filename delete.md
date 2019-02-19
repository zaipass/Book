### POST - delete 方法


1. 后端代码

    ```python
    # schema/delete.py
    class DeleteInfo(graphene.Mutation):
        class Arguments:
            pk = graphene.Int()

        ok = graphene.Boolean()

        def mutate(self, info, **kwargs):
            pk = kwargs.get('pk')

            info = Info.objects.get(id=pk)
            info.delete()
            return DeleteInfo(ok=True)
    
    class DMutation(object):
        delete_info = DeleteInfo.Field()
    ```

    ```python
    # 项目总的 schema 
    from app.schema.delete import DMutation

    class Mutations(DMutation, graphene.ObjectType):
        pass
    
    schema = graphene.Schema(mutation=Mutations)
    ```

2. 传递参数

    ```javascript
    mutation deleteInfoNick {
        deleteInfo (pk: 1) {
            ok
        }
    }
    ```
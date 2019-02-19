### POST - update 方法


1. 后端代码

    ```python
    class UpdateInfo(graphene.Mutation):
        class Arguments:
            nickname = graphene.String()
            pk = graphene.Int(required=True)
        
        info = graphene.Field(InfoType)
        ok = graphene.Boolean()

        def mutate(self, info, **kwargs):
            pk = kwargs.get('pk')
            info_obj = Info.objects.get(id=pk)
            if not info_obj:
                return UpdateInfo(ok=False)
            info_obj.__dict__.update(**kwargs)
            info_obj.save()
            ok = True
            return UpdateInfo(ok=ok, info=info_obj)

    class UpdateUser(graphene.Mutation):
        pass

    class UMutation(object):
        update_info = UpdateInfo.Field()

        update_user = UpdateUser.Field()
    ```

    ```python
    # 项目总 schema
    from app.schema.update import UMutation
    from app.schema.create import CMutation

    class Mutations(CMutation, UMutation, graphene.ObjectType):
        pass

    schema = graphene.Schema(mutation=Mutations)

    ```

2. 传递参数
    ```javascript
    mutation updateInfoNick {
        updateInfo (pk: 1, nickname: "passion") {
            info {
                id,
                nickname
            },
            ok
        }
    }
    ```
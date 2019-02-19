### POST - create 方法


1. 后端代码

    ```python 
    # schema/create.py
    class CreateInfo(graphene.Mutation):
        class Arguments:
            nickname = graphene.String(required=True)
            weight = graphene.Int()

        info = graphene.Field(InfoType) 
        ok = graphene.Boolean()

        def mutate(self, info, **kwargs):
            print(info.context.user, '==当前用户==')
            # kwargs 是传递参数中的变量
            user = info.context.user
            info = Info(**kwargs, owner=user)
            try:
                info.save()
                ok = True
            except Exception:
                ok = False
            return CreateInfo(ok=ok, info=info)

    class CMutation(object):
        create_info = CreateInfo.Field()
    ```

    ```python
    # 项目下的总 schema.py
    from app.schema.create import CMutation
    class Mutations(CMutation, graphene.ObjectType):
        pass

    schema = graphene.Schema(mutation=Mutations)
    ```

2. 传递参数

    ```javascript
    mutation createInfoNick {
        createInfo (nickname: "wo", weight: 56) {
            info {
                id,
                ...,
                weight,
                owner,
            },
            ok
        }
    }
    ```
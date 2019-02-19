# DRF & Graphene

1. 安装包

    ```shell
    # graphene_django
    $ pip install graphene-django>=2.0
    # rest_framework
    $ pip install djangorestframework
    # 使用的是 oauth2.0 进行身份权限验证
    $ pip install django-oauth-toolkit
    ```

2. settings 配置

    ```python
    INSTALLED_APPS = [
        ...
        'graphene_django',
        'rest_framework',
        'oauth2_provider',
        'app',
        ...
    ]

    GRAPHENE = {
        # schema所在的位置   '项目名称.schema文件.schema对象'
        'SCHEMA': 'qltest.schema.schema',  
        # 可以在此处添加中间件 (此处无用 - JWT)
        'MIDDLEWARE': [
            'graphql_jwt.middleware.JSONWebTokenMiddleware',
        ],
    }

    OAUTH2_PROVIDER = {
        'AUTHORIZATION_CODE_EXPIRE_SECONDS': 60 * 5,
        'ACCESS_TOKEN_EXPIRE_SECONDS': 60 * 60 * 12,
        # this is the list of available scopes
        'SCOPES': {
            'ql01': 'ql01 operation scope',
            'ql02': 'ql02 operation scope',
            'users': 'users operation scope'
        }
    }

    REST_FRAMEWORK = {
        'DEFAULT_AUTHENTICATION_CLASSES': (
            'oauth2_provider.contrib.rest_framework.OAuth2Authentication',
        ),
        'DEFAULT_PERMISSION_CLASSES': (
            'rest_framework.permissions.IsAuthenticated',
        ),
        # 'DEFAULT_FILTER_BACKENDS': ('django_filters.rest_framework.DjangoFilterBackend',),
        # 'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
        # 'PAGE_SIZE': 2
    }
    ```

3. 定义 models

    ```python
    from django.db import models

    class Info(models.Model):
        weight = models.DecimalField(max_digits=3, 
                                     decimal_places=1,
                                     verbose_name='体重',
                                     help_text='体重')
        nickname = models.CharField(max_length=20)  
        owner = models.ForeignKey('oauth2.user',
                                  related_name='info_user',
                                  on_delete=models.CASCADE,
                                  verbose_name='用户',
                                  help_text='用户')

        def __str__(self):
            return self.nickname 

        class Meta:
            verbose_name = '用户相关信息'
            verbose_name_plural = verbose_name
            ordering = ('-pk', )                                 
    ```

4. 创建 typemodels.py 

    ```python
    # important!
    from graphene_django.types import DjangoObjectType

    from .models import Info

    class InfoType(DjangoObjectType):
        class Meta:
            model = Info
    ```

5. 定义 app 视图 views

    ```python
    # 权限
    from graphene_django.views import GraphQLView 

    from rest_framework import viewsets, generics
    from rest_framework.permissions import IsAuthenticated, BasePermission
    from rest_framework.decorators import authentication_classes, permission_classes, api_view
    from rest_framework.settings import api_settings

    # 定义权限问题
    class OwnPermission(BasePermission):
        def has_object_permission(self, request, view, obj):
            print(obj.owner, request.user, '+++++++++ views.py')
            if obj.owner == request.user:
                return True
            else:
                return False

        def has_permission(self, request, view):
            print(request.user, '===>>> L')
            return True

    class TotalViews(GraphQLView):
        def parse_body(self, request):
            if type(request) is rest_framework.request.Request:
                return request.data
            return super().parse_body(request)

        # -----------
        # 重新定义 as_view() 类方法
        @classmethod
        def as_view(cls, *args, **kwargs):
            # 需要在 URL 中进行传递参数
            view = super(APGraphQLView, cls).as_view(*args, **kwargs)
            view = permission_classes((IsAuthenticated,))(view)
            view = authentication_classes(api_settings.DEFAULT_AUTHENTICATION_CLASSES)(view)
            view = api_view(['GET', 'POST'])(view)
            return view
        # ------------

    # 等同于重新定义 as_view() 类方法
    def drf_view(sc):
        # 传递参数
        view = TotalView.as_view(graphiql=True, schema=sm)
        view = permission_classes((OwnPermission, ))(view)
        # view = authentication_classes((TokenAuthentication,))(view)
        view = api_view(['GET', 'POST'])(view)
        return view
    ```

    - <strong>注意: </strong> 
    <span style="color: red">
    urls会发生不同: as_view的重写则在url中传递参数, drf_view在方法中定义参数
    </span>

6. app 下创建 schema 文件; 文件夹下创建 get.py/create.py/update.py ...

    ```python
    # 简单举例
    from .typemodels import InfoType
    from .models import Info

    class AppQuery(object):
        all_info = graphene.List(InfoType)

        own_info = graphene.Field(InfoType, pk=graphene.Int())

        def resolve_all_info(self, info, **kwargs):
            return Info.objects.all()

        def resolve_own_info(self, info, pk):
            return Info.objects.filter(id=pk)
    ```

7. 项目 settings 同级目录下创建总的 schema.py

    ```python
    from app.schema.get import AppQuery

    import graphene

    class Query(AppQuery, graphene.ObjectType):
        pass

    schema = graphene.Schema(query=Query)   # (mutation=Mutation)

    ```

8. 添加 urls.py

    ```python
    # 项目总路径
    urlpatterns = [
        ...
        path('graphql/', include('app.urls')),
    ]
    ```

    ```python
    # app/urls.py
    # 项目 schema 中导入 schema 对象
    from ql.schema import schema

    from .views import drf_view, TotalView

    urlpatterns = [
        # 无权限认证
        path('noa/', TotalView.as_view(graphiql=True)),
        # 权限认证
        path('yesa/', drf_view(schema)),
    ]
    ```
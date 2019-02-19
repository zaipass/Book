## 错误纠正

1. Django中继承'AbstractUser'类 <br/>
  报错: 'User' object has no attribute 'USERNAME_FIELD'
    ```python
    # 修改
    class User(AbstractUser):
        nickname = models.CharField(max_length=10)

        USERNAME_FIELD = 'nickname'

        def __str__(self):
            return self.nickname
    ```
2. 权限问题: (oauth2.0 和 models 的权限)
    - oauth2.0 中定义的权限
    ```python
    # settings文件中进行配置
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
    ```
    - models 中定义的权限
    ```python
    # models中定义的权限
    class Info(models.Model):
        vlog = models.CharField(max_length=200)

        class Meta:
            ...
            permissions = (
                ("vlog", "vlog_all_permissions"),
            )
    ```
3. 
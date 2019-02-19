## Graphql

- Facebook开源
- 只支持 GET/POST 请求方式
- 请求数据类型非json格式,一个 GraphQL 服务是通过定义类型和类型上的字段来创建的

    ```javascript
    type query {
        user {
            id, 
            nickname
        },
        ...
    }

    mutation opName {
        actionName {
            user {
                id,
                nickname
            },
            ok,
            ...
        }
    }
    ```
- 返回值是 json 格式的数据

    ```json
    {
        "data": {
            "user" : {
                "id" : 1,
                "nickname": "wo"
            }
        }
    }
    ```

- 根据传递的数据可以获取对应的响应数据, 减少API的多次请求
- 支持多种开发语言
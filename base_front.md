## Node.js - 前端服务

1. package.json

    ```json
    {
        "name": "graphql-test",
        "main": "app.js",
        "dependencies": {
            "express": "^4.16.4",
            "request": "",
            "graphql-request": ""
        }
    }
    ```

2. 安装

    ```shell
    $ npm install --save
    ```

3. 创建 app.js

    ```javascript
    // 导包
    const { GraphQLClient } = require('graphql-request');

    const client = new GraphQLClient('http://localhost:8000/graphql/ooo/', {
    headers: {
        // XCSRFToken: 'TjlCdlWHfqpS7aKsKZKJPKAlgodyC49v0BO3ptdV8J4GE4xNT80PXqV6XmquW9JW',
        authorization: 'Bearer 8WNxEdRUrfsJvPpm8jInLREzg8bd4C'
    }
    })

    const query = `query {
    ownUser (pk: 3) {
        username
    }
    }`

    const mutation = `mutation updateUser {
        updateUser(pk: 2, nickname: "max_11") {
            user {
                id,
                nickname
            }
        }
    }`

    const mutation_msg = `mutation createInfo {
    createInfo(weight: "23") {
        info {
            id, 
            nickname
        }
    }
    }
    `

    const update_msg = `mutation updateInfo{
    updateInfo(pk: 1, weight: "111") {
        msg {
            id
        },
        ok
    }
    }
    `

    const variables = ''

    client.request(update_info).then(data => console.log(data))
    ```
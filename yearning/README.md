# Yearning

[github](https://github.com/cookieY/Yearning)
[Yearning 使用文档](http://next.yearning.io)

## Q&A

1. 默认账号密码
   默认账号：admin，默认密码：Yearning_admin
2. docker-compose
    
    注意更换SECRET_KEY, 16 个字符串
   
    初始化: `command: /bin/bash -c "./Yearning install"`

    升级使用: `command: /bin/bash -c "./Yearning migrate"`

    重置admin密码: `command: /bin/bash -c "./Yearning reset_super"`

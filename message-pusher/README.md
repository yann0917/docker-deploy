# message-pusher

[github](https://github.com/songquanpeng/message-pusher)
[message-pusher 使用文档](https://alist.nn.ci/zh/)

## Q&A

* 系统启动后，使用 `root` 用户登录系统并做进一步的配置，默认密码为 `123456`

## 环境变量

* `REDIS_CONN_STRING`：设置之后将使用 Redis 作为请求频率限制的存储，而非使用内存存储。
例子：REDIS_CONN_STRING=redis://default:redispw@localhost:49153

* `SESSION_SECRET`：设置之后将使用固定的会话密钥，这样系统重新启动后已登录用户的 cookie 将依旧有效。
例子：SESSION_SECRET=random_string

* `SQL_DSN`：设置之后将使用指定数据库而非 SQLite。
例子：SQL_DSN=root:123456@tcp(localhost:3306)/message-pusher

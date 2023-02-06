# AList

[github](https://github.com/alist-org/alist)
[AList 使用文档](https://alist.nn.ci/zh/)

## Q&A

1. 忘记账号密码怎么办？

    进入 docker ，执行 `./alist admin` 即可查看初始账号密码

2. Docker 安装如何更新
    
    docker ps -a #查看容器(找Alist容器的ID)

    docker stop ID #停止Alist运行,不然无法删除

    docker rm ID #删除Alist容器(数据还在只要你不手动删除)

    docker pull xhofe/alist:latest

    重新执行安装步骤即可
# 使用说明

> [原docker 说明地址](https://github.com/TechXueXi/TechXueXi/blob/dev/DOCKER.md)

## 配置信息说明

* amd64（一般64位电脑，服务器）
    `docker pull techxuexi/techxuexi-amd64:{tag}`
* arm64（树莓派等）
  `docker pull techxuexi/techxuexi-arm64v8:{tag}`

对于 Server 酱和 pluspush，只需要填写 token，而钉钉，TG 等机器人需要填写 token 和 secret

* AccessToken 。bot 发送指令的 token 具体方法参见下方 bot 指南

* Secret 钉钉推送时为钉钉 Secret，Telegram bot 则为管理员数字 Id

* Nohead 无窗口模式 默认值True，Docker 不要修改此参数
* Pushmode 推送方式

```txt
0 不开启
1 钉钉
2 微信（并未实现， https://github.com/orgs/TechXueXi/projects/2#card-66789077 ）
3 Server 酱
4 pluspush
5 Telegram Bot （推荐，支持指令交互，随时可以开始学习，需要翻墙）
```

推送消息是为了把登录链接发送给你，现在请每天点击链接登录。

---

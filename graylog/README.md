# graylog

> [graylog installation docker](https://docs.graylog.org/en/4.0/pages/installation/docker.html)

## 目录结构

```bash
.
├── README.md
├── config
│   ├── graylog.conf
│   ├── log4j2.xml
│   └── node-id
├── data
├── docker-compose.yml
└── plugin
    └── graylog-integrations-plugins-4.0.3
        └── plugin
            └── graylog-plugin-integrations-4.0.3.jar
```

* `config` 目录存放自定义配置文件
* `plugin` 目录存放插件文件

## 自定义配置文件

```bash
mkdir config
cd config
wget https://raw.githubusercontent.com/Graylog2/graylog-docker/4.0/config/graylog.conf
wget https://raw.githubusercontent.com/Graylog2/graylog-docker/4.0/config/log4j2.xml
```

## 插件安装

```bash
mkdir plugin
cd plugin
wget https://downloads.graylog.org/releases/graylog-integrations/graylog-integrations-plugins-4.0.3.tgz
tar -xvzf graylog-integrations-plugins-4.0.3.tgz graylog-integrations-plugins-4.0.3/plugin/graylog-plugin-integrations-4.0.3.jar
```

---

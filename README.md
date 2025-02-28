# 跨平台 BOT 插件兼容框架·开发中

[![License](https://img.shields.io/github/license/richardchien/nonebot.svg)](LICENSE)&nbsp;![Python Version](https://img.shields.io/badge/python-3.7+-blue.svg)

本分支为实验性特性，使用HJSON作为配置文件，同时支持Docker部署

## 简介

拥有可全平台兼容的 BOT 兼容层，可以快速接入多个不同平台或相同平台 bot。(开发中)

BOT 兼容层：多接口、多接口权限模块、多接口消息流-主动发送、多接口插件兼容层-消息获取及响应

已实现内置插件功能：机器翻译、多功能转推、烤推、RSS 更新订阅等

主要依赖于模块[tweepy](https://github.com/tweepy/tweepy)进行推特操作

## 使用文档

[文档](https://bothbot-documentation.readthedocs.io/)

## 部署文档

### 项目使用及配置

本分支使用支持Docker部署，可自行根据Dockerfile构建，也可以使用[Docker Hub](https://hub.docker.com/r/lonelyion/tweet-to-bot)上已经构建好的镜像

##### 配置项目

首先服务器需安装好Docker环境以及docker-compose

接着新建一个文件夹用于存放配置文件

```shell
mkdir tweetToBot
mkdir tweetToBot/conf
cd tweetToBot
touch docker-compose.yml
wget -O conf/config.hjson https://raw.githubusercontent.com/chenxuan353/tweetToBot/v3/conf/config_example.hjson
```

打开项目根目录下的`docker-compose.yml`文件，写入以下内容：

```yml
version: "3.9"
services:
  tweet_to_bot:
    container_name: bot_1
    hostname: bot_1
    volumes:
      - ./conf:/app/conf
    image: lonelyion/tweet-to-bot:latest
    ports:
      - 80:80       # 烤推图片
      - 8091:8091   # NoneBot
    environment:
      - LANG=zh_CN.UTF-8
      - TZ=Asia/Shanghai
```

接下来打开`conf/config.hjson`，然后按照文件里的说明进行配置，记得将各功能按需求开启或关闭

##### 启动项目

```shell
docker-compose up -d
```

//TODO: 多docker实例配置的最佳实践

### bot 连接到后端

#### Tencent QQ

部署到 QQ 需要使用支持 CQHTTP 协议的 BOT 端

原 CoolQBot 的通信依赖于 CQHTTP，由于平台问题已迁移至[go-cqhttp](https://github.com/Mrs4s/go-cqhttp)

特别鸣谢[richardchien](https://github.com/richardchien)对 nonebot 封装项目的贡献。

CQHTTP 协议支持的端：[go-cqhttp](https://github.com/yyuueexxiinngg/cqhttp-mirai)、[mirai](https://github.com/yyuueexxiinngg/cqhttp-mirai)

连接配置：

在**ws_reverse_servers(反向 ws 配置)**的配置项内**输入服务器的 IP 与端口号**即可连上此后端

go-cqhttp的示例：

```json
    "ws_reverse_servers": [
        {
            "enabled": true,
            "reverse_url": "ws://127.0.0.1:8091/ws/",
            "reverse_api_url": "ws://127.0.0.1:8091/ws/api/",
            "reverse_event_url": "ws://127.0.0.1:8091/ws/event/",
            "reverse_reconnect_interval": 3000
        }
    ],
```
# 本项目参考https://github.com/zhayujie/chatgpt-on-wechat，基于这个做了些更付合自己需求的修改，我主要是在自己的服务器上进行docker部署，主要修改了docker-compose.yml文件

## Docker部署

> 使用docker部署无需下载源码和安装依赖，只需要获取 docker-compose.yml 配置文件并启动容器即可。

> 前提是需要安装好 `docker` 及 `docker-compose`，安装成功的表现是执行 `docker -v` 和 `docker-compose version` (或 docker compose version) 可以查看到版本号，可前往 [docker官网](https://docs.docker.com/engine/install/) 进行下载。

### (1) 下载 docker-compose.yml 文件

```bash
wget https://open-1317903499.cos.ap-guangzhou.myqcloud.com/docker-compose.yml
```

下载完成后打开 `docker-compose.yml` 修改所需配置，如 `OPEN_AI_API_KEY` 和 `GROUP_NAME_WHITE_LIST` 等,默认是一个微信机器人。
#### 如果需要启动多个机器人就写多个容器配置即可，这里是例子：
```bash
version: '2.0'
services:
  qwen-on-wechat:
    image: zhayujie/chatgpt-on-wechat
    container_name: qwen-on-wechat
    security_opt:
      - seccomp:unconfined
    environment:
      OPEN_AI_API_KEY: ''
      MODEL: 'qwen'
      PROXY: ''
      SINGLE_CHAT_PREFIX: '[""]'
      SINGLE_CHAT_REPLY_PREFIX: ""
      GROUP_CHAT_PREFIX: '["@bot"]'
      GROUP_NAME_WHITE_LIST: '["分身大集合"]'
      IMAGE_CREATE_PREFIX: '["画", "看", "找"]'
      CONVERSATION_MAX_TOKENS: 1000
      SPEECH_RECOGNITION: 'False'
      CHARACTER_DESC: '你是通义千问, 一个由阿里训练的大型语言模型, 你旨在回答并解决人们的任何问题，并且可以使用多种语言与人交流。'
      EXPIRES_IN_SECONDS: 3600
      USE_GLOBAL_PLUGIN_CONFIG: 'True'
      QWEN_ACCESS_KEY_ID: "your ak id"
      QWEN_ACCESS_KEY_SECRET: "your aks"
      QWEN_AGENT_KEY: "your agent key"
      QWEN_APP_ID: "your app id"
      QWEN_NODE_ID: ""
  linkai-on-wechat:
    image: zhayujie/chatgpt-on-wechat
    container_name: linkai-on-wechat
    security_opt:
      - seccomp:unconfined
    environment:
      OPEN_AI_API_KEY: ''
      MODEL: ''
      PROXY: ''
      SINGLE_CHAT_PREFIX: '[""]'
      SINGLE_CHAT_REPLY_PREFIX: ""
      GROUP_CHAT_PREFIX: '["@bot"]'
      GROUP_NAME_WHITE_LIST: '["分身大集合"]'
      IMAGE_CREATE_PREFIX: '["画", "看", "找"]'
      CONVERSATION_MAX_TOKENS: 1000
      SPEECH_RECOGNITION: 'False'
      CHARACTER_DESC: '你是linkai, 一个由linkAI训练的大型语言模型, 你旨在回答并解决人们的任何问题，并且可以使用多种语言与人交流。'
      EXPIRES_IN_SECONDS: 3600
      USE_GLOBAL_PLUGIN_CONFIG: 'True'
      USE_LINKAI: 'True'
      LINKAI_API_KEY: 'your api key'
      LINKAI_APP_CODE: 'your app code'
```
上面这段docker-compose.yml 里定义了两个service，既两个微信机器人：qwen-on-wechat和linkai-on-wechat，具体参数内容，参考[config.py](https://github.com/amyflash/chatgpt-on-wechat/blob/master/config.py)
### (2) 启动容器

在 `docker-compose.yml` 所在目录下执行以下命令启动容器：

```bash
sudo docker compose up -d
```
运行 `sudo docker ps` 能查看到 NAMES 为 chatgpt-on-wechat 的容器即表示运行成功。

注意：

 - 如果 `docker-compose` 是 1.X 版本 则需要执行 `sudo  docker-compose up -d` 来启动容器
 - 该命令会自动去 [docker hub](https://hub.docker.com/r/zhayujie/chatgpt-on-wechat) 拉取 latest 版本的镜像，latest 镜像会在每次项目 release 新的版本时生成

最后运行以下命令可查看容器运行日志，扫描日志中的二维码即可完成登录：

```bash
sudo docker logs -f chatgpt-on-wechat
```
### (3) 关闭容器，如果需要关闭容器，执行这个命令：

```bash
sudo docker compose down
```
这样对应的微信机器人也停止收发消息了，避免token浪费


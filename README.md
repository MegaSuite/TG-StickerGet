# StickerDownloader
<p>
   <a href="https://github.com/MegaSuite/TG-StickerGet/blob/main/LICENSE">
      <img alt="GitHub license" src="https://img.shields.io/github/license/megasuite/TG-StickerGet">
   </a>
   <a href="https://github.com/MegaSuite/TG-StickerGet/commits/main/">
      <img alt="GitHub last commit" src="https://img.shields.io/github/last-commit/megasuite/TG-StickerGet">
   </a>
</p>

> 一个可以帮你下载表情包的telegram机器人

本项目基于[rroy233/StickerDownloader](https://github.com/rroy233/StickerDownloader)进行适于docker运行的改进。

## 功能

* 发送表情、表情链接给bot，bot为您转换为便于保存的gif文件.
* 支持将Telegram官方出品的表情(tgs)格式转换为gif.
* 转发gif图给bot，bot会以文件形式发送回给你以便保存.
* 下载单个表情.
* 下载整个表情包.

![cover](docs/demo.gif)

## 使用方法

### 编写docker-compose.yaml

```bash
mkdir stickerget
cd stickerget
vim docker-compose.yaml
```

内容如下：


```yaml
services:
  stickerget:
    restart: always
    container_name: stickerget
    image: megasuite/tg_sticker_get:0.2
    volumes:
      - ./config/config.yaml:/app/config.yaml
    depends_on:
      - redis

  redis:
  	restart: always
    image: redis:alpine
    container_name: redis
```

### 创建Telegram Bot

获得`bot_token`,然后设置命令列表

```
help - 帮助
getlimit - 获取当日使用限额
admin - 查看管理员指令
```

### 编写config.yaml

```bash
mkdir config
vim config/config.yaml
```

内容如下：

```yaml
general:
  bot_token: "nnnnnnn:xxxxxxxxxxxxxxxxxxxx" # 从BotFather获得
  language: "zh-hans" # 默认语言(对应/languages文件夹中的文件名)
  worker_num: 2 # 消息处理的线程数
  download_worker_num: 3 # 下载、文件转码工作线程数
  admin_uid: 66666666 # 管理员telegram ID，从@getUserID_Robot获取
  user_daily_limit: 100 # 每日使用次数限制
  process_wait_queue_max_size: 50 # 等待队列最大长度
  process_timeout: 60 # 处理超时时间(s)
  support_tgs_file: true # 是否开启tgs表情支持
  max_amount_per_req: 100 # 下载整套表情包时允许的最大数量

cache:
  enabled: false # 是否启用文件缓存(需要使用Redis)
  storage_dir: "./storage/cache" # 文件缓存存放位置
  max_disk_usage: 1024 # 最大磁盘占用(MB)
  cache_expire: 86400 # 文件缓存有效期(s)
  cache_clean_interval: 1800 # 过期文件检查周期(s)

logger:
  report: false # 是否启用远程日志上报(需要自行设计接收端，参考https://github.com/rroy233/logger)
  report_url: "" # 远程日志上报url(POST)
  report_query_key: "" # 远程日志上报query参数

redis:
  server: "redis" # redis服务器地址，使用docker-compose部署，使用容器名称内部通信
  port: "6379" # redis服务器端口
  tls: false # redis是否启用tls
  password: "" # redis密码
  db: 0 # redis数据库编号
```

### 运行

```
docker compose up -d
```

### 目录结构

```yaml
stickerget
	-- docker-compose.yaml
	-- config
		-- config.yaml
```



### LICENSE

`GPL-3.0 license`

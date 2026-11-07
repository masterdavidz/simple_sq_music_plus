# sqmusic 部署文件（群晖 DSM 7.2 / Container Manager）

一份可直接使用的 `docker-compose.yml`，包含：

| 服务 | 说明 |
|---|---|
| `mysql` | MySQL 5.7，命名卷持久化，健康检查 |
| `sqmusic_main` | 后端，自建镜像（官方 v3.1.34 + `DOWNLOAD_COVER`/`DOWNLOAD_LYRIC` 开关） |
| `sqmusic_web` | 前端 Nginx，宿主机 `8096` → 容器 `80` |
| `kugoumusicapi` | 酷狗 API（可选，用于酷狗下载源） |

## 快速部署

```bash
mkdir -p /volume1/docker/sqmusic && cd /volume1/docker/sqmusic
curl -fsSL https://raw.githubusercontent.com/masterdavidz/simple_sq_music_plus/deploy/deploy/docker-compose.yml -o docker-compose.yml
# 按需修改音乐目录（默认 /volume1/music）和端口（默认 8096）
docker compose up -d
```

浏览器打开 `http://群晖IP:8096`，默认账号 `admin` / 密码 `admin`。

## 部署前必须确认的三件事

1. **音乐目录**：`/volume1/music` 换成你自己的共享文件夹（绝对路径）。
2. **端口**：`8096` 若被 Jellyfin 占用，改成别的。
3. **宿主机环境**（本机踩过的坑，详见主仓 README）：
   - 开机任务执行 `sysctl -w net.bridge.bridge-nf-call-iptables=0`，否则容器之间会超时；
   - 防火墙放行 `172.30.99.0/24`，否则容器出不了网、搜不到歌。

## 封面/歌词开关

自建镜像支持两个环境变量（默认 `true`，保持官方行为）：

```yaml
      DOWNLOAD_COVER: "false"   # 不下载专辑图/歌手图
      DOWNLOAD_LYRIC: "false"   # 不生成 .lrc、不写内嵌歌词
```

改完 `docker compose up -d` 即可，无需重建镜像。

## 镜像地址

```
crpi-x0rt30yit4wrxixn.cn-beijing.personal.cr.aliyuncs.com/sq_music/simple_sq_music_plus:music-only
```

源码 fork：https://github.com/masterdavidz/simple_sq_music_plus （`SearchHanderAbstract.java` 中实现了两个开关）

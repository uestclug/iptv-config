# IPTV

## Inroduce

这是对[电子科技大学IPTV](https://iptv.uestc.edu.cn)的介绍，以及展示如何快速搭建一个下游iptv服务器。

## Requirements

- 一台有着较为充足带宽和内存的服务器
- 安装有 nginx 和 wget 的操作系统。作为参考，电子科技大学 IPTV 使用了 debian 10 及系统内置的 nginx。
``` sudo apt install nginx wget```

## Nginx Configuration

IPTV 的取源部分非常简单，只需要在 nginx 配置文件中反代+缓存上游的文件，并加上ip白名单等需要的功能。

1. 复制 frontend/iptv.conf 至服务器的 /etc/nginx/conf.d/ 文件夹下，任意命名，只需要以 .conf 结尾
2. 修改 iptv.conf 文件的三处 [ip] 为你所使用的上游服务器的ip/域名
3. 将 iptv.conf 中所有出现的 iptv.uestc.edu.cn 替换为你所使用的服务器域名
4. 将 iptv.conf 的 ssl_certificate 相关字段配置为你所使用的证书。如果不启用 https 就将第一个花括号中的 443 端口替换为 80，并删除第二个 server {} 全部内容
5. 适当修改 /etc/nginx/nginx.conf 配置。作为参照，电子科技大学IPTV 将 worker_connections 修改为了 20480。
6. 添加ip白名单。将 frontend/ip_whitelist.txt 放置在 /etc/nginx/ip_whitelist.txt 并适当修改。

## Frontend Configuration

我们选用了[清华大学的前端模板](https://github.com/tvly/tvly-web)

我们的参照配置文件位于 frontend/config.json5 ,阅读并将相关配置项替换为你的服务器域名。

## Addon Configuration

该前端需要的部分信息文件上游暂时没有直接提供，我们选择了定时从清华 IPTV 获取。/var/www/iptv/ 为前端文件所在的文件夹

- json文件格式的节目单。(我们在清华基础上删除了部分缺失的内容)
```wget https://iptv.uestc.edu.cn/channels.json -O /var/www/iptv/channels.json```

- 节目列表信息。运行命令```crontab -e```并添加以下内容
```* */4 * * * wget https://iptv.tsinghua.edu.cn/epg/todayepg.json -O /var/www/iptv/dist/epg.json```


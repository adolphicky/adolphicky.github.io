---
layout: post
title: "Nginx配置文件简介"
date: 2018-01-15
description: "nginx配置文件，nginx.conf"
tag: middleware
---

### Nginx的全局配置
下面这段内容是对Nginx的全局属性配置，代码如下：
```
user nobody nobody;
worker_processes 4;
error_log logs/error.log notice;
pid logs/nginx.pid;
worker_rlimit_nofile 65535;
events{
  use epoll;
  worker_connections 65536;
}
```
上面这段代码中每个配置选项的含义解释如下：
- `user`是个主模块指令，指定Nginx worker进程运行用户级用户组，默认由nobody账号运行。
- `worker_processes`是个主模块指令，指定了Nginx要开启的进程数。每个Nginx进程平均耗费10MB~12MB内存。根据经验，一般指定一个进程足够了，如果是多核CPU，建议指定和CPU的数量一样多的进程数即可。
- `error_log`是个主模块指令，用来定义全局错误日志文件。日志输出级别有debug、info、notice、warn、error、crit可供选择，其中，debug输出日志最为详细，而crit输出日志最少。
- `pid`是个主模块指令，用来指定进程id的存储文件位置。
- `worker_rlimit_nofile`用于绑定worker进程和CPU，linux内核2.4以上可用（目前基本为2.6以上）
- `events`指令用来设定Nginx的工作模式及连接数上限。
- `use`是个事件模块指令，用来指定Nginx的工作模式。Nginx支持的工作模式有select、poll、kqueue、epoll、rtsig和/dev/poll。其中select和poll都是标准的工作模式，kqueue和epoll是高效的工作模式，不同的是epoll用在Linux平台上，而kqueue用在BSD系统中。`对于Linux系统，epoll工作模式是首选`。
- `worker_connections`也是个事件模块指令，用于定义Nginx每个进程的最大连接数，默认是1024.最大客户端连接数由`worker_processes`和`worker_connections`决定，即`max_cilent=worker_processes*worker_connections`,在作为反向代理时变为：`max_cilents=worker_processes*worker_connections/4`。
进程的最大连接数受Linux系统进程的最大打开文件数限制，在执行操作系统命令`ulimit -n 65536`后worker_connections的设置才能生效。

### HTTP服务器配置
下面这段内容是Nginx对HTTP服务器相关属性的配置，代码如下：
http{
  include conf/mime.types;
  default_type  application/octet-stream;
  log_format  max '$remote_addr - $remote_user [$time_local]'
    '"$request" $status $bytes_sent'
    '"$http_referer" "$http_user_agent"'
    '"$gzip_ratio"';
    log_format download '$remote_addr - $remote_user [$time_local]'
    '"$request" $status $bytes_sent'
    '"$http_referer" "$http_user_agent"'
    '"$http_range" "$sent_http_content_range"';
  client_max_body_size 20m;
  client_header_buffer_size 32k;
  senfile on;
  tcp_nopush on;
  tcp_nodelay on;
  keepalive_timeout 60;
  client_header_timeout 10;
  client_body_timeout 10;
  send_timeout 10;
}
下面详细介绍这段代码中每个配置选项的含义。
- `include`是个主模块指令，实现对配置文件所包含的文件的设定，可以减少主配置文件

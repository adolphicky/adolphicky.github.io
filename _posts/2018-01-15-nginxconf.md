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
- `worker_cpu_affinity`是个主模块指令，指定了Nginx要开启哪些CPU内核，采用二进制表示对应的CPU内核。
- `error_log`是个主模块指令，用来定义全局错误日志文件。日志输出级别有debug、info、notice、warn、error、crit可供选择，其中，debug输出日志最为详细，而crit输出日志最少。
- `pid`是个主模块指令，用来指定进程id的存储文件位置。
- `worker_rlimit_nofile`用于绑定worker进程和CPU，linux内核2.4以上可用（目前基本为2.6以上）
- `events`指令用来设定Nginx的工作模式及连接数上限。
- `use`是个事件模块指令，用来指定Nginx的工作模式。Nginx支持的工作模式有select、poll、kqueue、epoll、rtsig和/dev/poll。其中select和poll都是标准的工作模式，kqueue和epoll是高效的工作模式，不同的是epoll用在Linux平台上，而kqueue用在BSD系统中。`对于Linux系统，epoll工作模式是首选`。
- `worker_connections`也是个事件模块指令，用于定义Nginx每个进程的最大连接数，默认是1024.最大客户端连接数由`worker_processes`和`worker_connections`决定，即`max_cilent=worker_processes*worker_connections`,在作为反向代理时变为：`max_cilents=worker_processes*worker_connections/4`。
进程的最大连接数受Linux系统进程的最大打开文件数限制，在执行操作系统命令`ulimit -n 65536`后worker_connections的设置才能生效。

### HTTP服务器配置
下面这段内容是Nginx对HTTP服务器相关属性的配置，代码如下：
```
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
```
下面详细介绍这段代码中每个配置选项的含义。
- `include`是个主模块指令，实现对配置文件所包含的文件的设定，可以减少主配置文件的复杂度。类似于apache中的include方法。
- `default_type`属于HTTP核心模块指令，这里设定默认类型为二进制流，也就是当文件类型未定义时使用这种方式，例如在没有配置PHP环境时，Nginx是不予解析的，此时，用浏览器访问PHP文件就会出现下载窗口。
- `log_format`是Nginx的HttpLog模块指令，用于指定Nginx日志的输出格式。main为此日志输出格式的名称，可以在下面的access_log指令引用。
- `client_max_body_size`用来设置允许客户端请求的最大的单文件字节数。
- `client_header_buffer_size`用来制定来自客户端请求头的headerbuff大小。对于大多数请求，1KB的缓冲区大小已经足够,如果自定义了消息头或有更大的cookie，可以增加缓冲区大小。这里设置为32KB。
- `large_client_header_buffers`用来制定客户端请求中较大的消息头的缓存最大数量和大小，“4”为个数，“128K”为大小，最大缓存为4个128KB。
- `sendfile`参数用于开启高效文件传输模式。将tcp_nopush和tcp_nodely两个指令设置为on，用于防止网络阻塞。
- `keepalive_timeout`用于设置客户端连接保持活动的超时时间。在超过这个时间之后，服务器会关闭该连接。
- `client_header_timeout`用于设置客户端请求头读取超时时间。如果超过这个时间，客户端还没有发送任何数据，Nginx将返回“Request time out（408）”错误。
- `client_body_timeout`用于设置客户端请求主体读取超时时间，默认值为60.如果超过这个时间，客户端还没有发送任何数据，Nginx将返回“Request time out（408）”错误。
- `send_timeout`用于指定响应客户端的超时时间。这个超时仅限于两个链接活动之间的时间，如果超过这个时间，客户端没有任何活动，Nginx将会关闭链接。

### HttpGzip模块配置
下面是HttpGzip模块在Nginx配置中的相关属性设置：
```
gzip on;
gzip_min_length 1k;
gzip_buffers 4 16k;
gzip_http_version 1.1;
gzip_comp_level 2;
gzip_types text/plain application/x-javascript text/css application/xml;
gzip_vary on;
```

- `gzip`用于设置开启或者关闭gzip模块，“gzip on”表示开启gzip压缩，实时压缩输出数据流。
- `gzip_min_length`用于设置允许压缩的页面最小字节数，页面字节数从header头的Content-Length中获取。默认值是0，不管页面多大都进行压缩。建议设置成大于1K的字节数，小于1K可能会越压越大。
- `gzip_buffers`表示申请4个单位为16K的内存作为压缩结果流缓存，默认值是申请与原始数据大小相同的内存空间来存储gzip压缩结果。
- `gzip_http_version`用于设置识别HTTP协议版本，默认是1.1，目前大部分浏览器已经支持gzip解压，使用默认即可。
- `gzip_comp_level`用来指定gzip压缩比，1压缩比最小，处理速度最快；9压缩比最大，传输速度快，但处理最慢，也比较消耗CPU资源。
- `gzip_types`用来指定压缩的类型，无论是否指定，“text/html”类型总是会被压缩的。
- `gzip_vary`选项可以让前段的缓存服务器缓存经过gzip压缩的页面，例如，用squid缓存经过Nginx压缩的数据。

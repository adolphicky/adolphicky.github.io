---
layout: post
title: "Nginx应用简介"
date: 2018-01-15
description: "nginx,简介"
tag: middleware
---

## nginx的优势

### nginx与apache的异同
  nginx 和 apache一样，都是http服务器软件，在功能实现上都是采用模块化结构设计，都支持通用的语言接口，如php、perl、python等，同时还支持正向和反向代理、虚拟主机、URL重写、压缩传输、ssl加密传输等。它们之间最大的差别是apache的处理速度很慢，且占用很多内存资源，而nginx却恰恰相反：在功能实现上，apache的所有模块都支持动、静态编译，而nginx模块都是静态编译的，同时，apache对fcgi的支持不好，而nginx对fcgi的支持非常好；在处理方式上，nginx支持epoll，而apache不支持；在空间使用上，nginx轻量而apache可以用庞然大物来形容。

### nginx的优势体现
- 作为web服务器，nginx处理静态文件、索引文件，`自动索引的效率非常高`。
- 作为代理服务器，nginx可以实现`无缓存的反响代理加速`，提高网站运行速度。
- 作为负载均衡服务器，nginx既可以在内部直接支持rails和php，也可以支持http代理服务器对外进行服务，同时还支持加单的容错和利用算法进行负载均衡。
- 在性能方面，nginx是专门为性能优化而开发的，在实现上非常注重效率。它采用内核poll模型，可以支持更多的并发连接，`最大可以支持对50000个并发连接数的响应`，而且只占用很低的内存资源。
- 在稳定性方面，nginx采取了分阶段资源分配技术，使得cpu与内存的占用率非常低。nginx官方表示，nginx保持10000个没有活动的连接，而这些连接只占用2.5MB内存，因此，类似DOS这样的攻击对nginx来说基本上是没有任何作用的。
- 在高可用性方面，nginx支持热部署，启动速度非常迅速，因此可以在不间断服务的情况下，对软件版本或者配置进行升级，即使运行数月也无需重新启动，几乎可以做到7x24小时不间断地运行。

### nginx的模块与工作原理
  nginx由内核和模块组成，其中，内核的设计非常微小和简洁，完成的工作也非常简单，仅仅通过查找配置文件将客户端请求映射到一个location block（location 是nginx配置中的一个指令，用于url匹配），而在这个location中所配置的每个指令将会启动不同的模块去完成相应的工作。

  nginx的模块从结构上分为核心模块、基础模块和第三方模块，http模块、EVENT模块和MAIl模块等属于核心模块，HTTP Access模块、HTTP FastCGI模块、HTTP Proxy模块和HTTP Rewrite模块属于基本模块，而HTTP Upsteam Request Hash模块、Notice 模块和HTTP Access Key模块属于第三方模块，用户根据自己的需要开发的魔窟都属于第三方魔窟。正是有了这么多魔窟的支撑，nginx的功能才会如此的强大。

  nginx的魔窟从功能上分为如下三类。
  - Handlers（处理器魔窟）。此类模块直接处理请求，并进行输出内容和修改headers信息等操作。Handlers处理器模块一般只能有一个。
  - Filters（过滤器模块）。此类模块主要对其他处理器模块输出的内容进行修改操作，最后由nginx输出。
  - Proxies（代理类模块）。此类模块是nginx的HTTP Upstream之类的模块，这些模块主要与后端一些服务比如FastCGI等进行交互，实现服务代理和负载均衡等功能。
  ![](/images/posts/middleware/nginx/Nginxmod.png)

  在工作方式上，Nginx分为单工作进程和多工作进程两种模式。在单工作进程模式下，除主进程外，还有一个工作进程，工作进程是单线程的；在多工作进程模式下，每个工作进程包含多个线程。Nginx默认为单工作进程模式。

  Nginx的模块直接被编译进Nginx，因此属于静态编译方式。启动Nginx后，Nginx的模块被自动加载，不像apache，首先将模块编译为一个so文件，然后在配置文件中指定是否加载。在解析配置文件时，Nginx的每个模块都有可能去处理某个请求，但是同一个处理请求只能由一个模块来完成。

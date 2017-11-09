## xdebug 远程 php 调试

Xdebug 使用 C/S (客户端/服务器端) 模式来进行远程 php 调试。客户端是 PHP 运行环境启用的 Xdebug 模块，服务端是 IDE （比如 phpstorm）开启的 xdebug 服务。

## 1
* * *
## 试用

1. 开启 Xdebug 服务端：点击 phpstorm 菜单 Run -> Start Listening for PHP Debug Connections；
2. 设置服务端地址：打开文件 etc/nginx.conf, 将 X-Forwarded-For 后面的 IP 修改为 phpstrom 所在机器的 IP；
3. 开启 Xdebug 客户端：docker-compose up；
4. 在 phpstrom 里打开文件 src/index.php，加断点；
5. 访问 [http://127.0.0.1:5678/?XDEBUG_SESSION_START](http://127.0.0.1:5678/?XDEBUG_SESSION_START)

## 2
* * *
## 项目说明

### 2.1
* * *
### hg2c/php:5-dev

这个镜像在官方镜像 php:5-apache 的基础上，安装了 composer, phpunit, xdebug, mysql 等常用组件。

Dockerfile: [https://github.com/hg2c/php/blob/5-dev/Dockerfile](https://github.com/hg2c/php/blob/5-dev/Dockerfile)

### 2.2
* * *
### etc/xdebug.ini

这个文件会被加载到 php 容器里：/usr/local/etc/php/conf.d/xdebug.ini

### 2.3
* * *
### etc/nginx.conf

xdebug.ini 里启用了 xdebug.remote_connect_back, 就会使用 http header 里的 X-Forwarded-For 作为 Xdebug 服务端地址。所以需要在 nginx 里正确设置（开发机的 IP）。

也可以选择在 xdebug.ini 里指定服务端地址，例如 xdebug.remote_host = my.dev.host

## 3
* * *
## FAQ

1. 如果要在公网上测试，而公网服务器连接不上内网的开发机导致 Xdebug 失败的话，配置内网穿透（如 ssh 反向隧道）即可。可进入 php 容器，查看 /tmp/xdebug.log 解决 xdebug 连接问题；
2. docker-compose.yml 里的 nginx 非必选项，是为了更真实的模拟生产环境。

## 4
* * *
## 参考链接

1. [https://xdebug.org/docs/all_settings](https://xdebug.org/docs/all_settings)

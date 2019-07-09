#### [frp地址](https://github.com/fatedier/frp)

### frp的作用

* 利用处于内网或防火墙后的机器，对外网环境提供 http 或 https 服务。
* 对于 http, https 服务支持基于域名的虚拟主机，支持自定义域名绑定，使多个域名可以共用一个80端口。
* 利用处于内网或防火墙后的机器，对外网环境提供 tcp 和 udp 服务，例如在家里通过 ssh 访问处于公司内网环境内的主机。

### 下载软件
根据对应的操作系统及架构，从 [Release](https://github.com/fatedier/frp/releases)页面下载最新版本的程序。 将 frps 及 frps.ini 放到具有公网 IP 的机器上,将 frpc 及 frpc.ini 放到处于内网环境的机器上。   
### 通过 ssh 访问公司内网机器
1.修改 frps.ini 文件，这里使用了最简化的配置：
```
# frps.ini
[common]
bind_port = 7000 
```
2.启动 frps：
```
./frps -c ./frps.ini 
```
或者
```
./frps -c ./frps.ini & 后台启动,也可加上nohup
```
3.修改 frpc.ini 文件，假设 frps 所在服务器的公网 IP 为 x.x.x.x；
```
# frpc.ini
[common]
server_addr = x.x.x.x
server_port = 7000

[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 6000
```
4.启动frpc：
```
./frpc -c ./frpc.ini
```
或者
```
./frpc -c ./frpc.ini &  后台启动,也可加上nohup
```
5.通过 ssh 访问内网机器，也可通过ssh工具访问，假设用户名为 test：
```
ssh -oPort=6000 test@x.x.x.x
```

### 通过自定义域名访问部署于内网的 web 服务
有时想要让其他人通过域名访问或者测试我们在本地搭建的 web 服务，但是由于本地机器没有公网 IP，无法将域名解析到本地的机器，通过 frp 就可以实现这一功能，以下示例为 http 服务，https 服务配置方法相同， vhost_http_port 替换为 vhost_https_port， type 设置为 https 即可。

1.修改 frps.ini 文件，设置 http 访问端口为 8080：
```
# frps.ini
[common]
bind_port = 7000
vhost_http_port = 8080 (这里可以设置为公网服务器上的其他端口)
```
2.启动frps 
```
./frpc -c ./frpc.ini
```
3.修改 frpc.ini 文件，假设 frps 所在的服务器的 IP 为 x.x.x.x，local_port 为本地机器上 web 服务对应的端口, 绑定自定义域名 www.yourdomain.com:
```
# frpc.ini
[common]
server_addr = x.x.x.x
server_port = 7000

[web]
type = http
local_port = 80 (相当于把内外服务器的80端口映射到公网服务器的8080端口)
custom_domains = www.yourdomain.com (这里也可以设置为公网服务器的公网ip)
```
4.启动 frpc：
```
./frpc -c ./frpc.ini
```
5.将 www.yourdomain.com 的域名 A 记录解析到 IP x.x.x.x，如果服务器已经有对应的域名，也可以将 CNAME 记录解析到服务器原先的域名。   
6.通过浏览器访问 http://www.yourdomain.com:8080 即可访问到处于内网机器上的 web 服务,如果上面配置的custom_domains是公网ip地址，也可以直接通过 http://公网ip:8080 访问

### 一台客户端上不同端口的web应用想通过服务端映射
1.修改 frps.ini 文件
```
# frps.ini
[common]
bind_port = 7000
```
2.启动frps 
```
./frpc -c ./frpc.ini
```
3.修改 frpc.ini 文件
```
# frpc.ini
[common]
server_addr = x.x.x.x
server_port = 7000

[web]
type = tcp （此处采用tcp模式）
local_ip = 127.0.0.1 (本机ip)
local_port = 9001 （本机web端口）
remote_port = 9001 （远程服务上的映射端口）

[web2]
type = tcp（此处采用tcp模式）
local_ip = 127.0.0.1 (本机ip)
local_port = 8999 （本机web端口）
remote_port = 8999 （远程服务上的映射端口）
```
4.启动 frpc：
```
./frpc -c ./frpc.ini
```
5.在浏览器使用 x.x.x.x:9001  x.x.x.x:8999 进行访问。

[frp帮助文档](https://github.com/fatedier/frp/blob/master/README_zh.md)
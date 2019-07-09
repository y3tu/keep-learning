<!-- GFM-TOC -->
* [一、OpenJDK8](#OpenJDK8)
* [二、Redis单机版](#Redis单机版)
    * [安装](#安装)
    * [启动](#启动)
* [三、Docker](#Docker)
    * [使用脚本安装docker](#使用脚本安装docker)
    * [在docker中安装rabbitMQ](#在docker中安装rabbitMQ)
<!-- GFM-TOC -->

# OpenJDK8
>当前的所有命令都在root下进行   

1.yum搜索OpenJDK8软件包
* `yum search java-1.8|grep openjdk`
![搜索结果](https://i.loli.net/2019/05/07/5cd0ed46c7eac.png)

2.安装的版本是java-1.8.0-openjdk-devel.x86_64
* `yum install java-1.8.0-openjdk-devel.x86_64 -y`
![QQ20190507-103812@2x.png](https://i.loli.net/2019/05/07/5cd0efbb7c53f.png)

3.检查安装是否成功并查看版本
* `java -version`
![QQ20190507-104337@2x.png](https://i.loli.net/2019/05/07/5cd0f0fea45b6.png)

# Redis单机版
## 安装
1.下载redis安装包   
`wget http://download.redis.io/releases/redis-4.0.6.tar.gz`
![QQ20190507-111136@2x.png](https://i.loli.net/2019/05/07/5cd0f78166236.png)
2.解压压缩包   
`tar -zxvf redis-4.0.6.tar.gz`  
3.yum安装gcc依赖   
`yum install gcc`   
4.跳转到redis解压目录下   
`cd redis-4.0.6`   
5.编译安装   
`make MALLOC=libc`
## 启动
### 1.后台进程方式启动redis   
* 修改redis.conf文件,将daemonize no修改为daemonize yes。
* 进入src目录，指定redis.conf文件启动`./redis-server /y3tu/redis-4.0.6/redis.conf`

### 2.远程连接redis   
redis现在的版本开启redis-server后，redis-cli只能访问到127.0.0.1，因为在配置文件中固定了ip，因此需要修改redis.conf（有的版本不是这个文件名，只要找到相对应的conf后缀的文件即可）文件以下几个地方。   
* bind 127.0.0.1改为 #bind 127.0.0.1 (注释掉)
* protected-mode yes 改为 protected-mode no

# Nacos
1.下载Nacos安装包   
`wget https://github.com/alibaba/nacos/releases/download/1.0.0/nacos-server-1.0.0.tar.gz`   
2.解压压缩包   
`tar -zxvf nacos-server-1.0.0.tar.gz`   
3.修改配置   
打开conf/application.properties添加Nacos数据源配置   
![QQ20190507-123855@2x.png](https://i.loli.net/2019/05/07/5cd10beb4af8d.png)
4.启动单机版   
进入bin目录，执行`nohup sh startup.sh -m standalone &`   
5.查询管理界面   
浏览器输入`http://主机ip:8848/nacos/`

# Docker
## 使用脚本安装docker
1.使用 sudo 或 root 权限登录 Centos.   
2.确保 yum 包更新到最新。    
`yum update`   
3.执行 Docker 安装脚本。
```
$ curl -fsSL https://get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh
```
执行这个脚本会添加 docker.repo 源并安装 Docker。

4.启动 Docker 进程。

`sudo systemctl start docker`   
5.验证 docker 是否安装成功并在容器中执行一个测试的镜像。
```
sudo docker run hello-world
docker ps
```
## 在docker中安装rabbitMQ
1.进入docker hub镜像仓库地址：https://hub.docker.com/    
2.搜索rabbitMq，进入官方的镜像，可以看到以下几种类型的镜像；我们选择带有“mangement”的版本（包含web管理页面）
![xx1](https://images2018.cnblogs.com/blog/1107037/201808/1107037-20180809223206824-1435694565.png)
3.拉取镜像   
* `docker pull rabbitmq:3.7.7-management`
* `docker images` 查看所有镜像
![xx2](https://images2018.cnblogs.com/blog/1107037/201808/1107037-20180809225400982-948353369.png)            
4.根据下载的镜像创建和启动容器   
```
docker run -d --name rabbitmq3.7.7 -p 5672:5672 -p 15672:15672 -v `pwd`/data:/var/lib/rabbitmq --hostname myRabbit -e RABBITMQ_DEFAULT_VHOST=my_vhost  -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin df80af9ca0c9
```
说明:   
* -d 后台运行容器
* --name 指定容器名
* -p 指定服务运行的端口（5672：应用访问端口；15672：控制台Web端口号）
* -v 映射目录或文件
* --hostname  主机名（RabbitMQ的一个重要注意事项是它根据所谓的 “节点名称” 存储数据，默认为主机名）
* -e 指定环境变量；（RABBITMQ_DEFAULT_VHOST：默认虚拟机名；RABBITMQ_DEFAULT_USER：默认的用户名；RABBITMQ_DEFAULT_PASS：默认用户名的密码）   
5.使用命令：`docker ps` 查看正在运行容器
![xx3](https://images2018.cnblogs.com/blog/1107037/201808/1107037-20180810001344561-1044122568.png)   
6.可以使用浏览器打开web管理端：`http://Server-IP:15672`
![xx4](https://images2018.cnblogs.com/blog/1107037/201808/1107037-20180810001642216-1307723408.png)
    







    
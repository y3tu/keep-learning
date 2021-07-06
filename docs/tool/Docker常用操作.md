[TOC]

## 使用脚本安装docker
1.使用 sudo 或 root 权限登录 Centos.   
2.确保 yum 包更新到最新。    
```yum update```   
3.执行 Docker 安装脚本。
```
$ curl -fsSL https://get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh
```
执行这个脚本会添加 docker.repo 源并安装 Docker。

4.启动 Docker 进程。

```sudo systemctl start docker```   
5.验证 docker 是否安装成功并在容器中执行一个测试的镜像。
```
sudo docker run hello-world
docker ps
```

## 安装docker-compose
```
curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```   
给docker-compose执行权限，运行命令：

```chmod +x /usr/local/bin/docker-compose```   
检查，运行
```docker-compose --version```

## docker常用命令
* docker的启动、停止、重启
```
service docker restart containerId(容器id)---重启
service docker stop containerId(容器id)---停止
service docker start containerId(容器id)---启动
```
* 创建一个容器
```
docker run -d -p 8081:8081 -v /y3tu/log/y3tu-admin/:/logs/y3tu-admin y3tu-admin:1.0 --name admin

-d 后台运行
-p 设置端口宿主主机和docker端口映射
-v 挂在宿机目录,/y3tu/log/y3tu-admin/是宿机目录，/logs/y3tu-admin是当前docker容器的目录，宿机目录必须是绝对的。
--name 给容器起一个名字,可省略,省略的话docker会随机产生一个名字

```
* 启动的容器列表         
```docker ps```   
* 查看创建的所有容器     
```docker ps -a```    
* 查看指定容器的日志记录     
```docker logs -f containerId(容器id)```   
* 删除某个容器,若正在运行,需要先停止     
```docker rm containerId(容器id)``` 
* 杀死所有正在运行的镜像
```docker kill $(docker ps -a -q)```     
* 删除所有已经停止的容器   
```docker rm $(docker ps -a -q)```  
* 删除所有镜像
```docker rmi $(docker images -q)```  
* 强制删除无法删除的镜像  
```
docker rmi -f <IMAGE_ID>  
docker rmi -f $(docker images -q)
```
* 进入一个已经在运行的容器
```
docker exec -it 775c7c9ee1e1 /bin/bash 
775c7c9ee1e1 代表容器ID
输入exit退出容器
```
* 修改镜像的tag标签
```docker tag upms-server:1.2 upms-server:1.4```

## 如何把本地镜像上传到docker hub
1.首先在[https://hub.docker.com](https://hub.docker.com)注册账号   
2.```docker login``` 输入注册的用户名密码    
3.使用```docker tag```改变镜像名和tag，如果你的docker hub用户名是y3tu,那么镜像名应改为y3tu/upms-server:1.2   
```docker tag upms-server:1.2 y3tu/upms-server:1.2```   
4.```docker push y3tu/upms-server:1.2(镜像名:tag标签) ``` 有时push会超时,多push几次      
4.查看hub中是否已经有上传好的镜像,可以使用```docker pull y3tu/upms-server:1.2```下载镜像了

## 搭建私有docker仓库
https://www.cnblogs.com/subendong/p/9029495.html

## Jenkins与Docker的自动化CI/CD实战
https://blog.51cto.com/qiuyt/2163950

## 参考
[Docker菜鸟教程](https://www.runoob.com/docker)


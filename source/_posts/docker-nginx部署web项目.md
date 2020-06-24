---
title: docker-nginx部署web项目
type: categories
copyright: true
date: 2020-06-16 10:42:24
tags: [云服务器, web, nginx, 部署, docker]
categories:
description:
---

### 写在前面
作为web端开发er，几乎从未接触过后端及服务端相关知识。但有些场景下又需要将自己的一些东西从公网（域名）上展示出来，比如自己的可开源项目、博客、简历、demo等。有时候我们可以麻烦后台或者运维同事来帮我们搞一搞，但终究授之以鱼不如授之以渔，在同事的帮助下，我也成功的通过一条简单的途径实现了本地文件上传至服务器并从域名访问。本片博客记录下实现过程。
本实现途径是在linux服务器中安装docker，然后启用docker-nginx代理实现的，希望对与我面对同样困境的小白有所帮助。
第一次接触服务端知识，有错敬请指教，感谢！
那么，开始吧~

<!--more-->

### 0. 准备
- 云服务器——CentOS 7.x，低配置即可，如[1核 1GB 1Mbps]
- 域名——已备案
- 终端远程工具——如：xshell、FinalShell等

***提示：以下命令行内容均在服务器中完成~自行通过终端远程工具连接自己的服务器***

-----
### 1. 安装docker
#### 1.1 卸载旧版本
***如已安装想重新安装，或者是旧版本，则执行。
如已是新版，跳过本步。***
```
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```
#### 1.2 使用官方脚本自动安装
***以下两种方式任选其一***
- 命令：
`curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun`
- 国内 daocloud 命令
`curl -sSL https://get.daocloud.io/docker | sh`

#### 1.3 启动docker
`$ sudo systemctl start docker`

-----

### 2. Docker 安装 Nginx
Nginx 是一个高性能的 HTTP 和反向代理 web 服务器，同时也提供了 IMAP/POP3/SMTP 服务 。
docker内安装nginx,作为web服务器来提供我们所需要的的web服务。
#### 2.1 docker查看nginx可用版本
- `docker search nginx`
```
[root@VM_0_10_centos zxm]# docker search nginx
NAME                               DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
nginx                              Official build of Nginx.                        13333               [OK]                
jwilder/nginx-proxy                Automated Nginx reverse proxy for docker con…   1822                                    [OK]
richarvey/nginx-php-fpm            Container running Nginx + PHP-FPM capable of…   777                                     [OK]
linuxserver/nginx                  An Nginx container, brought to you by LinuxS…   115                                     
bitnami/nginx                      Bitnami nginx Docker Image                      84                                      [OK]
tiangolo/nginx-rtmp                Docker image with Nginx using the nginx-rtmp…   75                                      [OK]
alfg/nginx-rtmp                    NGINX, nginx-rtmp-module and FFmpeg from sou…   66                                      [OK]
...
...
```

#### 2.2 安装最新的nginx镜像
`$ docker pull nginx:latest`

#### 2.3 查看本地镜像
运行命令查看是否安装成功：`$ docker images`
```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              2622e6cca7eb        5 days ago          132MB
```
如上，有nginx，成功。

-----

### 3. 创建docker-nginx实例
`$ docker run --name [name] -p 8080:80 -d nginx`
> - [name]—— 容器名称
> - [-p 8080:80]—— 端口进行映射，将本地 8080 端口映射到容器内部的 80 端口
> - [-d nginx]—— 设置容器在在后台一直运行

-----
### 4. 运行容器
创建容器后默认是运行状态。可以通过`$ docker ps`命令查看运行中的容器，通过`$ docker ps -a`查看docker的所有容器。
如，我创建的容器名为`zxm-nginx-test`，可以在下面容器列表中查看到容器的运行状态及容器信息（id、name等）
```
[root@VM_0_10_centos zxm]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
daad54bac361        nginx               "/docker-entrypoint.…"   41 hours ago        Up About an hour    0.0.0.0:8080->80/tcp   zxm-nginx-test
```
如果容器未启动，则可以通过`$ docker start [容器name或id]`来启动容器。

-----

### 5. 测试访问
运行容器启动之后，可以通过服务器公网ip地址测试访问。
如：`http://[ip]:8080/`。
出现页面提示如下，表示成功运行。
![](/images/posts/serve_nginx.png)

-----

### 6. 上传文件到服务器
上传本地文件/文件夹（我们的web项目，静态资源）到自己的服务器任意位置。
比如我在root下创建了文件夹zxm，通过工具直接将本地文件`Loading.html`拖入其中。

![](/images/posts/serve_upload.gif)
然后本地资源就上传到了服务器中~
![](/images/posts/serve_file.jpg)

### 7. 拷贝文件到docker-nginx容器
首先，我们已经将需要的项目（资源）从本地上传到了服务器上；
然后我们需要做的则是从服务器上的项目资源从目前所在的地方拷贝到docker-nginx容器内，那么我们就需要知道三个东西：
1. 目标路径
2. 项目所在路径
3. docker 拷贝命令

接下来，我们一步一步来。
#### 7.1 查找docker-nginx容器目标文件路径
- 进入容器： `docker exec -it daad54bac361 bash`
```
$ docker exec -it daad54bac361 bash
```
- 目录： `ls`
```
root@daad54bac361:/# ls 
bin  boot  dev  docker-entrypoint.d  docker-entrypoint.sh  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```
- 查找配置文件： `cd etc/nginx/conf.d/`
```
root@daad54bac361:/# cd etc/nginx/conf.d/
root@daad54bac361:/etc/nginx/conf.d# ls
default.conf
```
- 查看配置文件： `cat default.conf`
```nginx
root@daad54bac361:/etc/nginx/conf.d# cat default.conf 
server {
    listen       80;
    listen  [::]:80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    #error_page  404              /404.html;

    ...
}
```
- 复制（记录）文件中`server>location>root`
如：`/usr/share/nginx/html`
这个目录则是容器的目标目录，接下来则需将其他位置的资源通过docker命令拷贝到该文件目录下。

- 退出容器： `exit`
```
root@daad54bac361:/etc/nginx/conf.d# exit
e xit
```
#### 7.2 拷贝
- 先回到刚才本地上传文件至服务器步骤的文件所在服务器位置中
- cd到该目录下
- docker命令拷贝到容器中
`docker cp [源文件目录] [容器id]:[目标目录]`
例如，在我的服务器中，想要将`Loading.html`传入到容器中，则是：
`docker cp Loading.html daad54bac361:/usr/share/nginx/html/`

#### 7.3 完成，访问
浏览器中输入`ip:8080/Loading.html`，正常访问到页面，收工~~
![](/images/posts/serveLoading.gif)

----- 

### 8. nginx域名映射配置
以上内容我们完成了基本的服务器项目部署，也就是从公网可以访问自己的项目啦~但是我们不能一直使用公网来访问，不够安全，且不够优雅！那么我们怎么使用域名来访问自己的项目呢？
接下来我们操作一下域名映射。

#### 8.1 docker内安装vim工具
- 进入docker容器
`docker exec -it [容器id或者name] bash`
- docker内安装vim
依次执行以下命令：
`apt-get update`
`apt-get install vim`

#### 8.2 配置`default.conf`文件
- cd到配置文件目录
```
root@daad54bac361:/# cd etc/nginx/conf.d/ 
root@daad54bac361:/etc/nginx/conf.d# ls
default.conf
```

- 编辑default.conf文件:
`vim default.conf`

- 修改如下：
将`server>server_name`的localhost改成自己的域名地址，如我的是`www.zhuxingmin.com`，但我可能会设置二级域名等，将`www`改成`*`。则配置如下：`server_name  *.zhuxingmin.com;`
```nginx
server {
    listen       80;
    listen  [::]:80;
    server_name  *.zhuxingmin.com;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    ...
}
```

- 退出容器
`exit`

#### 8.3 重启docker-nginx容器
`docker restart [容器name或id]`
如：
```
[root@VM_0_10_centos zxm]# docker restart zxm-nginx-test
zxm-nginx-test
[root@VM_0_10_centos zxm]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
daad54bac361        nginx               "/docker-entrypoint.…"   40 hours ago        Up 5 seconds        0.0.0.0:8080->80/tcp   zxm-nginx-test
```

### 9. 域名解析
- 前提： 域名已备案
- 打开域名解析
- 添加记录（解析）
```
记录类型:   A
主机记录:   test.zhuxingmin.com
解析线路:   默认
记录值：    服务器公网ip
TTL:       10分钟
```

然后，打开浏览器，输入自己配置的域名，即可访问到docker-nginx容器中的项目啦~~

![](/images/posts/serve_url.gif)

以上。



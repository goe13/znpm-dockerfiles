# znpm-dockerfiles

## 简介
用 Docker 容器服务的方式搭建 znmp 环境，易于维护、升级。使用前需了解 Docker 的基本概念，常用基本命令。
可以一条条命令执行docker命令来构建镜像，容器。这里推荐使用 docker-compose 来管理，执行项目，下面是使用流程。

相关软件版本：
- PHP 7.4
- MySQL 5.7
- Nginx 1.17
- Redis 5.0.8

用到的 PHP 拓展()：
- redis 
- swoole
- imagick
- mcrypt
- oci8

## 使用
### 1.安装 Docker，Docker-compose  
- Docker，详见官方文档：https://docs.docker.com/engine/installation/linux/docker-ce/centos/
- docker-compose，文档：https://docs.docker.com/compose/install/
```
sudo pip install -U docker-compose
```

### 2.下载 znpm-dockerfiles
直接 clone：
```
git clone git@github.com:goe13/znpm-dockerfiles.git
```
或者下载 zip 压缩包也可以。

### 3.下载需要的拓展包
先下载好要使用的拓展包，如果编译出错要多次构建容器就可以省掉下载时间。
```
cd znpm-dockerfiles/files

wget https://pecl.php.net/get/redis-3.1.6.tgz -O php/pkg/redis.tgz  
```

### 4.docker-compose 构建项目
进入 docker-compose.yml 所在目录：
执行命令：
创建私有桥接网络:`docker network create app_net`
```
docker-compose up
```  

如果没问题，下次启动时可以以守护模式启用，所有容器将后台运行：  
```
docker-compose up -d
``` 

使用 docker-compose 基本上就这么简单，Docker 就跑起来了，用 stop，start 关闭开启容器服务。  
更多的是在于编写 dockerfile 和 docker-compose.yml 文件。 

可以这样关闭容器并删除服务：
```
docker-compose down
```

### 5. 使用 Composer

我们在创建 PHP-fpm 容器时就已经将 Composer 安装在容器中，可以运行该容器进行 Composer 操作。

用 docker-compose 进行操作：
```
docker-compose run --rm -w /data/www/your-project-dir php-fpm composer update
```
`-w /data/www/your-project-dir`为在php-fpm的工作区域，znpm项目也是挂载在里面，所有我们可以直接在容器里运行composer。

或者进入宿主机（容器外部）vhosts 目录下用 docker 命令：
```
cd znmp-dockerfiles/vhosts

docker run -it --rm -v `pwd`:/data/www/ -w /data/www/your-project-dir files_php-fpm composer update
```

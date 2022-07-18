# Wordpress 安装过程

> 通过 `Docker` 安装

## 一、环境准备

| 序号 | 名称   | 版本   | 注释           |
| ---- | ------ | ------ | -------------- |
| 1    | ubuntu | 18.04  | 本人系统版本； |
| 2    | docker | latest | 环境必须安装！ |

## 二、docker 创建network

```shell
sudo docker network create --subnet=172.18.0.0/20 wordpress
```

## 三、安装MySQL

> 版本5.7

#### 2.1 安装

```shell
docker pull mysql:5.7
```

#### 2.2 启动

```shell
# 参考: https://hub.docker.com/_/mysql
$ docker run -d -p 3307:3306 \
-v /usr/local/etc/mysql-5.7/conf:/etc/mysql/conf.d \
-v /usr/local/etc/mysql-5.7/datadir:/var/lib/mysql \
-e MYSQL_DATABASE=wordpress \
-e MYSQL_ROOT_PASSWORD=lee99999. \
--network wordpress --ip 172.18.0.2 \
--name mysql-wordpress mysql:5.7
```

## 四、安装Wordpress

> 本地必须有docker

#### 3.1 安装

```shell
docker pull wordpress
```

#### 3.2 配置

> 简单Dockerfile定制下wordpress

```shell
# 创建进入
$ mkdir ./dockerfile-wordpress && cd ./dockerfile-wordpress
# 文件夹下
$ ls .
Dockerfile  php.ini

# dockerfile 定制内容
# 这里可以使用docker pull iskeke/wordpress:1.0.0 跳过
$ cat Dockerfile
FROM wordpress
MAINTAINER isKEKE<li_99999@126.com>
COPY php.ini /usr/local/php.ini
RUN cp /usr/local/php.ini /usr/local/etc/php

# build
docker build -t wordpress:1.0.0 .
```

#### 3.3 启动

```shell
# 参考: https://hub.docker.com/_/wordpress
# 比较菜，没去做么nginx+ssl。
$ docker run -d -p 80:80 \
--name wordpress \
--network wordpress \
--ip 172.18.0.3 \
-v /usr/local/etc/wordpress/content:/var/www/html/wp-content \
iskeke/wordpress:1.0.0
```

## 五、注释

> `isKEKE`

`email`

- li_99999@126.com

`gihub`

- https://github.com/isKEKE

`blog`

- http://www.iekeke.top/
# Wordpress搭建属于自己的小博客

## 一、环境准备

| 名称      | 版本    | 注释                                                         |
| --------- | ------- | ------------------------------------------------------------ |
| Ubuntu    | 18.04   | 非绝对，只是作者本人使用系统版本是这个。                     |
| Docker    | lastest | 直接安装最新即可。                                           |
| Wordpress | lastest | WEB服务，通过docker安装。                                    |
| MySQL     | 5.7     | 数据库，非绝对，只是作者本人使用版本是这个，通过docker安装。 |
| Nginx     | lastest | 反向代理用，通过docker安装。                                 |

> 这里不说如何安装docker。
> 官方安装文档链接：https://docs.docker.com/engine/install/

- 当你完成`docker`的安装，先去创建一个专属于wordpress的网络环境

  ```shell
  # Usage: docker network create [OPTIONS] NETWORK
  $ sudo docker network create --subnet 172.18.0.0/20 wordpress
  ```

## 二、MySQL数据库的搭建

> 参考链接：https://hub.docker.com/_/mysql

- 通过`docker`下载镜像

  ```shell
  $ sudo docker pull mysql:5.7
  ```

- 启动指令

  ```shell
  $ sudo docker run -d \
  --name wordpress_mysql \
  --network wordpress \
  --ip 172,18.0.2 \
  -v /home/keke/wordpress/mysql/conf:/etc/mysql/conf.d \
  -v /home/keke/wordpress/mysql/datadir:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=数据库的密码 \
  -e MYSQL_DATABASE=启动容器自动创建库名称 \
  -p 3306:3306 \
  mysql:5.7 \
  --character-set-server=utf8mb4 \
  --collation-server=utf8mb4_unicode_ci
  ```
  > MYSQL_DATABASE：这个东西在wordpress配置的时候要用到！其中端口转发可以去掉，主要防止设置错误才写。

## 三、Wordpress的搭建

> 参考链接：https://hub.docker.com/_/wordpress

- 通过`docker`下载镜像

  ```shell
  $ sudo docker pull wordpress
  ```


- 启动指令

  ```shell
  # 这里数据库配置其实可以设置成启动指令，当然了我比较喜欢可视化。
  # 可以去参考链接查看如何使用。
  $ sudo docker run -d \
  --name wordpress_blog \
  --network wordpress \
  --ip 172.18.0.3 \
  -v /home/keke/wordpress/html:/var/www/html \
  -p 8080:80 wordpress
  ```

  > 注意：稍后搭建nginx，这里wordpress_blog容器可以删除转发端口。

- 设置配置（`PS: 会写启动指令，这些可以忽略！`）

  - 如何配置那个**数据库主机**

    ```shell
    # 查看MySQL容器的IP
    $ sudo docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' wordpress_mysql
    # 我这里返回的结果为172.18.0.2，所有`数据库主机`填写172.18.0.2:3306
    ```


## 四、Nginx的搭建

> 参考链接：https://hub.docker.com/_/nginx

- 在你的当前主题文件`functions.php`末尾出添加如下代码

  - 这步骤是nginx会和wordpress中的重定位冲突，你使用的当前主题必须在对应的functions.php添加这个代码！

  - `remove_filter("temp;ate_redirect", "redirect_canonical");`

- 开始之前先登录`wordpress`的后台，登录链接：http://{host}:8080/login；修改先服务器默认的URL链接：

  - PS: 为什么不改WordPress地址（URL），因为爬修改失误访问不了后台，这样还需要手动去访问MySQL数据库修改！

- 通过`docker`下载镜像

  ```shell
  $ sudo docker pull nginx
  ```

- 启动指令

  ```shell
  $ sudo docker run -d \
  -p 80:80 \
  -p 443:443 \
  --name wordpress_nginx \
  --network wordpress \
  --ip 172.18.0.4 \
  -v /home/keke/wordpress/conf.d:/etc/nginx/conf.d \
  nginx
  ```

  > -v /home/keke/wordpress/conf.d:/etc/nginx/conf.d，这个参数是通过docker的容器卷技术，让nginx配置文件映射到本地，这样我们配置就更加方便

- `nginx`常用命令

  ```shell
  # 停止
  $ nginx -s stop
  # 安全退出
  $ nginx -s quit
  # 重新加载配置文件
  $ nginx -s reload
  # 查看nginx进程
  ps aux | grep nginx
  ```

- 设置配置

  ```shell
  # 进入到本地/home/keke/wordpress/conf.d文件夹下
  $ cd /home/keke/wordpress/conf.d
  # 创建wordpress_nginx的配置文件
  $ sudo touch ./wordpress.conf
  # 查看wordpress_blog的IP
  $ sudo docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' wordpress_blog
  # 我的wordpress_blog的ip: 172.18.0.3, 所有下面些172.18.0.3:80
  # 填写下列内容
  $ sudo vim ./wordpress.conf
  upstream blog {
      server 这里是wordpress_blog容器的IP;
  }
  server {
      listen 80;
      server_name 你服务器的域名或IP;
      location / {
              proxy_pass http://blog;
              proxy_set_header Host $http_host:$server_port;
     }
  }
  # 重新加载配置
  $ sudo docker exec wordpress_nginx nginx -s reload
  # 成功的打印信息，如下：
  # 2022/04/15 10:51:07 [notice] 43#43: signal process started
  ```

  > 这样你就可以进行访问你的博客了！
  >
  > 当然记得修改配置中WordPress地址（URL）链接端口为80，这也登录及后台端口就不会走8080！


## 五、作者

> 珂珂

​		这个搭建过程其实是我博客搭建过程，当然了并没有让博客支持https协议，我还没有搞ssl证书！至于为什么要用nginx一个是		证书问题，另一个是我有两台服务器，有一台有域名，但是开了个MC服务器，但资源没占完，另一台没有域名，只挂了一个天气预报的爬虫，资源空闲，所以使用nginx让它们进行资源共享，一台提供一部分的资源，另一台提供域名等！嘿嘿！PS：我还没安装到另一台，最近现实忙，这个文档是搭建过程写的！








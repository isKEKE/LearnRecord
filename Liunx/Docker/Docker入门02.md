#### 五、容器数据卷

##### 5.1 什么是容器数据卷？

- ```shell
  # 数据？如果数据都在容器中，那么我们容器删除，数据就会丢失！ 需求：数据可以持久化
  # 例如MYSQL，容器删除，删库跑路！ 需求：MYSQL数据可以存储到本地
  # 容器之间可以有一个数据共享的技术！Docker容器中产生的数据，同步到本地！
  # 这就是卷技术！目录的挂在，将我们容器内容的目录，挂载到liunx上面！
  ```

- ![image-20220218215207810](C:\Users\86131\Desktop\Docker学习\image\image-20220218215207810.png)

- **总结一句话：容器的持久化和同步操作！荣期间也是可以数据共享的！**

##### 5.2 使用容器卷

> 方式一：之间使用命令的来挂载 -v

- ```shell
  # docker run -it -v 主机目录:容器目录	
  docker run -it -v /home/keke/share:/home ubuntu:18.04 /bin/bash
  
  # 查看信息
  docker inspect 容器ID
  "Mounts": [
      {
          "Type": "bind",
          "Source": "/home/keke/share",		# 主机目录
          "Destination": "/home",				# 容器目录
          "Mode": "",
          "RW": true,
          "Propagation": "rprivate"
      }
  ],
  ```

- 好吃：以后修改只需要在本地修改即可，容器内会自动同步！

##### 5.3 实战：安装MYSQL

> 思考：MYSQL的数据持久化问题！

- ```shell
  # 获取镜像
  docker pull mysql:5.7
  
  # 运行容器，需要做数据挂在！安装启动mysql，需要配置密码的，这是注意点！
  # 官方测试：$ docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag
  '''
  docker run -d -p 3310:3306 -v /home/keke/mysql/conf:/etc/mysql/conf.d -v /home/keke/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=718293 --name=mysql mysql:5.7
  '''
  -d 后台运行
  -p 端口映射
  -v 卷挂载
  -e 环境配置
  --name 容器名称
  
  # 启动成功之后，本地使用 shell连接一下
  # 连接ubuntu服务器3310 -映射> mysql容器3306
  mysql -h localhost -P 3310 -uroot -p
  
  # 在本地测试创建一个数据，查看一下映射的路径是由OK
  ls /home/keke/mysql/data/ -l | grep test
  drwxr-x--- 2 999 docker     4096 2月  18 22:23 test
  ```

- 假设容器删除，发现挂载到本地的数据卷依旧没有丢失，这就实现了容器给持久化功能！

---

##### 5.4 具名挂载和匿名挂载

- ```shell
  # 匿名挂载
  -v 容器内路径！
  docker run -d -P --name=nginx01 -v /etc/nginx nginx
  
  # 查看所有volume的情况
  docker volume ls
  
  
  # 具名挂载
  root@keke-To-be-filled-by-O-E-M:/home/keke# docker volume ls
  DRIVER    VOLUME NAME
  local     6a8480a52f14f8d3db346f29e98bdedfef33444db28dd251f0be311485392327
  local     759af8c315f763e3a2e6e3ac8cee59a2c2eae940c88eccb3fd6595994c77a922
  local     830be080d1c9e9d03d8f9a8e9cba93d035a397a4352083e041fab09eeccc1950
  local     c3f0761fc08300d247789595aab296d3be038c633d99e5a235044bc4e01525c0
  local     e3c5ba3287347437096518c1b7a46ffa23624dd2b628589a61bc7cd6d0f6b352
  local     keke
  
  # 通过 -v 卷名:容器内路径
  # 查看一下卷位置
  root@keke-To-be-filled-by-O-E-M:/home/keke# docker inspect keke
  [
      {
          "CreatedAt": "2022-02-18T22:31:07+08:00",
          "Driver": "local",
          "Labels": null,
          "Mountpoint": "/var/lib/docker/volumes/keke/_data",
          "Name": "keke",
          "Options": null,
          "Scope": "local"
      }
  ]
  ```

- 所有docker容器内的卷，没有指定目录的情况下都是在`/var/lib/docker/volumes/keke/_data`
  我们通过具名挂载可以方便的找到我们的一个卷，大多数情况在使用`具名挂载`

- ```shell
  # 若何确定是具体挂载还是匿名挂载，还是指定路径挂载！
  -v 容器内路径			# 匿名挂载
  -v 卷ming:容器内路径	   # 具名挂载
  -v /宿主机路径:容器内路径	# 指定路径挂载
  
  ```

- 拓展

- ```shell
  # 通过-v 容器内路径:ro | rw 改写读写权限
  ro readonly	 # 只读
  rw readwrite # 可读可写
  
  # 一旦这个设置了容器权限，容器对我们挂载出的内容就有权限限定！
  docker run -d -P --name:nginx001 -v juming-nginx:/etc/nginx:ro nginx
  docker run -d -P --name:nginx002 -v juming-nginx:/etc/nginx:rw nginx
  
  # ro 只要看到ro就说明这个路径下只能通过宿主机操作；容器内是无法操作的！
  ```

---

##### 5.5 初始DockerFile

Dockerfile 就是用来构建docker镜像的构造文件!命令脚本！体验一下

> 方式二

通过这个脚本可以镜像可以生成镜像，镜像是一层一层的，脚本一个个的命令，每个命令都是一层！

- ```shell
  # 测试
  # 1.创建一个dockerfile文件名称，名字随机，建议Dockerfile
  # 2.文件内容
  FORM ubuntu:18.04					# 指令和参数，指令全大写
  
  VOLUME ["volume01", "volume02"]		# 匿名挂载
  
  CMD echo "----- end -----"
  
  CMD /bin/bash
  
  # --- 这里每个命令都是一层 ---
  root@keke-To-be-filled-by-O-E-M:/home/keke/docker-test-volume# docker build -f /home/keke/docker-test-volume/dockerfile -t keke/ubuntu:1.0 .
  Sending build context to Docker daemon  2.048kB
  Step 1/4 : FROM ubuntu:18.04
   ---> 5a214d77f5d7
  Step 2/4 : VOLUME ["volume01", "volume02"]
   ---> Running in cc2025bf43f2
  Removing intermediate container cc2025bf43f2
   ---> 8ed51d7bfd1d
  Step 3/4 : CMD echo "----- end -----"
   ---> Running in 4035776eedf9
  Removing intermediate container 4035776eedf9
   ---> 0ee3c6551fdb
  Step 4/4 : CMD /bin/bash
   ---> Running in 1680f0e89f7d
  Removing intermediate container 1680f0e89f7d
   ---> 82797e91f7bd
  Successfully built 82797e91f7bd
  Successfully tagged keke/ubuntu:1.0
  ```
```
  
- ![image-20220219233147980](C:\Users\86131\Desktop\Docker入门02\image\image-20220219233147980.png)

- ```shell
  # 这个卷和外部一定有同步的目录！
```

- ![image-20220220004506847](C:\Users\86131\Desktop\Docker入门02\image\image-20220220004506847.png) 

- 这种方式我们为了使用的十分多，因为我们通常会构建自己的镜像！
  假设构建镜像时候没有挂载卷，要手动镜像挂载 -v 

##### 5.6 数据卷容器

> 两个或多个mysql同步数据

- ![image-20220220204502068](C:\Users\86131\Desktop\Docker入门02\image\image-20220220204502068.png)

- ```shell
  docker run -it --name docker01 keke/ubuntu:1.0
  docker run -it --name docker02 --volumes-from docker01 keke/ubuntu:1.0
  
  # docker02 创建的内容，同步到了docker01上
  
  # 再创建一个，也可继承！只用通过--volumes-from 就可以实现容器间的共享
  docker run -it --name docker03 --volumes-from docker02 keke/ubuntu:1.0
  
  # 通过docker rm -f docker01，其他继承容器里面文件还是存在。
  # 测试结果存在！
  ```

- 多个mysql实现数据共享

- ```python
  docker run -d -p 3310:3306 -v /etc/mysql/conf.d -v /var/lib/mysql -e MYSQL_ROOT_PASSWORD=718293 --name=mysql01 mysql:5.7
   
  docker run -d -p 3310:3306 --volumes-from mysql01 -e MYSQL_ROOT_PASSWORD=718293 --name=mysql02 mysql:5.7
  
  # 这个时候就可以实现两个容器数据同步！
  ```

- 结论：容器之间配置信息的船体，数据卷容器的生命周期一直持续到没有人使用容器为主。
  但是一旦你持久化到了本地，这个时候，本地的数据是不会删除的!

  

#### 六、DockerFIle

Dockerfile是用来构建docker镜像的文件！命令参数脚本！

##### 6.1 构建步骤：

1. 编写一个dockerfile文件
2. docker build构建成为一个镜像
3. docker run 运行镜像
4. docker push 发布镜像（DockerHub、阿里云镜像仓库！）

---

##### 6.2 查看官方是如何写的

- ![image-20220220225415181](C:\Users\86131\Desktop\Docker入门02\image\image-20220220225415181.png)

- ```shell
  FROM scratch
  ADD ubuntu-bionic-oci-amd64-root.tar.gz /
  CMD ["bash"]
  ```

- 很多官方镜像都是基础包，很多功能没有，我们通常回自己搭建自己的镜像。
  官方既然可以制作镜像，那么我们也可以！

##### 6.3 Dockerfile构建过程

- 基础知识：
  1. 每个保留关键字（指令）都必须是大写字母
  2. 执行从上到下顺序执行
  3. `#` 表示注释
  4. 每一个指令都会创建提交的一个新的镜像层，并提交！
- ![image-20220220230125971](C:\Users\86131\Desktop\Docker入门02\image\image-20220220230125971.png)
- dockerfile是面向开发的，我们以后要发布项目，做镜像，就需要编写dockerfile文件，这个文件十分简单！
  Docker镜像逐渐成为企业交付的标准，必须掌握！
- 步骤：开发、部署、运维。。。缺一不可。
  - DockerFile：构建文件，定义了一切步骤，源代码
  - DockerImages: 通过DockerFile构建生成的镜像，最终发布和运行的产品
  - DockerContainer: 容器就是镜像运行起来提供服务器的

---

##### 6.4 DockerFIle的指令

以前的话我们就是使用别人的，现在我们知道了这些指令后，我们来练习自己写一个镜像

- ```shell
  FROM			# 基础镜像，一切重这里开始构建；ubuntu
  MAINTAINER		# 镜像是谁写的，姓名+邮箱！
  RUN				# docker镜像构建的时候需要运行的命令
  ADD				# 步骤，tomact镜像，这个tomcat的压缩包！添加内容！
  WORKDIR			# 镜像的工作目录 
  VOLUME			# 挂载的目录
  EXPOSE			# 暴露端口配置
  CMD				# 指定这个容器启动的时候运行的命令, 只有最后一个会生效，可被替代
  ENTRYPOINT		# 指定这个容器启动的时候运行的命令，可以追加命令！
  ONBUILD			# 当构建一个被继承DockerFile 这个时候就会运行ONBUILD 指令，触发指令。
  COPY			# 类似ADD，将我们文件拷贝到镜像中。
  ENV				# 构建的时候设置环境变量！
  ```

- ![image-20220220230649175](C:\Users\86131\Desktop\Docker入门02\image\image-20220220230649175.png)

##### 6.5 实战测试

- Docker Hub中99%镜像都是从这个基础镜像过来的，然后配置需要的软件和配置来进行构建。
- ![image-20220220231952358](C:\Users\86131\Desktop\Docker入门02\image\image-20220220231952358.png)

> 创建一个ubuntu

- ```shell
  # 1.编写dockerfile的文件
  root@keke-To-be-filled-by-O-E-M:/home/keke/dockerfile# cat ./mydocker-ubuntu
  
  FROM ubuntu:18.04
  
  MAINTAINER isKEKE<li_99999@126.com>
  
  ENV MYPATH /usr/local
  
  WORKDIR $MYPATH
  
  RUN apt update
  
  RUN apt -y install vim
  
  RUN apt -y install net-tools
  
  EXPOSE 80
  
  CMD echo $MYPATH
  
  CMD echo "----- end -----"
  
  CMD /bin/bash
  
  # 2.共同这个文件进行构建
  # docker build -f dockerfile文件路径 -t 镜像名字:[tag]
  Successfully built 77cec84bb659
  Successfully tagged myubuntu:0.1
  
  # 3.测试运行
  
  ```

- 对比：之前的原生ubuntu

- ![image-20220220234331736](C:\Users\86131\Desktop\Docker入门02\image\image-20220220234331736.png)

- 我们增加之后的镜像

- ![image-20220220234339679](C:\Users\86131\Desktop\Docker入门02\image\image-20220220234339679.png)

- 我们可以列出本地镜像的变更历史

- ![image-20220220234655517](C:\Users\86131\Desktop\Docker入门02\image\image-20220220234655517.png)

- 我们平时拿到一个镜像，可以研究一个它是怎么做的！

##### 6.6 CMD 和 ENRTYPOINT 区别

> CMD

- ```shell
  EXPOST			# 暴露端口配置
  CMD				# 指定这个容器启动的时候运行的命令, 只有最后一个会生效，可被替代
  ```

- ```shell
  # 测试 CMD
  # 编写dockefile文件
  cat ./dockerfile-cmd-test
  FROM ubuntu:18.04
  CMD ["ls", "-a"]
  
  # build构建镜像
  docker build -f dockerfile-cmd-test -t test:1.0 .
  
  # run运行,发现我们ls -a 生效了
  root@keke-To-be-filled-by-O-E-M:/home/keke/dockerfile# docker run test:1.0
  ...
  srv
  sys
  tmp
  usr
  var
  
  # 像追加一个命令 -l ls -al
  docker run test:1.0 -l
  docker: Error response from daemon: OCI runtime create failed: container_linux.go:380: starting container process caused: exec: "-l": executable file not found in $PATH: unknown.
  ERRO[0000] error waiting for container: context canceled
  
  # cmd的情况下， -l 替换了CMD ["ls", "a"] 命令，-l 不是一个命令所以报错！
  ```

> ENTRYPOINT

- ```shell
  root@keke-To-be-filled-by-O-E-M:/home/keke/dockerfile# docker build -f dockerfile-cmd-test-2 -t test:2.0 .
  Sending build context to Docker daemon  4.096kB
  Step 1/2 : FROM ubuntu:18.04
   ---> 5a214d77f5d7
  Step 2/2 : ENTRYPOINT ["ls", "-a"]
   ---> Running in c87bc58936e3
  Removing intermediate container c87bc58936e3
   ---> 09cee478a139
  Successfully built 09cee478a139
  Successfully tagged test:2.0
  root@keke-To-be-filled-by-O-E-M:/home/keke/dockerfile# docker images
  REPOSITORY            TAG       IMAGE ID       CREATED         SIZE
  test                  2.0       09cee478a139   4 seconds ago   63.1MB
  test                  1.0       0f8167b9afe9   6 minutes ago   63.1MB
  nginx                 latest    605c77e624dd   7 weeks ago     141MB
  tomcat                9.0       b8e65a4d736d   8 weeks ago     680MB
  tomcat                latest    fb5657adc892   8 weeks ago     680MB
  redis                 latest    7614ae9453d1   2 months ago    113MB
  mysql                 5.7       c20987f18b13   2 months ago    448MB
  ubuntu                18.04     5a214d77f5d7   4 months ago    63.1MB
  portainer/portainer   latest    580c0e4e98b0   11 months ago   79.1MB
  elasticsearch         7.6.2     f29a1ee41030   23 months ago   791MB
  root@keke-To-be-filled-by-O-E-M:/home/keke/dockerfile# docker run test:2.0
  .
  ..
  .dockerenv
  bin
  boot
  dev
  etc
  home
  lib
  lib64
  media
  mnt
  opt
  proc
  root
  run
  sbin
  srv
  sys
  tmp
  usr
  var
  
  # 我们的追加命令，是直接拼接在我们的ENTRYPOINT命令的后面！
  root@keke-To-be-filled-by-O-E-M:/home/keke/dockerfile# docker run test:2.0 -l
  total 72
  drwxr-xr-x   1 root root 4096 Feb 20 16:23 .
  drwxr-xr-x   1 root root 4096 Feb 20 16:23 ..
  -rwxr-xr-x   1 root root    0 Feb 20 16:23 .dockerenv
  drwxr-xr-x   2 root root 4096 Sep 30 12:34 bin
  drwxr-xr-x   2 root root 4096 Apr 24  2018 boot
  drwxr-xr-x   5 root root  340 Feb 20 16:23 dev
  drwxr-xr-x   1 root root 4096 Feb 20 16:23 etc
  drwxr-xr-x   2 root root 4096 Apr 24  2018 home
  drwxr-xr-x   8 root root 4096 May 23  2017 lib
  drwxr-xr-x   2 root root 4096 Sep 30 12:33 lib64
  drwxr-xr-x   2 root root 4096 Sep 30 12:32 media
  drwxr-xr-x   2 root root 4096 Sep 30 12:32 mnt
  drwxr-xr-x   2 root root 4096 Sep 30 12:32 opt
  dr-xr-xr-x 244 root root    0 Feb 20 16:23 proc
  drwx------   2 root root 4096 Sep 30 12:33 root
  drwxr-xr-x   5 root root 4096 Sep 30 12:34 run
  drwxr-xr-x   2 root root 4096 Sep 30 12:34 sbin
  drwxr-xr-x   2 root root 4096 Sep 30 12:32 srv
  dr-xr-xr-x  13 root root    0 Feb 20 16:23 sys
  drwxrwxrwt   2 root root 4096 Sep 30 12:34 tmp
  drwxr-xr-x  10 root root 4096 Sep 30 12:32 usr
  drwxr-xr-x  11 root root 4096 Sep 30 12:33 var
  ```

- DockerFile中很多命令都十分的相似，我们需要了解它们的区别，我们最好的学习就是对比它们然后测试！

---

##### 6.7 实战：Tomcat镜像

- 步骤

  1. 准备镜像文件tomcat压缩包，jdk的压缩包！

  2. 编写Dockerfile文件，官方命名`Dockerfile`,build会自动寻找这个文件，就不需要-f指定了

     - ```shell
       FROM centos
       
       MAINTAINER isKEKE<li_99999@126.com>
       
       COPY readme.txt /usr/local/readme.txt
       
       ADD jre-8u321-linux-x64.tar.gz /usr/local/
       
       ADD apache-tomcat-9.0.58.tar.gz /usr/local/
       
       ENV MYPATH /usr/local
       
       WORKDIR $MYPATH
       
       ENV JAVA_HOME $MYPATH/jre1.8.0_321
       
       ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
       
       ENV CATALINA_HOME $MYPATH/apache-tomcat-9.0.58
       
       ENV CATALINA_BASH $MYPATH/apache-tomcat-9.0.58
       
       ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINNA_HOME/bin
       
       EXPOSE 8080
       
       CMD $CATALINA_HOME/bin/startup.sh && tail -F $CATALINA_HOME/logs/catalina.out
       ```
     
  3. 构建镜像

     - ```shell
       $ docker build -t diytomcat:1.0 .
       ```
     
  4. 启动镜像

     - ```shell
       $ docker run -d -p 9090:8080 --name diy-tomcat \
       -v /home/keke/tmp/build/tomcat/test:/usr/local/apache-tomcat-9.0.58/webapps/test \
       -v /home/keke/tmp/build/tomcat/tomcatlogs/:/usr/local/apache-tomcat-9.0.58/logs diytomcat:1.0
       ```

  5. 测试访问

     - ![image-20220224170159507](C:\Users\86131\Desktop\Docker入门02\image\image-20220224170159507.png)

  6. 发布项目（邮局做了卷挂载，我们直接在本地编写项目就可以）

     - ```jsp
 // index.jsp      
       <%@ page language="java" contentType="text/html; charset=UTF-8"
           pageEncoding="UTF-8"%>
       <!DOCTYPE html>
       <html>
       <head>
       <meta charset="utf-8">
       <title>Hello, KEKE!(runoob.com)</title>
       </head>
       <body>
       Hello World!<br/>
       <%
       System.out.println("---my test web logs---");
       %>
       </body>
       </html>
       ```

     - ```xml
       // web.xml
       <?xml version="1.0" encoding="UTF-8"?>
       <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
                xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                             http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
                version="4.0"
                metadata-complete="true">
       </web-app>
       ```
     
     - 发现：项目部署成，可以直接访问ok!
  
- 我们一行开发的步骤：需要掌握DOckerfile的编写！我们之后一切都是使用docker发布运行！

---

##### 6.8 发布自己的镜像

> DockerHub

1. 地址https://hub.docker.com/注册自己的账号

2. 确定找个账号可以登录

3. 在我们服务器上提交自己的镜像

   - ```shell
     root@keke-To-be-filled-by-O-E-M:/home/keke/tmp# docker login --help
     
     Usage:  docker login [OPTIONS] [SERVER]
     
     Log in to a Docker registry.
     If no server is specified, the default is defined by the daemon.
     
     Options:
       -p, --password string   Password
           --password-stdin    Take the password from stdin
       -u, --username string   Username
     ```

4. 登录完毕之后就可以提交镜像，就是一步 docker push

   - ```shell
     root@keke-To-be-filled-by-O-E-M:/home/keke/tmp# docker push --help
     
     Usage:  docker push [OPTIONS] NAME[:TAG]
     
     Push an image or a repository to a registry
     
     Options:
       -a, --all-tags                Push all tagged images in the repository
           --disable-content-trust   Skip image signing (default true)
       -q, --quiet                   Suppress verbose output
     ```

   - ```shell
     # 提交镜像
     $ docker push diytomcat
     # 默认会拒绝，因为已经有了,带上自己的信息
     The push refers to repository [docker.io/iskeke/diytomcat]
     An image does not exist locally with the tag: iskeke/diytomcat
     # 通过tag加些信息
     $ docker tag 140b0129d661 iskeke/tomcat:1.0
     # 提交
     $ docker push iskeke/diytomcat:1.0
     
     # 自己发布的镜像尽量带上版本号
     $ docker push iskeke/tomcat:1.0
     The push refers to repository [docker.io/iskeke/tomcat]
     2ab09a3ca514: Pushed
     c99db74bbf74: Pushed
     634266ce9b30: Pushed
     74ddd0ec08fa: Mounted from library/centos
     1.0: digest: sha256:628e7ae08b186006739d34efda2263623f5e36759bc274c9a9b31e527ab5d2af size: 1160
     ```

   - 提交的时候也是按照镜像的层次进行提交的！


> 阿里云镜像服务器上

1. 登录阿里云

2. 找到容器镜像服务

3. 创建命名空间

   - ![image-20220224173646128](C:\Users\86131\Desktop\Docker入门02\image\image-20220224173646128.png)

4. 创建镜像仓库

   - ![image-20220224173714311](C:\Users\86131\Desktop\Docker入门02\image\image-20220224173714311.png)

5. 浏览阿里云

   - ![image-20220224173755510](C:\Users\86131\Desktop\Docker入门02\image\image-20220224173755510.png)

   - ```shell
     $ docker login --username=iskeke99999 registry.cn-hangzhou.aliyuncs.com
     Password:
     WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
     Configure a credential helper to remove this warning. See
     https://docs.docker.com/engine/reference/commandline/login/#credentials-store
     Login Succeeded
     
     $ docker push image:tag
     ```

---

##### 6.9 总结

![image-20220224181948749](C:\Users\86131\Desktop\Docker入门02\image\image-20220224181948749.png)

#### 七、Docker 网络

##### 7.1 理解Docker0

> 测试

- 先清空当前镜像

- ```shell
  $ip addr
  ```

- ![image-20220224183704052](C:\Users\86131\Desktop\Docker入门02\image\image-20220224183704052.png)

- 三个网络

  ```shell
  # 问题：docker 是如何出来容器网络访问的？
  
  $ docker run -d -P --name tomcat-1 tomcat:2.0
  
  # 查看容器的内容网络地址
  docker exec -it tomcat-1 apt install -y iproute2 iproute2-doc # 默认官方没有ip指令
  docker exec -it tomcat-1 ip addr
  root@keke-To-be-filled-by-O-E-M:/home/keke# docker exec -it tomcat-1 ip addr
  1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
      link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
      inet 127.0.0.1/8 scope host lo
         valid_lft forever preferred_lft forever
  29: eth0@if30: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
      link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
      inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
         valid_lft forever preferred_lft forever
          
  # 思考。linux能不能ping通容器给内部网络
  root@keke-To-be-filled-by-O-E-M:/home/keke# ping 172.17.0.2
  PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
  64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.134
  
  # liunx 可以ping 通docker 容器内部
  ```

> 原理

1. 我们每启动一个docker容器，docker就会给docker容器分配一个ip，我们只要安装了docker，就会有一个网卡docker0.
   模式: 交接模式，使用的技术是evth-pair技术！

   ```shell
   $ ifconfig # 主机
   ...
   veth742cd8d: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
           inet6 fe80::9cdd:e2ff:fedf:4fa0  prefixlen 64  scopeid 0x20<link>
           ether 9e:dd:e2:df:4f:a0  txqueuelen 0  (以太网)
           RX packets 4305  bytes 296977 (296.9 KB)
           RX errors 0  dropped 0  overruns 0  frame 0
           TX packets 6657  bytes 9593393 (9.5 MB)
           TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
   ...
   
   $ docker exec -it tomcat ifconfig # 容器
   ...
   29: eth0@if30: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
       link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
       inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
          valid_lft forever preferred_lft forever
   ...
   ```

2. 再次启动一个容器测试

   ```shell
   $ ifconfig # 主机
   
   $docker exec -it tomcat-1 ifconfig	# 容器 tomcat-1
   eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
           inet 172.17.0.3  netmask 255.255.0.0  broadcast 172.17.255.255
           ether 02:42:ac:11:00:03  txqueuelen 0  (Ethernet)
           RX packets 24  bytes 3774 (3.6 KiB)
           RX errors 0  dropped 0  overruns 0  frame 0
           TX packets 0  bytes 0 (0.0 B)
           TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
   ```

   


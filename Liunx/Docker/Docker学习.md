# Docker 学习

#### 一、文档资料

官方: 		https://www.docker.com/

文档地址: https://docs.docker.com/

仓库地址: https://hub.docker.com/

#### 二、Docker 安装

###### 环境准备

- liunx: ubuntu18.04

- 支持ssh等

###### 环境准备

```python
# 系统版本
ann@ubuntu:~/Desktop$ cat /etc/os-release 
NAME="Ubuntu"
VERSION="18.04.6 LTS (Bionic Beaver)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 18.04.6 LTS"
VERSION_ID="18.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=bionic
UBUNTU_CODENAME=bionic
```

###### 开始安装

- 卸载旧安装包

```ubuntu
 sudo apt-get remove docker docker-engine docker.io containerd runc
```

- 使用仓库安装

```python
# 1.需要的环境安装包
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

# 2.添加 Docker 的官方 GPG 密钥
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    
# 3.设置镜像仓库：（使用阿里云镜像地址）
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] http://mirrors.aliyun.com/docker-ce/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    
# 4、安装 Docker 引擎 docker-ce 社区版
apt update
sudo apt-get install docker-ce docker-ce-cli containerd.io

# 5、验证 Docker 引擎是否已正确安装。
docker version
```

- 测试

```python
# 1.运行 hello-world
sudo docker run hello-world

# 2.查看以下下载的hello-world镜像是否存在
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    feb5d9fea6a5   4 months ago   13.3kB

$ sudo docker image

# /var/lib/docker 这是docker的默认工作路径！
```

- 卸载

  https://docs.docker.com/engine/install/ubuntu/#uninstall-docker-engine

- 镜像加速

  > [容器镜像服务 (aliyun.com)](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)

#### 三、Docker 常用命令

##### 3.1 帮助命令

```
docker version;			# 显示版本信息。
docker info;			# 显示系统信息，包含镜像和容器的数量。
docker command --help;  # 万能命令；帮助命令。
```

- 帮助文档的地址：https://docs.docker.com/reference/

##### 3.2 镜像命令

**docker images**

- ```python
  root@ubuntu:/home/ann# docker images 
  REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
  hello-world   latest    feb5d9fea6a5   4 months ago   13.3kB
  
  # 解释
  REPOSITORY	镜像的仓库源
  TAG			镜像的表情
  IMAGE ID	镜像的id
  CREATED 	镜像的创建时间
  SIZE		镜像大小
  ```

- | 名称，简写     | 描述                             |
  | -------------- | -------------------------------- |
  | `--all`,`-a`   | 显示所有图像（默认隐藏中间图像） |
  | `--quiet`,`-q` | 仅显示图像 ID                    |

---

**docker search 搜索镜像**

- ```
  NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
  mysql                             MySQL is a widely used, open-source relation…   12114     [OK]       
  mariadb                           MariaDB Server is a high performing open sou…   4645      [OK]       
  mysql/mysql-server                Optimized MySQL Server Docker images. Create…   905       [OK]
  phpmyadmin                        phpMyAdmin - A web interface for MySQL and M…   451       [OK]       
  centos/mysql-57-centos7           MySQL 5.7 SQL database server                   92                   
  mysql/mysql-cluster               Experimental MySQL Cluster Docker images. Cr…   92                   
  centurylink/mysql                 Image containing mysql. Optimized to be link…   59        [OK]
  ```

- ```python
  # 可选项，通过收藏来过滤
  # -f, --filter filter   Filter output based on conditions provided
  # 搜索处的STARS数量都是大于3000的
  docker search --filter=STARS=3000 mysql
  ```

---

**docker pull 下载镜像**

- ```python
  '''第一种下载方式, 默认是最新版本`latest`'''
  root@ubuntu:/home/ann# docker pull mysql
  Using default tag: latest
  latest: Pulling from library/mysql
  72a69066d2fe: Pull complete 	# 分层下载，docker image的核心 联合文件系统
  93619dbc5b36: Pull complete 
  99da31dd6142: Pull complete 
  626033c43d70: Pull complete 
  37d5d7efb64e: Pull complete 
  ac563158d721: Pull complete 
  d2ba16033dad: Pull complete 
  688ba7d5c01a: Pull complete 
  00e060b6d11d: Pull complete 
  1c04857f594f: Pull complete 
  4d7cfa90e6ea: Pull complete 
  e0431212d27d: Pull complete 
  Digest: sha256:e9027fe4d91c0153429607251656806cc784e914937271037f7738bd5b8e7709
  Status: Downloaded newer image for mysql:latest
  docker.io/library/mysql:latest	# 真实地址
  # docker pull mysql === docker.io/library/mysql:latest   等价的
  
  '''第二种，指定版本下载'''
  # docker pull 镜像名[:tag]
  root@ubuntu:/home/ann# docker pull mysql:5.7
  5.7: Pulling from library/mysql
  72a69066d2fe: Already exists 
  93619dbc5b36: Already exists 
  99da31dd6142: Already exists 
  626033c43d70: Already exists 
  37d5d7efb64e: Already exists 
  ac563158d721: Already exists 
  d2ba16033dad: Already exists 	
  0ceb82207cd7: Pull complete 
  37f2405cae96: Pull complete 
  e2482e017e53: Pull complete 
  70deed891d42: Pull complete 
  Digest: sha256:f2ad209efe9c67104167fc609cca6973c8422939491c9345270175a300419f94
  Status: Downloaded newer image for mysql:5.7
  docker.io/library/mysql:5.7
  
  ```

---

**docker rmi 删除镜像**

- ```python
  # 通过镜像ID删除
  root@ubuntu:/home/ann# docker rmi -f IMAGE_ID
  Untagged: mysql:5.7
  Untagged: mysql@sha256:f2ad209efe9c67104167fc609cca6973c8422939491c9345270175a300419f94
  Deleted: sha256:c20987f18b130f9d144c9828df630417e2a9523148930dc3963e9d0dab302a76
  Deleted: sha256:6567396b065ee734fb2dbb80c8923324a778426dfd01969f091f1ab2d52c7989
  Deleted: sha256:0910f12649d514b471f1583a16f672ab67e3d29d9833a15dc2df50dd5536e40f
  Deleted: sha256:6682af2fb40555c448b84711c7302d0f86fc716bbe9c7dc7dbd739ef9d757150
  Deleted: sha256:5c062c3ac20f576d24454e74781511a5f96739f289edaadf2de934d06e910b92
  
  # 删除多个
  docker rmi -f IMAGE_ID IMAGE_ID IMAGE_ID 
          
  # 删除所有的镜像
  docker rmi -f $(docker images -aq)
  ```

- 

##### 3.3 容器命令

> 说明：有了镜像才可以创建容器, liunx,下载一个ubuntu镜像来测试学习
>
> docker pull ubuntu:18.04

**新建容器并启动**

- ```python
  docker run [可选参数] image
  
  # 参数说明
  --name="Name"	# 容器名字；用来区分容器
  -d				# 以后台方式运行
  -it				# 使用交互方式运行，进入容器查看内容
  -p				# 指定容器的端口	8080:8080
  	-p ip:主机端口:容器端口
  	-p 主机端口:容器端口 （常用）
      -p 容器端口
  -P				# 随机指定端口
  
  # --- 分割 ---
  # 启动并进入容器；这是是基础版本，很多命令都是不完善的！
  root@ubuntu:/home/ann# docker run -it ubuntu:18.04 /bin/bash
  root@563aca6c3848:/#
  ```

- `exit`   -  退出容器命令

---

**列出所有正在运行的的容器**

- ```python
  '''
  docker ps 命令
  -a		# 列出当前正在运行的+带出历史运行过的容器
  -n=? 	# 显示最近创建的容器，?=int数量
  -q		# 只显示ID
  '''
  root@ubuntu:/home/ann# docker ps -a
  CONTAINER ID   IMAGE          COMMAND       CREATED             STATUS                          PORTS     NAMES
  563aca6c3848   ubuntu:18.04   "/bin/bash"   3 minutes ago       Exited (0) About a minute ago             musing_satoshi
  4a9d00361bba   feb5d9fea6a5   "/hello"      About an hour ago   Exited (0) About an hour ago              vigilant_mclean
  3b20b775a45c   feb5d9fea6a5   "/hello"      About an hour ago   Exited (0) About an hour ago              keen_cartwright
  2b32fe15badc   feb5d9fea6a5   "/hello"      24 hours ago        Exited (0) 24 hours ago                   agitated_perlman
  f90ecafc5e57   feb5d9fea6a5   "/hello"      24 hours ago        Exited (0) 24 hours ago                   boring_archimedes
  5db533c60785   feb5d9fea6a5   "/hello"      24 hours ago        Exited (0) 24 hours ago                   trusting_satoshi
  ```

---

**退出容器**

- ```python
  exit 			# 直接退出并停止容器
  ctrl + P + Q 	# 容器不停止退出
  ```

---

**删除容器**

- ```
  # 删除单个, 不能删除正在运行的容器；如果要强制删除，-f
  docker rm 容器ID
  
  # 删除所有
  docker rm -f $(docker ps -aq)
  
  # 通过管道删除
  docker ps -a -q | xargs docker rm -f
  ```

---

**启动和停止容器的操作**

- ```python
  docker start 容器ID 		# 启动
  docker restart 容器ID		# 重启
  docker stop 容器ID		# 停止
  docker kill 容器ID		# 强删
  ```

##### 3.4 常用的其他命令

**后台启动**

- ```python
  # 命令：docker run -d 镜像名
  root@ubuntu:/home/ann# docker run -d ubuntu:18.04
  e095d78deb9dde88217dc594d211a0afba540bed543cba6d0fff5971471f7fc0
  
  # 问题docker ps 发现 ubuntu停止了
  # 常见坑：docker 容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止
  # nginx，但容器启动后，发现自己没有提供服务，就会立即停止，就没有程序了。
  ```

---

**查看日志命令**

- ```python
  docker logs --help
  
  # 自己编写一段shell脚本
  "while true; do echo keke; sleep 1; done"
  
  #docker ps -q
  6c95d0c4887d
  
  # 查看日志
  -tf		# 显示所有日志
  --taill NUMBER # 显示日志的条目
  docker -t -f --taill NUMBER 容器ID		# 显示行数日志
  ```

---

**查看容器中的进程信息**

- ```python
  '''docker top 容器ID'''
  UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
  root                4877                4853                0                   00:48               ?                   00:00:00            /bin/sh -c while true;do echo keke;sleep 1; done
  root                5267                4877                0                   00:52               ?                   00:00:00            sleep 1
  
  ```

---

**查看镜像的元数据**

- ```python
  '''docker inspect 容器ID'''
  
  # 测试
  [
      {
          "Id": "6c95d0c4887d6e4d6188cda7c436f0e73559a8e3e80ba4d0b37b71ba7825d7c0",
          "Created": "2022-02-16T08:48:16.559750671Z",
          "Path": "/bin/sh",
          "Args": [
              "-c",
              "while true;do echo keke;sleep 1; done"
          ],
          "State": {
              "Status": "running",
              "Running": true,
              "Paused": false,
              "Restarting": false,
              "OOMKilled": false,
              "Dead": false,
              "Pid": 4877,
              "ExitCode": 0,
              "Error": "",
              "StartedAt": "2022-02-16T08:48:16.856729232Z",
              "FinishedAt": "0001-01-01T00:00:00Z"
          },
          "Image": "sha256:5a214d77f5d747e6ed81632310baa6190301feeb875cf6bf9da560108fa09972",
          "ResolvConfPath": "/var/lib/docker/containers/6c95d0c4887d6e4d6188cda7c436f0e73559a8e3e80ba4d0b37b71ba7825d7c0/resolv.conf",
          "HostnamePath": "/var/lib/docker/containers/6c95d0c4887d6e4d6188cda7c436f0e73559a8e3e80ba4d0b37b71ba7825d7c0/hostname",
          "HostsPath": "/var/lib/docker/containers/6c95d0c4887d6e4d6188cda7c436f0e73559a8e3e80ba4d0b37b71ba7825d7c0/hosts",
          "LogPath": "/var/lib/docker/containers/6c95d0c4887d6e4d6188cda7c436f0e73559a8e3e80ba4d0b37b71ba7825d7c0/6c95d0c4887d6e4d6188cda7c436f0e735
  59a8e3e80ba4d0b37b71ba7825d7c0-json.log",
          "Name": "/competent_gould",
          "RestartCount": 0,
          "Driver": "overlay2",
          "Platform": "linux",
          "MountLabel": "",
          "ProcessLabel": "",
          "AppArmorProfile": "docker-default",
          "ExecIDs": null,
          "HostConfig": {
              "Binds": null,
              "ContainerIDFile": "",
              "LogConfig": {
                  "Type": "json-file",
                  "Config": {}
              },
              "NetworkMode": "default",
              "PortBindings": {},
              "RestartPolicy": {
                  "Name": "no",
                  "MaximumRetryCount": 0
              },
              "AutoRemove": false,
              "VolumeDriver": "",
              "VolumesFrom": null,
              "CapAdd": null,
              "CapDrop": null,
              "CgroupnsMode": "host",
              "Dns": [],
              "DnsOptions": [],
              "DnsSearch": [],
              "ExtraHosts": null,
              "GroupAdd": null,
              "IpcMode": "private",
              "Cgroup": "",
              "Links": null,
              "OomScoreAdj": 0,
              "PidMode": "",
              "Privileged": false,
              "PublishAllPorts": false,
              "ReadonlyRootfs": false,
              "SecurityOpt": null,
              "UTSMode": "",
              "UsernsMode": "",
              "ShmSize": 67108864,
              "Runtime": "runc",
              "ConsoleSize": [
                  0,
                  0
              ],
              "Isolation": "",
              "CpuShares": 0,
              "Memory": 0,
              "NanoCpus": 0,
              "CgroupParent": "",
              "BlkioWeight": 0,
              "BlkioWeightDevice": [],
              "BlkioDeviceReadBps": null,
              "BlkioDeviceWriteBps": null,
              "BlkioDeviceReadIOps": null,
              "BlkioDeviceWriteIOps": null,
              "CpuPeriod": 0,
              "CpuQuota": 0,
              "CpuRealtimePeriod": 0,
              "CpuRealtimeRuntime": 0,
              "CpusetCpus": "",
              "CpusetMems": "",
              "Devices": [],
              "DeviceCgroupRules": null,
              "DeviceRequests": null,
              "KernelMemory": 0,
              "KernelMemoryTCP": 0,
              "MemoryReservation": 0,
              "MemorySwap": 0,
              "MemorySwappiness": null,
              "OomKillDisable": false,
              "PidsLimit": null,
              "Ulimits": null,
              "CpuCount": 0,
              "CpuPercent": 0,
              "IOMaximumIOps": 0,
              "IOMaximumBandwidth": 0,
              "MaskedPaths": [
                  "/proc/asound",
                  "/proc/acpi",
                  "/proc/kcore",
                  "/proc/keys",
                  "/proc/latency_stats",
                  "/proc/timer_list",
                  "/proc/timer_stats",
                  "/proc/sched_debug",
                  "/proc/scsi",
                  "/sys/firmware"
              ],
              "ReadonlyPaths": [
                  "/proc/bus",
                  "/proc/fs",
                  "/proc/irq",
                  "/proc/sys",
                  "/proc/sysrq-trigger"
              ]
          },
          "GraphDriver": {
              "Data": {
                  "LowerDir": "/var/lib/docker/overlay2/4c0f897a7a48b88fa7573f454f93186a2d80443bf8d2705b8a6a983ff7faa259-init/diff:/var/lib/docker/o
  verlay2/d5057be4ddae6600367bd386de784876041e07d32bf0b1af5a22299d75944ba6/diff",
                  "MergedDir": "/var/lib/docker/overlay2/4c0f897a7a48b88fa7573f454f93186a2d80443bf8d2705b8a6a983ff7faa259/merged",
                  "UpperDir": "/var/lib/docker/overlay2/4c0f897a7a48b88fa7573f454f93186a2d80443bf8d2705b8a6a983ff7faa259/diff",
                  "WorkDir": "/var/lib/docker/overlay2/4c0f897a7a48b88fa7573f454f93186a2d80443bf8d2705b8a6a983ff7faa259/work"
              },
              "Name": "overlay2"
          },
          "Mounts": [],
          "Config": {
              "Hostname": "6c95d0c4887d",
              "Domainname": "",
              "User": "",
              "AttachStdin": false,
              "AttachStdout": false,
              "AttachStderr": false,
              "Tty": false,
              "OpenStdin": false,
              "StdinOnce": false,
              "Env": [
                  "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
              ],
              "Cmd": [
                  "/bin/sh",
                  "-c",
                  "while true;do echo keke;sleep 1; done"
              ],
              "Image": "ubuntu:18.04",
              "Volumes": null,
              "WorkingDir": "",
              "Entrypoint": null,
              "OnBuild": null,
              "Labels": {}
          },
          "NetworkSettings": {
              "Bridge": "",
              "SandboxID": "663ce5e3e65c0d6cc3a5db7c19d6ef6feb1a2afe008a0a810ad030adb3c494dc",
              "HairpinMode": false,
              "LinkLocalIPv6Address": "",
              "LinkLocalIPv6PrefixLen": 0,
              "Ports": {},
              "SandboxKey": "/var/run/docker/netns/663ce5e3e65c",
              "SecondaryIPAddresses": null,
              "SecondaryIPv6Addresses": null,
              "EndpointID": "baaea886e1e2d397269f64648b0c99f24e3554502385550eb6b4df30351e7a71",
              "Gateway": "172.17.0.1",
              "GlobalIPv6Address": "",
              "GlobalIPv6PrefixLen": 0,
              "IPAddress": "172.17.0.2",
              "IPPrefixLen": 16,
              "IPv6Gateway": "",
              "MacAddress": "02:42:ac:11:00:02",
              "Networks": {
                  "bridge": {
                      "IPAMConfig": null,
                      "Links": null,
                      "Aliases": null,
                      "NetworkID": "dccb1d778bc967ef51efff6c7ca9beface8d170e7205ace8ca2c988d7cf526a7",
                      "EndpointID": "baaea886e1e2d397269f64648b0c99f24e3554502385550eb6b4df30351e7a71",
                      "Gateway": "172.17.0.1",
                      "IPAddress": "172.17.0.2",
                      "IPPrefixLen": 16,
                      "IPv6Gateway": "",
                      "GlobalIPv6Address": "",
                      "GlobalIPv6PrefixLen": 0,
                      "MacAddress": "02:42:ac:11:00:02",
                      "DriverOpts": null
                  }
              }
          }
      }
  ]
  ```

---

**进入当前正在运行的容器**

- ```python
  # 通常容器都是使用后台方式运行的，需要进入容器，修改一些配置
  # 命令
  docker exec -it 容器ID bashShell
  # 测试
  root@6c95d0c4887d:/# ps -ef
  UID         PID   PPID  C STIME TTY          TIME CMD
  root          1      0  0 08:48 ?        00:00:00 /bin/sh -c while true;do echo keke;sleep 1; done
  root       1004      0  0 09:04 pts/0    00:00:00 /bin/bash
  root       1029      1  0 09:05 ?        00:00:00 sleep 1
  root       1030   1004  0 09:05 pts/0    00:00:00 ps -ef
              
  # 方式二：
  docker attach 容器ID
  # 测试
  root@ubuntu:/home/ann# docker attach 6c95d0c4887d
  keke
  keke
  ,,,
  正在执行当前的代码
  
  # docker exec		# 进入容器中开启一个新的终端（常用）
  # docker attach		# 进入容器正在执行的终端，不会启动新的进程
  
  ```

---

**从容器内拷贝到主机上**

- ```python
  '''docker cp 只要容器存在，不管是否在运行，都可以cp'''
  docker cp 容器id:容器内容	目的主机路径
  # 这里是一个手动过程，为了我是使用 -v 卷的技术，可以实现，自动同步。
  ```
##### 3.5 小结

![](.\Docker学习\image\image-20220217182149457.png)

##### 3.6 作业练习一

> Docker 安装Nginx

```
# 1.搜索镜像
# 2.下载镜像

'''docker run -d --name nginx001 -p 2000:80 nginx 运行测试'''
-d 后台运行 --name 容器名称 -p 宿主机端口:容器内部端口
# 测试
'''
root@keke-To-be-filled-by-O-E-M:~# curl localhost:2000
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
'''
```

- 思考问题：我们每次改动nginx配置文件，都需要进入容器内部？十分麻烦，我要是可以在容器外部提供一个映射路径，达到在容器修改文件名，容器内部就可以自动修改?-v 数据卷！==查看第五章==

---

##### 3.7 作业练习二

> Docker 来安装一个tomcat

- ```python
  # 官方的使用
  $ docker run -it --rm tomcat:9.0
  
  # 我们之前的启动都是后台，停止了容器之后，容器还是可以查看	docker run -it --rm,一般用来测试，用完就删除。
  
  # 下载，启动
  docker pull tomcat
  docker run -d -p 2000:8080 tomcat
  
  # 测试访问没问题
  # root@keke-To-be-filled-by-O-E-M:/home/keke# docker exec -it c8da7869be9a  /bin/bash
  # 发现问题
  1. linux命令少了;
  2. 没有 webapps
  -> 阿里云镜像的原因，默认是最小的镜像，所以不必要的都剔除了。
  # ---
  root@c8da7869be9a:/usr/local/tomcat# ls
  BUILDING.txt     LICENSE  README.md      RUNNING.txt  conf  logs            temp     webapps.dist
  CONTRIBUTING.md  NOTICE   RELEASE-NOTES  bin          lib   native-jni-lib  webapps  work
  root@c8da7869be9a:/usr/local/tomcat# cd ./webapps.dist/
  root@c8da7869be9a:/usr/local/tomcat/webapps.dist# ls
  ROOT  docs  examples  host-manager  manager
  root@c8da7869be9a:/usr/local/tomcat/webapps.dist# cd .
  root@c8da7869be9a:/usr/local/tomcat/webapps.dist# cd ..
  root@c8da7869be9a:/usr/local/tomcat# cp -r ./webapps.dist/* ./webapps
  ```

- 思考问题：我们以后要部署项目，如果每次都要进入容器是不是十分麻烦？例如：webapps， 我们在外部放置项目，就自动同步到内部就好了！

##### 3.8 作业练习三

> 部署es + kibana

- ```python
  # es 暴漏的端口很多
  # es 十分的耗内存
  # es 的数据一般需要放置到安全目录！需挂在
  # --net somenetwork ? 网络配置
  
  # 启动并下载
  $ docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.2
  
  # 启动之后 liunx就非常卡！ 
  # docker stats 查CPU的状态
  
  # 测试es成功否
  root@keke-To-be-filled-by-O-E-M:/home/keke# curl localhost:9200
  {
    "name" : "c609752ae649",
    "cluster_name" : "docker-cluster",
    "cluster_uuid" : "EQ2__ZFVT2iE6EGn1NGYoA",
    "version" : {
      "number" : "7.6.2",
      "build_flavor" : "default",
      "build_type" : "docker",
      "build_hash" : "ef48eb35cf30adf4db14086e8aabd07ef6fb113f",
      "build_date" : "2020-03-26T06:34:37.794943Z",
      "build_snapshot" : false,
      "lucene_version" : "8.4.0",
      "minimum_wire_compatibility_version" : "6.8.0",
      "minimum_index_compatibility_version" : "6.0.0-beta1"
    },
    "tagline" : "You Know, for Search"
  }
  
  ```

- ```python
  # 赶快关闭，增加内存限制; 修改配置文件 -> -e 环境配置修改
  $ docker run -d --name elasticsearch_ -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.6.2
  ```

- ![image-20220217220935652](.\Docker学习\image\image-20220217220935652.png)

- ==作业：使用kibana连接es？思考网络如何连接过去！==  第七章

##### 3.9 可视化

- portainer(先用这个)

  ```python
  docker run -d -p 8080:9000 \
  --restart=alway - v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
  ```

- Rancher(CI/CD再用)

###### 什么是 portainer?

DOkcer图形化界面管理工具！提供一个后台面包供我们操作！

- ```shell
  docker run -d -p 8080:9000 \
  --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /home/keke/public:/public --privileged=true portainer/portainer
  
  # 汉化参考
  https://www.quchao.net/Portainer-CN.html
  ```

- 访问外网：http://192.168.101.37:8080/

- ![选择本地即可](C:\Users\86131\Desktop\Docker学习\image\image-20220217230246355.png)

- ![Home](.\Docker学习\image\image-20220217230533213.png)

#### 四、Docker镜像讲解

##### 4.1 镜像是什么

- ```shell
  镜像是一种尽量及、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需要的所有内容，包括代码、运行时、库、环境变量和配置文件。
  
  所有的应用，直接打包docker镜像，就可以直接跑起来。
  
  如何得到镜像？
  - 从远程仓库下载
  - 朋友拷贝给你
  - 自己制作一个镜像DockerFile
  ```

---

##### 4.2 Docker镜像加载原理

>UnionFS（联和文件系统）

我们下载的时候看到的一层层就是这个。

![image-20220218210032269](.\Docker学习\image\image-20220218210032269.png)

> Docker 镜像加载原理

![image-20220218211053953](C:\Users\86131\Desktop\Docker学习\image\image-20220218211053953.png)

虚拟机Ubuntu好几个G，为什么Docker这里才60+M？

![](.\Docker学习\image\image-20220218211118007.png)

对于一个精简的OS，rootfs可以很小，只需要包含最基本的命令，工具和程序库就可以了，因为底层直接用Host的kernel，自己只需要提供rootfs就可以了。由此可见对于不同的liunx发行版，bootfs基本是一致的，rootfs会有差别，因此不用的发行版可以公用。

虚拟机时分钟级别，容器是秒级别！ 

---

##### 4.3 分层理解

> 分层的镜像

![image-20220218211615250](C:\Users\86131\Desktop\Docker学习\image\image-20220218211615250.png)

```shell
root@keke-To-be-filled-by-O-E-M:~# docker image inspect redis:latest
[
    {
        "Id": "sha256:7614ae9453d1d87e740a2056257a6de7135c84037c367e1fffa92ae922784631",
        "RepoTags": [
            "redis:latest"
        ],
        "RepoDigests": [
            "redis@sha256:db485f2e245b5b3329fdc7eff4eb00f913e09d8feb9ca720788059fdc2ed8339"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2021-12-21T12:42:49.755107412Z",
        "Container": "13d25f53410417c5220c8dfe8bd49f06abdbcd69faa62a9b877de02464bb04a3",
        "ContainerConfig": {
            "Hostname": "13d25f534104",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.12",
                "REDIS_VERSION=6.2.6",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-6.2.6.tar.gz",
                "REDIS_DOWNLOAD_SHA=5b2b8b7a50111ef395bf1c1d5be11e6e167ac018125055daa8b5c2317ae131ab"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"redis-server\"]"
            ],
            "Image": "sha256:e093f59d716c95cfce82c676f099b960cc700432ab531388fcedf79932fc81ec",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {}
        },
        "DockerVersion": "20.10.7",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.12",
                "REDIS_VERSION=6.2.6",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-6.2.6.tar.gz",
                "REDIS_DOWNLOAD_SHA=5b2b8b7a50111ef395bf1c1d5be11e6e167ac018125055daa8b5c2317ae131ab"
            ],
            "Cmd": [
                "redis-server"
            ],
            "Image": "sha256:e093f59d716c95cfce82c676f099b960cc700432ab531388fcedf79932fc81ec",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": null
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 112691373,
        "VirtualSize": 112691373,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/ec3808550897f67f52342fc5ee60daa3a7144b720b60346f1f31d10ad262b62b/diff:/var/lib/docker/overlay2/bb76c5dfc9fc4a0e97891f08a24034968465778540cecfad7200f88be4f8ce96/diff:/var/lib/docker/overlay2/4d41017269051b0d02234dc37f7bba659fea319f11e8e8425b9668404b5a17ff/diff:/var/lib/docker/overlay2/d497af7dca9d1cbc10e2cb458b18af9dbacd114bd6efda7e629e7f98a5b04e69/diff:/var/lib/docker/overlay2/ae3423fc7a07a1030a032f77caa6510b083238734f696d3ae72e5bffa2e21f2f/diff",
                "MergedDir": "/var/lib/docker/overlay2/5b848131edc40e55bf30f8f2cf27357b55cde163af3b800bd2c9741c8738f33a/merged",
                "UpperDir": "/var/lib/docker/overlay2/5b848131edc40e55bf30f8f2cf27357b55cde163af3b800bd2c9741c8738f33a/diff",
                "WorkDir": "/var/lib/docker/overlay2/5b848131edc40e55bf30f8f2cf27357b55cde163af3b800bd2c9741c8738f33a/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:2edcec3590a4ec7f40cf0743c15d78fb39d8326bc029073b41ef9727da6c851f",
                "sha256:9b24afeb7c2f21e50a686ead025823cd2c6e9730c013ca77ad5f115c079b57cb",
                "sha256:4b8e2801e0f956a4220c32e2c8b0a590e6f9bd2420ec65453685246b82766ea1",
                "sha256:529cdb636f61e95ab91a62a51526a84fd7314d6aab0d414040796150b4522372",
                "sha256:9975392591f2777d6bf4d9919ad1b2c9afa12f9a9b4d260f45025ec3cc9b18ed",
                "sha256:8e5669d8329116b8444b9bbb1663dda568ede12d3dbcce950199b582f6e94952"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```

理解：

- ![image-20220218211851902](C:\Users\86131\Desktop\Docker学习\image\image-20220218211851902.png)
- ![image-20220218212058893](C:\Users\86131\Desktop\Docker学习\image\image-20220218212058893.png)
- ![image-20220218212154659](C:\Users\86131\Desktop\Docker学习\image\image-20220218212154659.png)
- ![image-20220218212337620](C:\Users\86131\Desktop\Docker学习\image\image-20220218212337620.png)

> 特性

Docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部！

这一层就是我们通常说的容器层，容器之下的都叫镜像！

![image-20220218212900856](C:\Users\86131\Desktop\Docker学习\image\image-20220218212900856.png)

如何提交一个自己的镜像

---

##### 4.4 commit镜像

- ```shell
  docker commit 提交容器成为一个新的副本
  
  # 命令和git原理类似
  docker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像名:[TAG]
  ```

- 实战测试

- ```shell
  # 1.启动一个默认的tomcat
  
  # 2.发现这个默认的tomcat是没有webapps应用，镜像的原因，官方的镜像默认webapps下面是没有文件的！
  
  # 3.我自己拷贝进去了基本的文件
  
  # 4.将我们操作过的容器通过commit提交为一个镜像！我们以后就是用我们修改过的镜像即可，这就是我们自己的一个修改过的镜像！
  ```

- ![image-20220218213824500](C:\Users\86131\Desktop\Docker学习\image\image-20220218213824500.png)

学习方式：理解概念，但是一定要实践，最好实践和理论相结合一次搞定这个知识！

```shell
如果想要保存当前的容器的状态，就可以通过commit来提交，获得一个镜像；
就好比我们以前学习VM时候，快照！
```

==到这里才算入门docker==

  ```

  ```


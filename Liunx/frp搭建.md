# Frp搭建

## 安装&&配置

#### 服务端

> 本人系统：Debian10.5

- 下载

  > https://github.com/fatedier/frp/releases/tag/v0.42.0

- 安装

  ```shell
  sudo cp frps /usr/bin/ && \
  sudo mkdir /etc/frp && \
  sudo cp frps.ini /etc/frp/ && \
  sudo cp frps /etc/frp/ && \
  sudo cp ./systemd/* /etc/systemd/system/
  ```

- 配置

  ```shell
  # sudo vim /etc/frp/frps.ini
  [common]
  bind_port = 7000                 #客户端连接的端口
  max_pool_count = 10                    #最大连接数
  log_file = ./frps.log                  #日志路径
  log_level = info                       #日志信息
  log_max_days = 3
  ```

- 启动

  ```shell
  # 启动
  sudo systemctl start frps
  # 关闭
  sudo systemctl stop frps
  # 开机自启
  sudo systemctl enable frps
  ```

#### 客户端

> 本人系统Uubntu:18.04

- 下载同服务端一样

- 安装

   ```shell
  sudo cp frpc /usr/bin/ && \
  sudo mkdir -p /etc/frp && \
  sudo cp frpc.ini /etc/frp/ && \
  sudo cp frpc /etc/frp/ && \
  sudo cp ./systemd/* /etc/systemd/system/
  ```

- 配置

  ```shell
  [common]
  server_addr = 服务端的IP
  server_port = 7000 # 服务器端口
  
  [mc]
  type = tcp
  local_ip = 127.0.0.1
  local_port = 25565 # 本地映射端口
  remote_port = 36925 # 服务端转发端口
  ```

  
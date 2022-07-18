# Hexo+Nginx搭建博客

> https://blog.52zhuanke.cn/005.html

## 一、环境配置

- 换源

  - ```shell
    # 参考链接：https://developer.aliyun.com/mirror/centos?spm=a2c6h.13651102.0.0.6cc11b111xIJsj
    # 备份
    mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
    
    # 下载repo, 没有curl先安装
    yum install -y curl
    curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
    
    # 安装epel
    sudo yum install epel-release
    
    # 更新
    yum update
    
    # 清除缓存
    yum clean all
    
    # 生成缓存
    yum makecache
    ```

- 安装vim
  - ```shell
    yum install -y vim
    ```

- 安装nginx

  - ```shell
    yum install -y nginx
    ```

- 安装nodejs

  - ```shell
    yum install -y nodejs
    ```

## 二、Nodejs配置

```shell
# 将Node.js加入环境变量
# 获得node安装路径
type node

# 编辑环境配置文件
vim ~/.bash_profile

# 填写内容如下
export NODE_PATH="/usr/bin/node"	# 这个路径是type node输出
PATH=$NODE_PATH/bin:$NODE_PATH/lib/node_modules:$PATH:$HOME/bin # 自行修改
export PATH

# 使其生效
source ~/.bash_profile
```

## 三、安装Hexo

- 安装

  - ```shell
    # 安装npm
    yum install -y npm
    
    # 安装pm2 稍后要用到
    npm install -g pm2
    
    # 安装hexo
    npm install -g hexp-cli
    
    # 查看hexo是否安装成功
    $ hexo -v
    hexo-cli: 4.3.0
    os: linux 3.10.0-1160.62.1.el7.x86_64 CentOS Linux 7 (Core)
    node: 16.14.1
    v8: 9.4.146.24-node.20
    ...
    ```

- 配置

  - ```shell
    # hexo初始化
    $ cd /var
    $ hexo init blog
    $ cd ./blog
    # 安装扩展
    $ npm install
    # 生成静态资源
    $ hexo g
    # 开启本地服务器
$ hexo s
    '''现在可以访问了，默认端口4000‘’‘
    # 关闭防火墙或者可以自己开放对应端口
    systemctl stop firewalld.service
    # 效果如下
    ```
    
  - ![image-20220511152725914](http://raw.staticdn.net/iskeke/images/main/blog/202205111529987.png)
  
  - 到这部其实hexo就是运行成功，若有外网IP，那么通过http://外网IP:端口/就可以访问到你的hexo博客

- 后台运行hexo

  - ```shell
    # 进入blog文件夹
    cd /var/blog
    
    # 编辑hexo.js
    vim ./hexo.js
    
    # 编辑完成pm2后台启动hexo
    pm2 start hexo.js
    
    # pm2查看后台
    pm2 list
    ```

  - ```javascript
    // hexo.js 填写内容
    var spawn = require('child_process').spawn;
    // 其实就是等于执行hexo server -p 4000; 4000端口请自行修改
    free = spawn('hexo', ['server', '-p 4000']);
       
    free.stdout.on('data', function (data) {
       console.log('standard output:\n' + data);
    });
       
    free.stderr.on('data', function (data) {
       console.log('standard error output:\n' + data);
    });
       
    free.on('exit', function (code, signal) {
       console.log('child process eixt ,exit:' + code);
    });
    ```

  - ![image-20220511154034206](http://raw.staticdn.net/iskeke/images/main/blog/202205111540932.png)

## 四、Nginx反向代理到hexo

- 配置

  - ```shell
    # 进入到nginx配置文件夹中
    cd /etc/nginx/conf.d
    
    # 新建hexo.conf并编辑
    vim ./hexo.conf
    
    # 编写好之后重载配置
    nginx -s reload
    
    # 这时就可以直接访问你的博客了。
    ```

  - http://192.168.78.148/

  - ```shell
    # hexo.conf内容
    upstream hexo {
            server 192.168.78.148:4000;	# 关键点，这里设置内网IP:hexo开放端口。
    }
    
    server {
    listen 80;                   		# 监听80端口
       listen [::]:80;
       server_name  192.168.78.148;     # 域名或者外网IP，注意不是内网IP
       location / {
               proxy_set_header X-Real-IP $remote_addr;
               proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
               proxy_set_header Host $http_host;
               proxy_set_header X-Nginx-Proxy true;
               proxy_set_header Connection "";
               proxy_pass http://hexo;
               proxy_redirect default;
       }
    }
    ```

- 问题

  - ```shell
    '''
    2022/05/11 16:01:49 [crit] 2139#2139: *38 connect() to 192.168.78.148:4000 failed (13: Permission denied) while connecting to upstream, client: 192.168.78.1, server: 192.168.78.148, request: "GET / HTTP/1.1", upstream: "http://192.168.78.148:4000/", host: "192.168.78.148"
    '''
    # 如果nginx的error.log日志发现出现这种异常，临时关闭SeLinux
    $ setenforce 0
    ```

- 效果
  
  - ![image-20220511161034668](http://raw.staticdn.net/iskeke/images/main/blog/202205111610197.png)
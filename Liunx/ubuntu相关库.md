# Ubuntu命令记录

#### 一、常用命令

```python
# 立刻关机
poweroff

# 创建文件
touch 文件名称

# 查看隐藏文件
ls -a
# 显示文件详细信息, -h可选，人性化显示文件大小
ls -l [-h]
```

#### 二、更新

```python
# 阿里源
https://developer.aliyun.com/mirror/?spm=a2c6h.25603864.0.0.29534ccaDtzQIc
# 对源文件进行备份
cp /etc/apt/sources.list /etc/apt/sources.list.bak
# 更新
sudo apt-get update
sudo apt-get upgrade
```

#### 三、获得本地IP信息

```
ip addr show
hostname -I
```

#### 四、ssh链接Ubuntu系统

```python
# 进入Ubuntu 查看22 端口的情况，发现22端口并未开启；
netstat -ntlp|grep 22

# Ubuntu安装默认不安装ssh服务，打开SSH服务，开启22端口，执行以下命令；
sudo apt-get install openssh-server
sudo apt-get install ufw

# 防火墙启动
sudo ufw enable

# 防火墙开机自启
sudo ufw default deny

# 防火墙添加端口
sudo ufw allow 22

# 重启ssh
sudo /etc/init.d/ssh stop
sudo /etc/init.d/ssh start
```

#### 五、Screen库的应用

```python
# 创建screen会话
screen -S MC
# 创建窗口
ctrl+a c
# 显示窗口情况
ctrl+a w
# 切换下一个窗口
ctrl+a n | 空格
# 查询screen 会话
screen -ls
# 进入
screen -r Name
# 删除会话
screen -X -S 会话ID quit
```

#### 六、Github相关指令

```python
# 下载项目
git clone 项目地址
```

#### 七、JAVA安装

> https://www.cnblogs.com/ziyue7575/p/13898610.html

#### 八、SFTP安装

>https://blog.csdn.net/pegasusliuyong/article/details/103629793



- 重装nginx

这个特殊的命令对我有用。`sudo apt-get remove --purge nginx nginx-full nginx-common`

和`sudo apt-get install nginx`

- 安装pyexecjs

[(29条消息) ubuntu python3.6 安装 pyexecjs + nodejs/pyv8_True Truth-CSDN博客_execjs 安装nodejs](https://blog.csdn.net/u012939880/article/details/80302552)

- Node 环境

[stompJs出现TextEncoder is not defined解决方法 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/77852641)

[Ubuntu18.04 Install Node.js Npm - 简书 (jianshu.com)](https://www.jianshu.com/p/f3dad64d896a)

#### 九、Python3虚拟环境搭建

> https://www.cnblogs.com/HeZhengfa/p/10486841.html

```python
# 创建虚拟环境，会在当前目录创建.env
virtualenv .env --python=python3

# 激活虚拟环境
source .env/bin/activate

# 退出虚拟环境
deactivate
```


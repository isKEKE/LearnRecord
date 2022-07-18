# Git Help

## 一、Git

#### 1.1 Git Setup Configuration

```shell
# name
$ git config --global user.name "Your Name"
# email
$ git config -- global user.name "Your email"
# 
```

#### 1.2 初始化仓库

```shell
# 在指定的项目目录进行初始化仓库
$ git init
```

#### 1.3 简易操作-创建分支&&添加&&提交

```shell
# 创建一个名为master分支
$ git checkout -b master
# 添加内容
$ git add [dir | file] ...
# 提交内容
$ git commit -m "description"
```

#### 1.4 通过`status`显示工作目录和暂存区的状态

```shell
$ git status status || git status -s
```

> 如果出现中文乱码, 输入如下指令

```shell
$ git config --global core.quotepath false
```

#### 1.5 通过log显示日志或进行回滚

> Example

```shell
$ git log
'''
commit fa0ab70f5a59de868088c0a9b54e6dbfd79d6c18 (HEAD -> master)
Author: isKEKE <li_99999@126.com>
Date:   Mon Jul 18 21:34:22 2022 +0800

    update Liunx

commit aaba946ebe90631c7f15b889bb808622a3039425 (origin/master)
Author: isKEKE <li_99999@126.com>
Date:   Mon Jul 18 21:23:14 2022 +0800

    add Liunx
'''
```

> Rollback:

```shell
# 当前位置为> fa0ab70f5a59de868088c0a9b54e6dbfd79d6c18, 进行回滚.
$ git reset --hard [HEAD^ | HASH ID]
```

> Note: `git reset --hard [HEAD^ | HASH ID]`进行了回滚, 并不能再次回到回滚之前的状态，但可用通过以下

```shell
$ git reflog
'''
fa0ab70 (HEAD -> master) HEAD@{0}: commit: update Liunx
aaba946 (origin/master) HEAD@{1}: commit (initial): add Liunx
'''
# 我们只要知道对应的commit id进行回滚即可。
```

## 二、Github> 远程仓库

#### 2.1 创建你的RSA KEY

```shell
$ ssh-keygen -t rsa -C "Your email"
# 把`.ssh`下生成的id_rsa.pub设置到你的github上
```

#### 2.2 本地仓库和远程仓库进行绑定

```shell
$ git remote add {OriginName} git@github.com:{YourName}/{ProjectName}.git
# OriginName: 相当于你对远程仓库起的别名，本地pull和push用到它：
# YourName: 你的github用户名称；
# ProjectName: 远程仓库名称。
```

> 如果连接错误怎么删除配置的远程仓库

```shell
$ git remote rm {OriginName}
```

#### 2.3 Pull Origin

```shell
$ git pull {OriginName} [{BranchName}]
```

#### 2.4 Push Origin

```shell
# 如果是第一次，添加-u参数
$ git push [-u] {OriginName} {BranchName}
```




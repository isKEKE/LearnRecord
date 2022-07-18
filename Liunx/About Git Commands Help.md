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

```


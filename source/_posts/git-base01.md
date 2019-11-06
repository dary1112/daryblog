---
title: git的基本使用（一）
categories:
- 工具
tags:
- git
---

**本文主要介绍Git，实现基本的代码托管，适合初次接触Git的开发人员，高级用法请查阅后续文章。**

<br>

> [Git](https://git-scm.com 'https://git-scm.com') is a free and open source distributed version control system 
>
> Git是一个免费开源的分布式版本控制系统，用于快速高效地处理各种大小型的项目。



## Git用途

1. 托管代码到远程，分布式托管，避免本机磁盘损坏造成不可挽回的局面。
2. 版本控制，可以发布多个版本并且实现在各个版本之间来回穿梭（实现原理：文件快照，每个版本都会有一个文件快照，比直接备份文件快速便捷。因此，Git仓库又被称为版本库）。
3. 团队协作，强大的分支功能，可以快速实现团队协作。

## Git代码托管平台

* github：  [https://github.com](https://github.com/ 'https://github.com/')

  github是全球最大的开源社交编程及代码托管网站。

* coding：  [https://dev.tencent.com](https://dev.tencent.com/ 'https://dev.tencent.com/')

  与腾讯云达成战略合作，基于云计算技术的软件开发平台，集项目管理、代码托管、运行空间、质量控制为一体。 

* gitee：   [https://gitee.com](https://gitee.com/ 'https://gitee.com/')

   码云(gitee.com)是 OSCHINA.NET 推出的代码托管平台,支持 Git 和 SVN,提供免费的私有仓库托管。

   

## Git工作流程

![](/img/git示意图.jpg 'git流程示意图')

### 概念介绍
* workspace：本地工作空间
* Index：暂存区
* repository：本地版本库
* remote：远程版本库

### 工作流程

要实现代码托管，需要在本地init一个本地仓库，通过 *add* 命令添加到暂存区，然后 *commit* 提交到本地仓库（每一次提交都会产生一个新的版本），最后通过*push* 推送到远程。



## Git使用步骤

​	*本次以gitee为例做讲解，读者也可以选择github或者coding，使用步骤一致，步骤中使用 [] 中括号括起来的部分需要替换成你自己相应的内容，不需要保留中括号。*

​	**第一次安装git的时候要执行1-4步，否则从第5步开始**

1. 安装git工具，安装完成之后鼠标右键只要出现 `git bash here` 菜单即说明安装成功。

   [windows系统下载链接]( https://git-scm.com/download/win 'https://git-scm.com/download/win' )

   [MAC下载链接]( https://git-scm.com/download/mac 'https://git-scm.com/download/mac' )

2. 注册gitee的账号（或其他平台账号），修改个人空间地址，绑定邮箱。

3. 全局配置用户名和邮箱

   `git config --global user.name [个人空间地址用户名]` 

   `git config --global user.email [账号绑定的邮箱]`

4. 配置密钥对：生成公钥和私钥，用于上传代码时的安全验证

   在`git bash`里执行命令`ssh-keygen`  一路回车，就可以生成密钥对，默认密钥对是存放在`(/c/Users/[主机用户名]/.ssh/)` 。这个目录下有两个文件， .pub就是公钥，另外一个是私钥，这两个文件千万不要动！！！

   到线上（gitee或其他平台）打开设置->安全设置->ssh公钥，把本地的公钥文件全选复制进来，输入登录密码，就配置成功了。

   **第一次创建项目的时候执行5-7步，否则从第8步开始**

5. 创建本地仓库

   在本地创建一个项目文件夹，项目代码都在这个文件夹里，执行 `git init`  初始化一个本地git仓库，这个时候项目里会多出一个.git目录（这个目录默认是隐藏的，这里就是用来存放文件快照的地方），这个目录千万不要动！！！

6. 创建一个线上仓库

   登录gitee，新建仓库，输入项目名称，选择私有或者公开源代码（私有在加入合作者之前就只能你自己能查看，公开就意味着开源），下面的选框一个都不要勾（初始化的不是文件都来自于本地仓库，线上仓库不需要任何文件），最后点击创建就ok了。

7. 将本地仓库和线上仓库建立关联：`git remote add origin [线上仓库的SSH地址]`

   ​	<small>如果在执行这句话的时候报错：`fatal: remote origin already exists.`</small>

   ​	<small>那么就先执行 `git remote rm origin`</small>

   ​	<small>再重新执行 `git remote add origin [线上仓库的SSH地址]`</small>

8. 代码添加到暂存区  `git add -A`  (也可以 git add [文件名] 来单独添加某一个文件)

9. 代码提交到本地仓库  `git commit -m '[说明本次提交所作的操作，越详细越好]' `

10. 代码推送到远程 `git push origin master`

## 附录1：Git常见命令
* `git init`&nbsp;&nbsp;&nbsp;&nbsp;初始化仓库
* `git config`&nbsp;&nbsp;&nbsp;&nbsp;配置用户信息
* `git remote`&nbsp;&nbsp;&nbsp;&nbsp;新增或者删除远程仓库的关联
* `git add`&nbsp;&nbsp;&nbsp;&nbsp;添加到暂存区
* `git commit`&nbsp;&nbsp;&nbsp;&nbsp;代码提交（每一次commit都会有一个新的版本号）
* `git push`&nbsp;&nbsp;&nbsp;&nbsp;推送到远程仓库
* `git status`&nbsp;&nbsp;&nbsp;&nbsp;查看当前仓库的状态
* `git log`&nbsp;&nbsp;&nbsp;&nbsp;查看日志（每一个commit在这里都能查看到，而且commit后面的随机字符串就是版本号），按字母q 退出log
* `git reset --hard [要回退的版本号]`&nbsp;&nbsp;&nbsp;&nbsp;回退到之前的某一个版本
* `git clone [线上仓库的https地址]`&nbsp;&nbsp;&nbsp;&nbsp;把线上仓库代码克隆到本地
* `git pull`&nbsp;&nbsp;&nbsp;&nbsp;在已有的仓库基础上拉取最新的线上代码

## 附录2：使用Git时候的一些注意事项
* 一个本地仓库对应一个远程仓库
* 远程代码和本地代码要保持统一
* .git 文件不能嵌套（仓库不能嵌套）




---
title: git的基本使用（一）
categories:
- 工具
tags:
- git
---

# Git

 [Git](https://git-scm.com) is a free and open source distributed version control system 

git是一个免费开源的分布式版本控制系统，用于快速高效地处理从小型到大型的项目

### Git用途

* 托管代码到远程
* 版本控制（文件快照）
* 团队协作

### Git代码托管平台

* github：  [https://github.com](https://github.com/) 
* coding： https://dev.tencent.com/ 
* 码云：     [https://gitee.com](https://gitee.com/) 



**workspace**（本地工作空间）  ---add---> **Index**（暂存区）   ---commit--->   **repository**（本地版本库）   ---push---> **remote**（远程版本库）

![](/img/git示意图.jpg)



### git使用流程

1. **第一次安装git的时候要执行1-3步，否则从第4步开始。**安装git工具，注册gitee的账号，修改个人空间地址，绑定邮箱

2.  `git config --global user.name [个人空间地址]` 

   `git config --global user.email [账号绑定的邮箱]`

3. 配置密钥对：生成公钥和私钥，用于上传代码时的安全验证

   `ssh-keygen`  一路回车，就可以生成密钥对，默认密钥对是存放在`(/c/Users/[主机用户名]/.ssh/)` 。这个目录下有两个文件， .pub就是公钥，另外一个是私钥，这两个文件千万不要动！！！

   到线上（gitee）打开设置->安全设置->ssh公钥，把本地的公钥文件复制进来，输入登录密码，就配置成功了

4. **第一次创建项目的时候执行4-6步，否则从第7步开始。**在本地创建一个项目文件夹，项目代码都在这个文件夹里，执行 `git init`  初始化一个本地git仓库，这个时候项目里回多出一个.git目录（这个目录默认时隐藏的），这个目录不要动！！！

5. 创建一个线上仓库：登录gitee，新建仓库，输入项目名称，选择公开源代码，下面的选框一个都不要勾，创建就ok了

6. 将本地仓库和线上仓库建立关联：`git remote add origin [线上仓库的SSH地址]`

   如果在执行这句话的时候报错：`fatal: remote origin already exists.`

   那么就先执行 `git remote rm origin`

   再重新执行`git remote add origin [线上仓库的SSH地址]`

7. 代码添加到暂存区  `git add -A`  (也可以 git add [文件名] 来单独添加某一个文件)

8. 代码提交到本地版本库  `git commit -m '[说明本次提交所作的操作，越详细越好]' `

9. 代码推送到远程 `git push origin master`

   

### git常见命令

* `git init`  初始化仓库
* `git config`  配置用户信息
* `git remote`  新增或者删除远程仓库的关联
* `git add`  添加到暂存区
* `git commit`  代码提交（每一次commit都会有一个新的版本号）
* `git push` 推送到远程仓库
* `git status`  查看当前仓库的状态
* `git log`  查看日志（每一个commit在这里都能查看到，而且commit后面的随机字符串就是版本号），按字母q 退出log
* `git reset --hard [要回退的版本号]`   回退到之前的某一个版本
* `git clone [线上仓库的https地址]`  把线上仓库代码克隆到本地



### 使用git时候的一些注意事项

* 一个本地仓库对应一个远程仓库
* 远程代码和本地代码要保持统一
* .git 文件不能嵌套（仓库不能嵌套）
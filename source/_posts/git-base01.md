---
title: git的基本使用（一）
date: 2019-11-21 17:13

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
3. 团队协作，强大的分支功能，可以快速实现团队协作👨‍👩‍👧‍👧👨‍👨‍👦👨‍👨‍👧👨‍👨‍👧‍👦👨‍👨‍👦‍👦👨‍👨‍👧‍👧👩‍👩‍👦👩‍👩‍👧👩‍👩‍👧‍👦👩‍👩‍👦‍👦👩‍👩‍👧‍👧👨‍👦

## Git代码托管平台

* github：  [https://github.com](https://github.com/ 'https://github.com/')

  github是全球最大的开源社交编程及代码托管网站，也被称为全球最大同性交友网（gayhub）👨‍❤️‍💋‍👨👨‍❤️‍💋‍👨👨‍❤️‍💋‍👨👨‍❤️‍💋‍👨👨‍❤️‍💋‍👨👨‍❤️‍👨👨‍❤️‍👨👨‍❤️‍👨👨‍❤️‍👨👨‍❤️‍👨

* coding：  [https://dev.tencent.com](https://dev.tencent.com/ 'https://dev.tencent.com/')

  与腾讯云达成战略合作，基于云计算技术的软件开发平台，集项目管理、代码托管、运行空间、质量控制为一体。 

* gitee：   [https://gitee.com](https://gitee.com/ 'https://gitee.com/')

   码云(gitee.com)是 OSCHINA.NET 推出的代码托管平台,支持 Git 和 SVN,提供免费的私有仓库托管。

   

## Git工作流程

![git流程示意图](/img/article/git示意图.jpg 'git流程示意图')

### 概念介绍
* workspace：本地工作空间
* Index：暂存区
* repository：本地版本库
* remote：远程版本库

### 工作流程

要实现代码托管，需要在本地init一个本地仓库，通过 *add* 命令添加到暂存区，然后 *commit* 提交到本地仓库（每一次提交都会产生一个新的版本），最后通过*push* 推送到远程🙆‍♀️🙆‍♀️🙆‍♀️。



## Git使用步骤

​	*本次以gitee为例做讲解，读者也可以选择github或者coding，使用步骤一致，步骤中使用 [] 中括号括起来的部分需要替换成你自己相应的内容，不需要保留中括号。*

1. ​	**第一次安装git的时候要执行1-4步，否则从第5步开始**

   安装git工具，安装完成之后鼠标右键只要出现 `git bash here` 菜单即说明安装成功。

   [windows系统下载链接]( https://git-scm.com/download/win 'https://git-scm.com/download/win' )

   [MAC下载链接]( https://git-scm.com/download/mac 'https://git-scm.com/download/mac' )

2. 注册gitee的账号（或其他平台账号），修改个人空间地址，绑定邮箱。

3. 全局配置用户名和邮箱

   `git config --global user.name【你的码云账号]`

   `git config --global user.email [你的码云验证邮箱]`

4. 配置密钥对：生成公钥和私钥，用于上传代码时的安全验证

   在`git bash`里执行命令`ssh-keygen`  一路回车，就可以生成密钥对，默认密钥对是存放在`(/c/Users/[主机用户名]/.ssh/)` 。这个目录下有两个文件， .pub就是公钥，另外一个是私钥，这两个文件千万不要动！！！

   到线上（gitee或其他平台）打开设置->安全设置->ssh公钥，把本地的公钥文件全选复制进来，输入登录密码，就配置成功了。

5. **第一次创建项目的时候执行5-7步，否则从第8步开始**

   创建本地仓库

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

## 版本管理

* 把已经放在暂存区的内容在拉回到工作区

  ```bash
  # 拉回暂存区的 index.txt 文件
  git reset HEAD -- index.txt
  
  # 拉回暂存区的 ceshi 文件夹
  git reset HEAD -- ceshi/
  
  # 拉回暂存区的 所有文件
  git reset HEAD -- .
  ```

* 历史版本回退

  ```bash
  # 查看历史版本
  git log
  ```

  ![git版本信息](/img/article/git版本信息.png 'git版本信息')

  这里commit后面的字符串即为版本号，我们可以使用 `git reset --hard 版本编号` 进行历史回退

  ```bash
  # 回退到第一次提交的版本
  git reset --hard ce0c17f7a703c6847552c7aaab6becea6f0197f2
  
  # 回退到第二次提交的版本
  git reset --hard abb2c4f12566440e04bc166c3285f855a37a3bb2
  ```

## 分支管理

git分支，就是我们自己把我们的整个文件夹分成一个一个独立的区域，比如我在开发 **登录** 功能的时候，可以放在 `login` 分支下进行开发；开发 **列表** 功能的时候，可以放在 `list` 分支下进行开发，大家互不干扰，每一个功能都是一个独立的功能分支，这样开发就会好很多。

git在初始化的时候，会自动生成一个分支，叫做 `master`，是表示主要分支的意思，我们就可以自己基于`master`开辟出很多独立分支

- 开辟一个分支使用 `git branch 分支名称` 指令

  ```bash
  # 开辟一个 login 分支
  $ git branch login
  ```

- 查看一下当前分支情况

  ```bash
  # 查看当前分支情况
  $ git branch
  ```

![查看分支情况](/img/article/查看分支情况.png '查看分支情况')

已经可以看到，当前有两个分支了，一个是 `master`，一个是 `login`。前面有个 `*` 号，并且有高亮显示的，表示你当前所处的分支。

我们对 **登录** 功能的开发要移动到 `login` 分支去完成，此时可以切换到其他分支，使用 `git checkout 分支名称`

```bash
# 切换到 login 分支
$ git checkout login
```

然后我们在整个`login`分支上进行 **登录** 功能的开发。开发完毕以后，我们就在当前分支上进行提交，提交以后我们再把分支切换回`master`发现 `master` 上面还是最初始的状态，而 `login` 分支上有我们新写的 **登录** 功能的代码。我们按照分支把所有功能都开发完毕了以后，需要把所有代码都合并到 `master` 主分支上。

`git` 的合并分支，只能是把别的分支的内容合并到自己的分支上

- 使用的指令是 `git merge`

  ```bash
  # 切换到 master 分支
  $ git checkout master
  
  # 把 login 的内容合并到自己的分支
  $ git merge login
  ```

这个时候，我们刚才在 `login` 上开发的东西就都来到了 `master` 主分支上，**如果是有多个分支的话，那么所有的最后都合并到 `master` 分支上的时候，我们的主分支上就有完整网站的所有页面，各个分支上都是单独的页面和功能**。这个时候我们开辟的分支就没有什么用了，就可以删除分支了

1. 先切换到别的分支

2. 使用指令 `git branch -d 分支名称` 来删除

   ```bash
   # 先切换到别的分支
   $ git checkout master
   
   # 删除 login 分支
   $ git branch -d login
   ```

### 常用的分支命名

在使用分支的时候我们的分支命名也要尽量规范一些，我们有一些分支名称大家都默认是有特殊意义的，比如我们之前的写的`login`分支就是不规范的分支名称。

常见的分支名称

1. master：主分支，永远只存储一个可以稳定运行的版本，不能再这个分支上直接开发
2. develop（dev）： 主要开发分支，主要用于所用功能开发的代码合并，记录一个个的完整版本
   - 包含测试版本和稳定版本
   - 不要再这个分支上进行开发
3. feature-xxx（feat-xxx）：功能开发分支，从`dev`创建的分支主要用作某一个功能的开发，以自己功能来命名就行，例如 `feat-login` / `feat-list`，开发完毕后合并到 `dev` 分支上
4. feat-xxx-fix: 某一分支出现`bug`以后，在当前分支下开启一个`fix`分支，比如登录功能有bug，可以基于`feat-login`开辟一个新的分支`feat-login-fix`，解决完 `bug` 以后，合并到当前功能分支上，如果是功能分支已经合并之后发现 `bug` 可以直接在 `develop` 上开启分支，修复完成之后合并到 `dev` 分支上
5. hotfix-xxx： 用于紧急`bug`修复，直接在 `master` 分支上开启，修复完成之后合并回 `master`

## 冲突解决

git冲突是指在我们的上传过程中，本地的版本和远程的版本不一致导致的，这个时候只要先使用 `git pull` 拉取回来，让本地和远程保持一致，然后再从新上传就好了，但是 `git pull` 相对不安全，因为会自动和本地内容合并，我们也可以选择使用 `git fetch`

```bash
# 使用 fetch 获取远程最新信息并开辟一个临时分支
$ git fetch origin master:tmp

# 将当前分支和临时分支的内容进行对比
$ git diff tmp

# 再选择合并分支内容
$ git merge tmp
```

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
* `git pull`nbsp;&nbsp;&nbsp;&nbsp;在已有的仓库基础上拉取最新的线上代码，拉取之后直接合并
* `git fetch`&nbsp;&nbsp;&nbsp;&nbsp;在已有的仓库基础上拉取最新的线上代码，拉取之后由用户决定是否合并
* `git branch`&nbsp;&nbsp;&nbsp;&nbsp;查看分支
* `git branch newBranch`&nbsp;&nbsp;&nbsp;&nbsp;基于当前分支创建`newBranch`分支
* `git branch -d myBranch`&nbsp;&nbsp;&nbsp;&nbsp;删除`myBranch`分支
* `git diff tmp`&nbsp;&nbsp;&nbsp;&nbsp;查看当前分支和`tmp`分支的区别
* `git merge tmp`&nbsp;&nbsp;&nbsp;&nbsp;将`tmp`分支合并到当前分支

## 附录2：使用Git时候的一些注意事项
* 一个本地仓库对应一个远程仓库
* 远程代码和本地代码要保持统一
* .git 文件不能嵌套（仓库不能嵌套）

## 附录3：使用Git提交时的备注信息

**用于说明 commit 的类别，只允许使用下面7个标识**

1. feat：新功能（feature）
2. fix：修补bug
3. docs：文档（documentation）
4. style： 格式（不影响代码运行的变动）
5. refactor：重构（即不是新增功能，也不是修改bug的代码变动）
6. test：增加测试
7. chore：构建过程或辅助工具的变动 


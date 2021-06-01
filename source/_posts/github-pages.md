---
title: vue项目如何使用git pages部署
date: 2021-06-01 20:11

categories:
- 工具
tags:
- vue
- git

---

**自己做了一个vue项目，想发给别人看？使用git pages！**

<br>

@[toc]

<br>

## 前言

首先，git pages可能大家都不陌生，它是用来托管静态网站的，也就是说如果你有一个静态网站，可以将代码上传至git仓库并开通pages服务，就会得到一个链接，打开这个链接即可访问你自己的网站，就不需要再去花钱租用云服务器了。

我们常用的代码托管平台，[github](https://github.com/)、[gitee](https://gitee.com/)、[coding](http://coding.net/) 都具备pages能力，可以开通其服务。

不过😠😠😠。。。

![](/img/article/gitee-pages.png)

![](/img/article/coding-pages.png)



* gitee早前已开通过pages服务的可以继续使用，但新仓库不支持开通
* coding去年经过大改版，要开通服务流程非常麻烦，暂时不论

因此，只能选择github，虽然不稳定，虽然访问慢，但无奈人穷，总比没有好啊😅😅😅

接下来说说vue项目如何开通github pages服务



## 开通github pages

### 一、开通仓库

开通git仓库，上传代码，这一步无需我再多言，其实我感觉是多余的，因为大家都会🙃🙃🙃

### 二、打开pages服务

1. 找到仓库中的settings，点开pages

   ![](/img/article/github-pages01.png)



2. 选择分支，计划将哪个分支的代码进行托管就选择哪个分支，这里我的仓库只有master一个分支

   ![](/img/article/github-pages02.png)



3. 选择目录，只有两个选项，一个root代表仓库根目录，另外有一个docs，这里就要说一下了，因为我们是使用vue-cli写的项目，所以根目录是不能拿来部署的，那就只能选择docs了，这里先点击save，你将得到一个链接，链接格式为：`https://用户名.github.io/项目名`，当然，暂时还无法访问的，因为我们并没有docs目录，怎么办？造一个！我们继续往下看。

   ![](/img/article/github-pages03.png)

   ![](/img/article/github-pages04.png)



4. 我们要托管的是我们项目打包之后的代码，webpack打包默认是在dist目录，但是这个咱们也是可以设置的，在vue.config.js里（没有这个文件的话就在项目根目录里新建一个）写上如下代码，这样打包之后的代码就会在docs目录了。并且git提交的时候不要忽略docs，要提交上去才可以，以后每次提交之前都先`npm run build` 打包好再上传。

   ```js
   module.exports = {
     outputDir: 'docs'
   }
   ```

   

5. 但是，别急，这个时候上传代码，打开链接，会发现页面空白一片，虽然看不到东西，但至少pages已经生效了，因为你看到的是空白而不是404🤣🤣🤣，为什么会这样呢？打开network咱们看看

   ![](/img/article/github-pages05.png)

   可以看到我们访问地址有一层path，这个path是项目名来的，但是请求js或者css的时候路径明显不对，因为我们打包后的代码默认是会请求根目录的静态文件的，这个可以打开打包后的index.html求证，下面是我从index.html中截取的代码片段，很明显看到路径，在本地运行时我们通过`http://localhost:8080`打开是没问题的，但是一旦访问路径多了一层那就不对了，因此我们希望js和css这样的静态文件路径也可以加上一层项目名，这样就可以了。

   ```html
   <script src="/js/chunk-vendors.c23eae66.js"></script>
   <link href="/css/chunk-873475d4.f11a044a.css" rel="prefetch">
   ```

   

6. 要想让打包后的文件路径多一层，那就需要把静态文件放进同名文件夹里，在vue.config.js里可以进行设置assetsDir和publicPath

   ```js
   module.exports = {
     publicPath: '/spsi-fe/',
     outputDir: 'docs',
     assetsDir: 'spsi-fe'
   }
   ```

   我们再次`npm run build`，打开docs/index.html看看

   ```html
   <script src="/spsi-fe/js/chunk-vendors.c23eae66.js"></script>
   <link href="/spsi-fe/css/chunk-873475d4.f11a044a.css" rel="prefetch">
   ```

   

7. 这个时候再重新打包上传，首页终于可以运行了，但是，别高兴太早，当你要跳转其他路由的时候又会出问题，比如：本来首页url为：`https://dary1112.github.io/spsi-fe`，关于页面的路由是`/about`，那么我一跳转就会变成`https://dary1112.github.io/about`，这样肯定就不对了，那怎么办呢？不要忘了路由里有一个`base`参数可以设置

   ```js
   // router.js
   const router = new VueRouter({
     mode: 'history',
     base: '/spsi-fe',
     routes,
   })
   ```

   就可以啦，这样不管路由如何跳转，前面始终都会带上`/spsi-fe`🤓🤓🤓



> 但是，以我的经验，github pages及其不稳定，经常不能访问，用着玩还可以，真要搭建网站博客之类还是花钱使用服务器吧😂😂😂



## 附录

关于本文涉及到的一些配置，官网传送门在这里：

* [publicPath](https://cli.vuejs.org/zh/config/#publicpath)

* [outputDir](https://cli.vuejs.org/zh/config/#outputdir)

* [assetsDir](https://cli.vuejs.org/zh/config/#assetsdir)

* [base](https://router.vuejs.org/zh/api/#base)

  
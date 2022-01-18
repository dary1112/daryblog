---
title: vue性能优化之使用gzip压缩打包，减小chunk-venders.js体积
date: 2022-01-18 18:11

categories:
- 大前端
tags:
- vue
- 经验

---

**vue项目打包之后生成的chunk-venders.js文件很大，影响性能，本文介绍如果使用gzip压缩，提升性能。**

<br>

@[toc]

<br>

## 前言

不管是vue项目还是react项目在使用webpack打包之后都会生成一个动辄一两兆甚至更大的js文件，在某些情况下严重影响项目性能，打开页面的时候白屏时间会很长，本文将介绍如何使用gzip压缩打包，主要是nginx部署的配置，非常重要，我查阅了很多文章基本都没用说清楚甚至错误的。

gzip压缩分两种，一种是服务器压缩后传输给浏览器，但是这种方案是请求时服务器实时压缩，比较消耗服务器性能；另外一种就是前端在webpack打包的时候压缩好，服务器做一些相应配置就可以返回压缩包给浏览器，只是打包出来的dist体积会偏大，但是不消耗服务器性能。

这两种综合起来使用是比较划算的，接下来说说前端打包步骤。



## 一、webpack配置

这一步是很简单的，安装一个包，然后加上配置即可

```bash
npm install --save-dev compression-webpack-plugin@1.1.2
```

> 我这里下载的是`1.1.2`版本的，试过更高的版本会有ES6语法的报错，因为我node使用的是v12，如果node版本更高可以尝试更高版本。

然后在`vue.config.js`里加上配置如下：

```js
module.exports = {
  chainWebpack: config => {
    const CompressionWebpackPlugin = require('compression-webpack-plugin')
    if (process.env.NODE_ENV === 'production') {
        config.plugin('CompressionPlugin').use(
        	new CompressionWebpackPlugin({
            test: /\.(js|css)$/,
            threshold: 10240, // 超过10kb的文件就压缩
      			deleteOriginalAssets:true, // 不删除源文件
      			minRatio: 0.8
          })
        )
   	}
  }
}
```

然后当我们运行`npm run build`之后你就会发现dist里除了以前的文件以外会有很多同名的gz文件，而且体积小了很多，这就是压缩后的了。

![dist里的gz文件示意图](/img/article/vue-gzip01.png)



## 二、nginx配置

这个配置花了我很多时间，网上写的基本都不全甚至是错误的，按照我下面的步骤保证可以实现效果。

### 1. 检查nginx模块

首先要检查一下nginx的模块，找到nginx的q启动文件，我的是`/usr/sbin/nginx`，如果你找不到可以使用`ps -ef | grep nginx`命令找到master进程所在的目录，进入sbin目录然后执行`./nginx -V`，注意是大写的V，查看结果如下：

![查看nginx版本和模块](/img/article/vue-gzip02.png)



第一行是nginx的版本，我的是1.16.1，重点是最后一行，我的nginx安装了很多模块，其中我们需要的就是红框部分`--with-http_gzip_static_module`，有的话那就不需要下面的步骤了，可以直接跳到第2步，如果没有那就继续往下看。

#### 1.1 加入模块重新编译

如果我们在上面步骤里发现nginx没有`gzip_static`模块的话，那就需要我们重新编译安装一下nginx。

首先需要找到nginx的源码路径，如果不知道可以执行`find / -name nginx`查找，我的在`/usr/local/nginx-1.16.1`，然后cd到这个目录，可以先使用`ll`命令看一下有没有`configure`文件，如果有说明源码目录找对了，如果没有则再查找一下，实在找不到那就说明源码已经被删了，那就只能卸载当前nginx整个重装了。

如果第一步看到的nginx已有一些模块，则需要把这些已有的模块复制下来，然后再后面加上`--with-http_gzip_static_module`，执行如下命令：

```bash
./configure --prefix=/usr/share/nginx --modules-path=...[整个复制]... --with-http_gzip_static_module
```

如果第一步看到的一个模块都没有的话，那就直接重新编译，注意 `--prefix=`后面写ng所在路径：

```bash
./configure --prefix=/usr/share/nginx --with-http_gzip_static_module
```

#### 1.2 安装

执行命令

```bash
make
```

进行安装

#### 1.3 备份

为了确保安全，将旧的nginx做一个备份（目录如果不一样记得更换）

```bash
cp /usr/local/nginx/sbin/nginx /usr/local/nginx/sbin/nginx.bak
```

### 1.4 覆盖原来的nginx

先把nginx服务停止掉

```bash
ps -ef | grep nginx
```

找到master进程并且将其kill掉。

复制安装好的新的nginx文件覆盖旧的：

```bash
cp ./objs/nginx /usr/local/nginx/sbin/
```

#### 1.5 验证

查看模块

```bash
/usr/local/nginx/sbin/nginx -V　
```

如果出现 `gzip_module`说明安装成功。

### 2. 添加gzip配置

一般是在http里面加，也可以在某个server里加

```bash
http {
	gzip on;
	gzip_static on;
	gzip_min_length  5k;
	gzip_buffers     4 16k;
	gzip_http_version 1.0;
	gzip_comp_level 7;
	gzip_types       text/plain application/javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
	gzip_vary on;
}
```

其中：`gzip_static on;` 是为了命中dist里的gz文件，其他的配置是服务器实时压缩配置，一般两种都写上，有静态gz文件的会优先返回gz文件，没有的话就会开启实时压缩，实时压缩是比较耗服务器资源的。



## 三、附录

配置项释义：

```bash
		# 开启服务器实时gzip
    gzip on;
    
    # 开启静态gz文件返回
    gzip_static on;
    
    # 启用gzip压缩的最小文件，小于设置值的文件将不会压缩
    gzip_min_length 1k;
    
    # 设置压缩所需要的缓冲区大小     
    gzip_buffers 32 4k;

		# 设置gzip压缩针对的HTTP协议版本
    gzip_http_version 1.0;

    # gzip 压缩级别，1-9，数字越大压缩的越好，也越占用CPU时间
    gzip_comp_level 7;

    # 进行压缩的文件类型。javascript有多种形式。其中的值可以在 mime.types 文件中找到。
    gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png application/vnd.ms-fontobject font/ttf font/opentype font/x-woff image/svg+xml;

    # 是否在http header中添加Vary: Accept-Encoding，建议开启
    gzip_vary on;

    # 禁用IE 6 gzip
    gzip_disable "MSIE [1-6]\.";
```




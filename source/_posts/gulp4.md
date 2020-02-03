---
title: gulp4.0的使用和gulpfile.js任务配置
date: 2019-12-27 17:20

categories:
- 大前端
tags:
- JavaScript
---

**本文主要介绍gulp4.0的使用和常规代码的打包配置**

<br>

## 自动化构建工具

写代码的时候会遵循一些框架的方式或者某些技术，或我们自己处理的模块化，但是这种代码是不能直接在浏览器运行的，这个过程我们就需要使用自动化构建工具，这些工具都是基于node环境。

node官网：[https://nodejs.org](https://nodejs.org/en/ 'https://nodejs.org')，下载LTS版本的即可。

## 常用的工具

* grunt  比较古老，功能少，更新少，插件少，现在用的很少🧐🧐
* gulp   pc端 jquery的项目打包更多使用gulp
* webpack    最主流的打包工具，vue、react都是用的webpack👊👊

## gulp的介绍

* gulp官网：[https://www.gulpjs.com.cn/](https://www.gulpjs.com.cn/ 'https://www.gulpjs.com.cn/')

* 用自动化构建工具增强你的工作流程！

* gulp 将开发流程中让人痛苦或耗时的任务自动化，从而减少你所浪费的时间、创造更大价值。

* 简单：代码优于配置、node 最佳实践、精简的 API 集，gulp 让工作前所未有的简单。

* 高效：基于 node 强大的流(stream)能力，gulp 在构建过程中并不把文件立即写入磁盘，从而提高了构建速度。

* 生态：遵循严格的准则，确保我们的插件结构简单、运行结果可控。

* Builds can be the most awful sinkhole for teams to waste their time with - gulp is a serious win for any project

  （对于一个团队来讲，构建是一个浪费时间的最糟糕的一个坑，但是如果使用gulp，对于任何项目来讲这都将会是一场巨大的胜利）✌✌✌✌

## gulp使用

1. 安装node环境：[https://nodejs.org](https://nodejs.org/en/ 'https://nodejs.org')，下载LTS版本的即可

2. 在任意位置打开命令行工具，使用npm全局安装gulp命令行工具 `npm i gulp -g`，安装完成以后执行`gulp -v` 只要看到版本号就说明安装成功

3. 创建项目目录，一般由小写字母加数字构成

4. 进入目录，执行`npm init -y` 初始化项目，会创建一个package.json文件，这个文件里就是当前项目的一些说明信息，也可以手动修改

5. 规划目录结构，比如：src放源代码，dist就是根据src源代码使用gulp打包之后可以用来上线的代码，dist是不用动的，我们写代码在src里面写，但是最后运行的是dist目录

   ![gulp项目目录结构](/img/article/gulp项目目录结构.png)

6. 在当前项目局部安装gulp， `npm i gulp -dev`，高版本npm会自动帮我们 --save 保存在package.json文件的依赖里，-dev的意思是安装成开发依赖，也就是说这个包只有开发环境需要，线上产品环境不需要。这样的话即使删除node_modules也可以直接运行 `npm i` 就可以根据package.json里面的所有依赖包信息把这些依赖包全局安装进来

7. 管理路径，把所有的路径集中用paths对象来管理，包括源文件路径和目标路径

   \*\* 代表所有文件夹

   \* 代表所有文件

   ```javascript
   const path = {
       html: {
           src: 'src/**/*.html', // src下面的所有文件夹下的所有html文件，下面的js，css以此类推
           dest: 'dist' // 处理的结果放到dist目录
       },
       css: {
           src: 'src/css/**/*.css', // src下面css目录里的所有文件夹下的所有css文件
           dest: 'dist/css' // 处理的结果放到dist下的css目录里
       },
       js: {
           src: 'src/js/**/*.js',
           dest: 'dist/js'
       },
       img: {
           src: 'src/images/**/*',
           dest: 'dist/images'
       },
       libs: {
           src: 'src/libs/**/*',
           dest: 'dist/libs'
       }
   }
   ```

8. 制定压缩html的任务：执行`npm i gulp-htmlmin -dev`安装压缩html的插件，然后制定压缩任务，再通过module.exports 把这个任务暴露出去，就可以执行`gulp html` 来运行这个任务了

   ```javascript
   const gulp = require('gulp')
   const htmlmin = require('gulp-htmlmin')
   
   const html = () => {
       // 需要把任务代码return
       return gulp.src(path.html.src)
           .pipe(htmlmin({
           	removeComments: true,//清除HTML注释
           	collapseWhitespace: true,//压缩HTML
           	collapseBooleanAttributes: true,//省略布尔属性的值 <input checked="true"/> ==> <input checked />
         		removeEmptyAttributes: true,//删除所有空格作属性值 <input id="" /> ==> <input />
         		removeScriptTypeAttributes: false,//删除<script>的type="text/javascript"
         		removeStyleLinkTypeAttributes: true,//删除<style>和<link>的type="text/css"
         		minifyJS: true,//压缩页面JS
         		minifyCSS: true//压缩页面CSS 
       	}))
       	.pipe(gulp.dest(path.html.dest))
   }
   module.exports = {
     html
   }
   ```

9. 制定js任务：先ES6转ES5，然后再压缩，最后在导出那里加上js任务

   安装压缩js的包`npm i gulp-uglify -dev` 

   安装ES6转ES5的包`npm i gulp-babel @babel/core @babel/preset-env -dev`

   ```javascript
   const uglify = require('gulp-uglify')
   const babel = require('gulp-babel')
   
   const js = () => {
       return gulp.src(path.js.src)
           .pipe(babel({
           	presets: ['@babel/env']
       	}))
           .pipe(uglify())
           .pipe(gulp.dest(path.js.dest))
           .pipe(connect.reload())
   }
   module.exports = {
       html,
       js
   }
   ```

10. css任务压缩css：`npm i  gulp-clean-css -dev`

   ```javascript
   const cleanCss = require('gulp-clean-css')
   
   const css = () => {
     return gulp.src(path.css.src)
         .pipe(cleanCss())
         .pipe(gulp.dest(path.css.dest))
   }
   module.exports = {
       html,
       js,
       css
   }
   ```

11. 给需要兼容的css样式自动加上兼容性前缀：`npm i gulp-autoprefixer -dev`

    ```javascript
    const cleanCss = require('gulp-clean-css')
    const autoPrefixer = require('gulp-autoprefixer')
    
    const css = () => {
      return gulp.src(path.css.src)
           .pipe(autoPrefixer({
            	browsers: ['last 2 versions']
           }))
          .pipe(cleanCss())
          .pipe(gulp.dest(path.css.dest))
    }
    module.exports = {
        html,
        js,
        css
    }
    ```

    

12. 如果用到sass来写样式，那么还要把css任务做进一步修改：

    sass编译成成css：`npm i node-sass gulp-sass -dev` 

    首先，**path对象里css的src属性路径要把后缀名css改成scss**，再修改css任务

    ![](/img/article/gulp-path.png)

    ```javascript
    const cleanCss = require('gulp-clean-css')
    const autoPrefixer = require('gulp-autoprefixer')
    const sass = require('gulp-sass')
    
    const css = () => {
      return gulp.src(path.css.src)
          .pipe(sass())
          .pipe(autoPrefixer({
          	browsers: ['last 2 versions']
      	  }))
          .pipe(cleanCss())
          .pipe(gulp.dest(path.css.dest))
    }
    ```

13. 开启服务器： `npm i gulp-connect -dev`项目根目录是dist，所以项目中的一切路径都写成/开头的绝对路径，/指的就是dist，避免模块化之后相对位置发生变变化导致路径错误

    ```javascript
    const connect = require('gulp-connect')
    
    const server = () => {
        connect.server({
            root: 'dist', // 项目根路径是dist
            livereload: true, // 服务器支持热更新
            port: 2333 // 配置端口号2333
        })
    }
    ```

14. 由于dist目录是每次执行任务时自动生成的，所以为了避免上一次旧的代码对新的代码造成影响，我们一般会在开启任务之前先把dist目录删掉： `npm i del -dev`

    ```javascript
    const del = require('del')
    
    const clean = () => del(['dist'])
    ```

15. 还有一些图片或者引入的第三方的文件需要做一个移动处理

    ```javascript
    // img任务：复制到dist里
    const img = () => gulp.src(path.img.src).pipe(gulp.dest(path.img.dest))
    
    // libs任务：文件的复制
    const libs = () => gulp.src(path.libs.src).pipe(gulp.dest(path.libs.dest))
    ```

16. 监听html、js和css文件的变化，重启对应任务，在被监听的任务后面都要重启服务器 

    ```javascript
    // watch任务：监听一些文件的修改，一旦被修改了就自动重启对应的任务
    const watch = () => {
        gulp.watch(path.html.src, html)
        gulp.watch(path.css.src, css)
        gulp.watch(path.js.src, js)
    }
    
    // 再在html、css和js任务后面都加上一句.pipe(connect.reload())，比如css任务改成这样：
    const css = () => {
      return gulp.src(path.css.src)
          .pipe(sass())
          .pipe(cleanCss())
          .pipe(gulp.dest(path.css.dest))
          .pipe(connect.reload())
    }
    ```

17. 任务导出做一下修改，单个导出只能单个执行，所以我们可以把所有要执行的任务放在默认人任务里，就只需要在命令行里执行gulp即可执行所有任务：导出默认任务流，先同步执行clean，再异步执行其他任务

    ```javascript
    module.exports.default = gulp.series(delDist, gulp.parallel(html, css, js, img, libs, server, watch))
    
    ```

18. 如果需要跨域访问其他接口，则需要配置跨域，需要依赖另一个中间件：`npm i http-proxy-middleware -dev`

    ```javascript
    const connect = require('gulp-connect')
    const proxy = require('http-proxy-middleware')
    
    const server = () => {
      connect.server({
        root: 'dist',
        livereload: true,
        port: 8000,
        // 中间件：函数返回一个数组，数组配置跨域代理
        middleware: function () {
          return [
            // 将以/api为开头的请求代理到域 http://localhost:80
            proxy('/api', {
              target: 'http://localhost:80',
              changeOrigin: true
            })
          ]
        }
      })
    }
    ```

<br>

现在，我们只需要在命令行里运行`gulp` 项目就能启动起来了，然后在浏览器里输入 `http://localhost:2333`就能访问主页啦，最后，贴上完整代码：🙆‍♂️🙆‍♂️🙆‍♂️

```javascript
const gulp = require('gulp'),
      htmlmin = require('gulp-htmlmin'),
      cleanCss = require('gulp-clean-css'),
      del = require('del'),
      uglify = require('gulp-uglify'),
      babel = require('gulp-babel'),
      connect = require('gulp-connect'),
      sass = require('gulp-sass')

// 在每次开启新任务之前先把dist删掉
const delDist = () => del(['dist'])

// 先把所有的任务用到的文件源路径和目标路径做一个统一规划
const path = {
  html: {
    src: 'src/**/*.html',
    dest: 'dist'
  },
  css: {
    src: 'src/css/**/*.scss',
    dest: 'dist/css'
  },
  js: {
    src: 'src/js/**/*.js',
    dest: 'dist/js'
  },
  img: {
    src: 'src/images/**/*',
    dest: 'dist/images'
  },
  libs: {
    src: 'src/libs/**/*',
    dest: 'dist/libs'
  }
}

// 制定html任务：通过函数的方式
const html = () => {
  // 需要把任务代码return
  return gulp.src(path.html.src)
    .pipe(htmlmin({
      removeComments: true,//清除HTML注释
      collapseWhitespace: true,//压缩HTML
      collapseBooleanAttributes: true,//省略布尔属性的值 <input checked="true"/> ==> <input checked />
      removeEmptyAttributes: true,//删除所有空格作属性值 <input id="" /> ==> <input />
      removeScriptTypeAttributes: false,//删除<script>的type="text/javascript"
      removeStyleLinkTypeAttributes: true,//删除<style>和<link>的type="text/css"
      minifyJS: true,//压缩页面JS
      minifyCSS: true//压缩页面CSS 
    }))
    .pipe(gulp.dest(path.html.dest))
    .pipe(connect.reload())
}

// css任务：先把scss文件编译成css，再压缩
const css = () => {
  return gulp.src(path.css.src)
    .pipe(sass())
    .pipe(cleanCss())
    .pipe(gulp.dest(path.css.dest))
    .pipe(connect.reload())
}

// js任务
const js = () => {
  return gulp.src(path.js.src)
    .pipe(babel({
      presets: ['@babel/env']
    }))
    .pipe(uglify())
    .pipe(gulp.dest(path.js.dest))
    .pipe(connect.reload())
}

// img任务：复制到dist里
const img = () => gulp.src(path.img.src).pipe(gulp.dest(path.img.dest))

// libs任务
const libs = () => gulp.src(path.libs.src).pipe(gulp.dest(path.libs.dest))

// server任务
const server = () => {
  connect.server({
    root: 'dist',
    livereload: true,
    port: 1912
  })
}

// watch任务：监听一些文件的修改，一旦被修改了就自动重启对应的任务
const watch = () => {
  gulp.watch(path.html.src, html)
  gulp.watch(path.css.src, css)
  gulp.watch(path.js.src, js)
}

// gulp.series 是同步执行任务，第一个执行完了才能执行第二个
// gulp.parallel 是异步执行任务，多个任务同时运行
module.exports.default = gulp.series(delDist, gulp.parallel(html, css, js, img, libs, server, watch))

```

<br><br>

这是package.json🙋‍♂️🙋‍♂️🙋‍♂️

```javascript
{
  "name": "for-my-rose-project",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "Dary",
  "license": "ISC",
  "dependencies": {
    "gulp": "^4.0.2"
  },
  "devDependencies": {
    "@babel/core": "^7.7.7",
    "@babel/preset-env": "^7.7.7",
    "del": "^5.1.0",
    "gulp-babel": "^8.0.0",
    "gulp-clean-css": "^4.2.0",
    "gulp-connect": "^5.7.0",
    "gulp-htmlmin": "^5.0.1",
    "gulp-sass": "^4.0.2",
    "gulp-uglify": "^3.0.2",
    "node-sass": "^4.13.0"
  }
}
```


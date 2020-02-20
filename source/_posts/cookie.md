---
title: 前端cookie的介绍和使用封装
date: 2019-11-21 23:51

categories:

- 大前端

tags:

-  JavaScript


---



**本文主要是介绍cookie的特点以及封装前端操作cookie的方法**
@[toc]
<br>

在正式介绍cookie之前我们要先来说一说网络通讯协议😉

首先：什么是网络通讯协议？所谓协议一般就是甲乙双方沟通要遵循的规则与方式，那么通讯协议就是通讯双方要遵循的规则，网络通讯协议则是客户端与服务器双方传输数据所要遵循的协议，一般是http或者https，而它们都是基于TCP/IP协议的。

## TCP/IP协议是面向连接的

在发送数据之前，都必须先在双方之间建立一条连接。在TCP/IP协议中，TCP 协议提供可靠的连接服务，连接是通过三次握手进行初始化的，如下图：

![三次握手](/img/article/三次握手.jpg '三次握手')

客户端要向服务器发送请求，首先会发送一个请求连接，这是第一次握手，当服务器收到这个请求以后返回给客户端一条消息，这是第二次握手，然后客户端再把确认消息发给服务器，这是第三次握手，经过这三次握手客户端与服务器之间的连接就建立起来了。就像打电话一样，客户端向服务器拨通电话，服务器接起电话，说了一句：“喂？”，然后客户端收到了这句“喂”，再跟服务器说一声“喂！”，连接就建立好了，可以愉快的通话了😊

建立连接需要经过三次握手，那数据传输结束以后呢？我们打电话事情说完了要挂掉电话要经历一个怎样的过程？可能对话是下面这样的：

A：“我挂了”

B：“好”

A：“拜拜”

B：“拜拜，我也挂了”

A：“好，拜拜”

B：“嗯嗯，拜拜，挂了”

A：“好，拜拜”

B：“拜拜”

有点搞笑😂，经过好多次消息确认好不容易才能把电话挂掉，但这并不是无聊，而是要互相确认对方是否真的已经准备断开连接了，那么我们的TCP/IP协议也会考虑到这一点，要断开连接会经过四次挥手，如下图：

![四次挥手](/img/article/四次挥手.jpg '四次挥手')

数据传输结束时，客户端向服务器发送一条结束的消息，服务器收到消息后会马上返回给客户端确认消息，并随后发送一条“我也结束了”的消息，当客户端收到这条消息以后会响应给服务器确认消息，服务器收到这条确认消息以后就会关闭，而客户端则会等待两个单位时间之后关闭。注意这里客户端并不是立马关闭，而是会等待两个单位时间，为什么是两个单位时间呢？首先，所谓的单位时间指的是一次前后端数据传输所花费的时间，这里要等待两个单位时间是因为客户端要等数据发送到服务器以后确认收不到数据返回，这一来一去就是两个单位时间了，因为如果这个时候服务器并没有结束的话会再次给客户端响应，而客户端在两个单位时间后收不到响应则代表本次数据传输正式结束。

下面贴出两张动图：

![三次握手](/img/article/三次握手.gif '三次握手')

![四次挥手](/img/article/四次挥手.gif '四次挥手')

这两张动图对于计算机专业的读者可以尝试探究，本文不做详细展开🙃

## http无状态

由于http是基于TCP/IP协议的，也就是说每一个http请求都会通过三次握手建立连接，四次挥手断开连接，那也就得到一个结论：**http协议是一种无状态协议**，这里的**无状态**指的是无法保存状态，因为一旦数据传输结束连接就会断开，就无法获取一些状态了，这样设计的好处就是可以节约资源，需要的时候才发出请求，但是也会造成一些困扰，比如：用户在登录页面完成了登录，那么接下来就会回到首页或者其他页面进行操作，但问题是这个时候登录的这次http请求已经断开了，所以当完成页面跳转之后就取不到登录状态了，那怎么办？再登录一次？😒这酸爽。。。

所以这个时候我们需要有一个东西可以记录这些状态，就比如：A给B打电话要C的电话号码，那么通话结束以后A就不能再得到C的号码了，所以我们平时就会在通话的时候拿个笔和本记录下C的号码，这样以后就都可以直接拨打了，那么我们也需要这样一个东西可以在连接断开以后还可以记录下一些状态，其中比较常用的就是**cookie**。

## cookie

cookie称之为**会话跟踪技术**，顾名思义，就是在一次会话中跟踪记录一些状态。首先，所谓的”会话“指的就是从浏览器打开一个网站到访问它的其他网页直到浏览器关闭的这个过程。cookie就可以在一次会话从开始到结束的整个过程，全程跟踪记录客户端的状态（例如：是否登录、购物车信息、是否已下载、是否 已点赞、视频播放进度等等）。

### cookie的特点

1. **只能存储文本**  (如果浏览器可以随意在客户端机器上生成文件，比如身份牌是个定时炸弹，安全问题将会变得非常严重) 
2. **单条存储有大小限制4KB左右**（文件若没有大小限制，比如身份牌的重量是140斤，挂脖子能不能累死？但是现在很多浏览器能存储的大小不止4KB，可能还会多一点点）
3. **数量限制**（一般浏览器，限制大概在50条左右，你家的门禁卡里能存的下一部蓝光高清么？）
4. **读取有域名限制：**不可跨域读取，只能由来自 写入cookie的 同一域名 的网页可进行读取。简单的讲就是，存在那个域下的cookie，只有当前域有权利读取（身份牌是我的，当然只有我能读取，你媳妇儿的手机自动连接了邻居老王家的wifi，你知道这意味着什么吗？）
5. **时效限制：**每个cookie都有时效，默认的有效期是，会话级别：就是当浏览器关闭，那么cookie立即销毁，但是我们也可以在存储的时候手动设置cookie的过期时间（安全学基本理论：密码锁每次打开都需要重新输入密码，输入一次密码，以后就不再验证，就没有安全可言，问：信用卡为什么会有过期时间？）
6. **路径限制：**存cookie时候可以指定路径，只允许子路径读取外层cookie，外层不能读取内层。*一般cookie都存在项目的根目录，这样就可以避免这种问题。*

### cookie的使用

cookie的使用方式很简单😄，系统提供的只有一个属性 `document.cookie`，无论是存还是取或者其他操作都是通过这一个属性来完成（注：cookie是http协议下的技术，所以不要用file的方式打开本地html文件测试cookie，虽然有部分浏览器也在file协议下实现了cookie，但是不推荐这么做）。

首先，我们来看看如何存：

```javascript
document.cookie = 'username=dary' // 存一条username为dary的cookie
```

![](/img/article/cookieconsole截图1.png)

但是如果当我们要存一条中文的cookie，比如：`username=张三`，在部分低版本浏览器就会遇到一些位置错误，这时就可以使用 `encodeURIComponent` 编码对中文进行编码：

```javascript
document.cookie = `username=encodeURIComponent('熊大林')`
```

![](/img/article/cookieconsole截图2.png)

可以看到，中文内容已经被编码了，以后取得时候我们可以通过`decodeURIComponent` 方法进行解码，下文会提到😛

#### cookie的时效性

现在我们回头去看看上面我们存过的cookie，其中有`Expires/max-Age`选项，这一项指的就是cookie的有效期，我们可以看到是session，代表会话期，也就是默认的会话结束cookie失效，这时我们重启浏览器就看不到这条cookie了。

![](/img/article/cookieconsole截图3.png)

除了默认的会话过期我们还可以手动设置cookie的过期时间，比如：7天后过期

```javascript
var date = new Date()
date.setDate(date.getDate() + 7)
document.cookie = `username=${encodeURIComponent('熊大林')};expires=${date.toUTCString()}`
```

![](/img/article/cookieconsole截图4.png)

我们可以看到过期时间已经是7天以后了，这里我用了`toUTCString()`方法转成了标准时区，所以比北京时间快8个小时，这时我们关闭浏览器再次打开，仍然可以看到这条cookie😋

#### cookie的存储路径

我们来测试一下路径，随便进入项目中某一个目录或者新建一个目录，然后把一下代码放进去执行

```javascript
document.cookie = 'username=dary'
```

![](/img/article/cookieconsole截图5.png)

我们可以看到，path不再是之前的 `/` 了，而是当前目录，这时我们再回到首页，你会发现，这条cookie不见了，因为外层时访问不了内层目录存的cookie的。

所以我们一般存cookie都会这么写：

```javascript
document.cookie = 'username=dary;path=/'
```

不管当前目录是谁统一存根目录，这样项目中任意位置都可以访问这一条cookie，这就perfect🤩啦！

**由此：我们可以封装一个存储cookie的方法如下：**

```javascript
/** 存一条cookie
 * @param {string} key 要存的cookie的名称
 * @param {string} value 要存的cookie的值
 * @param {object} [options] 可选参数，过期时间和目录，如：{ path: '/', expires: 7 }存根目录7天过期的cookie
 */
function setCookie (key, value, options) {
    var str = `${key}=${encodeURIComponent(value)}`
    // 先判断options是否传进来了
    if (options) {
        // 如果传进来了再判断有哪个属性
        if (options.path) {
            // 路径拼接进去
            str += `;path=${options.path}`
        }
        if (options.expires) {
            var date = new Date()
            // 日期设置为过期时间
            date.setDate(date.getDate() + options.expires)
            str += `;expires=${date.toUTCString()}`
        }
    }
    document.cookie = str
}
```

<br>

#### 取cookie

现在我们掌握了如何存cookie，接下来看看怎么取吧

取cookie同样使用`document.cookie`这个属性：

```javascript
console.log(document.cookie) // username=dary
```

但是如果我们在一个网站里存了多条cookie，这个时候得到的结果就是

```javascript
console.log(document.cookie) // username=dary; age=18
```

多条cookie之间以`;&nbsp;` 隔开，注意：这里是分号和一个空格，这个对我们拆开每一条cookie非常重要。所以我们现在希望能把cookie每一条拆开，得到一个对象，这样就可以取得某一条cookie的值了，所以我们可以封装一个获取cookie的方法如下：

```javascript
/** 获取某一条cookie
 * @param {string} key 要获取的cookie的名称
 * @retrun {string} 当前这条cookie的值
 */
getCookie (key) {
    var str = document.cookie
    var arr = str.split('; ')
    var obj = new Object()
    arr.forEach(item => {
        var subArr = item.split('=')
        obj[subArr[0]] = decodeURIComponent(subArr[1])
    })
    return obj[key]
}
```

#### 删除cookie

删除cookie的方式特别简单，我们只需要把cookie的过期时间设置为已经过去了的时间就行了，这个时候浏览器一看，诶？这条cookie不是已经过期了么？我是谁？😖我在哪？😖就只能把它删掉了🥺，方法如下：

```javascript
/** 删一条cookie
 * @param {string} key 要删的cookie的名称
 * @param {path} [path] 可选参数，要删的cookie的所在的路径，如果就是当前路径这个参数可以不传
 */
function removeCookie (key, path) {
    var date = new Date()
    date.setDate(date.getDate() - 1) // 过期时间设置为昨天
    var str = `${key}='';expires=${date.toUTCString()}`
    if (path) {
        str += `;path=${path}`      
    }
    document.cookie = str
}
```

#### 修改cookie

重新存一下把之前的覆盖掉就是修改了cookie了😎

## 附

最后附上一个cookie方法，既可以完成存也可以取，甚至可以删😏

```javascript
// 通过判断有没有传第二个参数value来决定是存还是取
// 这个方法也可以用于删cookie，比如：cookie('username', '', { expires: -1, path: '/' })
function cookie (key, value, options) {
    if (value) {
        var str = `${key}=${encodeURIComponent(value)}`
        if (options) {
            if (options.path) {
                str += `;path=${options.path}`
            }
            if (options.expires) {
                var date = new Date()
                date.setDate(date.getDate() + options.expires)
                str += `;expires=${date.toUTCString()}`
            }
        }
        document.cookie = str
    } else {
        var str = document.cookie
        var arr = str.split('; ')
        var obj = new Object()
        arr.forEach(item => {
            var subArr = item.split('=')
            obj[subArr[0]] = decodeURIComponent(subArr[1])
        })
        return obj[key]
    }
}
```




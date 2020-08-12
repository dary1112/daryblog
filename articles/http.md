---
title: HTTP请求完整过程以及头信息深入解析
date: 2020-08-12 20:31

categories:
- 大前端
tags:
- http
- JavaScript

---

**本文介绍HTTP请求已经各种method之间的区别与联系。**

<br>

@[toc]

<br>

## HTTP 请求/响应的步骤

客户端连接到 Web 服务器 一个 HTTP 客户端，通常是浏览器，与 Web 服务器的 HTTP 端口（默认为80）建立一个 TCP 套接字连接。例如，http://www.xiongdalin.com。

发送 HTTP 请求 通过 TCP 套接字，客户端向 Web 服务器发送一个文本的请求报文，一个请求报文由请求行、请求头部、空行和请求数据 4 部分组成。

服务器接受请求并返回 HTTP 响应 Web服务器解析请求，定位请求资源。服务器将资源复本写到 TCP 套接字，由客户端读取。一个响应由状态行、响应头部、空行和响应数据 4 部分组成。

释放连接 TCP 连接 若 connection 模式为 close，则服务器主动关闭 TCP 连接，客户端被动关闭连接，释放 TCP 连接;若 connection 模式为 keepalive，则该连接会保持一段时间，在该时间内可以继续接收请求;

客户端浏览器解析 HTML 内容 客户端浏览器首先解析状态行，查看表明请求是否成功的状态代码。然后解析每一个响应头，响应头告知以下为若干字节的 HTML 文档和文档的字符集。客户端浏览器读取响应数据 HTML，根据 HTML 的语法对其进行格式化，并在浏览器窗口中显示。



## 请求头和响应头

### request header

1. method，详见下文

2. Host

   请求的web服务器域名地址

3. User-Agent

   HTTP客户端运行的浏览器类型的详细信息。通过该头部信息，web服务器可以判断出http请求的客户端的浏览器的类型。

4. Accept

   指定客户端能够接收的内容类型，内容类型的先后次序表示客户都接收的先后次序

5. Accept-Lanuage

   指定HTTP客户端浏览器用来展示返回信息优先选择的语言

6. Accept-Encoding

   指定客户端浏览器可以支持的web服务器返回内容压缩编码类型。表示允许服务器在将输出内容发送到客户端以前进行压缩，以节约带宽。而这里设置的就是客户端浏览器所能够支持的返回压缩格式。

7. Accept-Charset

   HTTP客户端浏览器可以接受的字符编码集

8. Content-Type

   显示此HTTP请求提交的内容类型。一般只有post提交时才需要设置该属性

   有关Content-Type属性值有如下两种编码类型：

   1. “application/x-www-form-urlencoded”： 表单数据向服务器提交时所采用的编码类型，默认的缺省值就是“application/x-www-form-urlencoded”。 然而，在向服务器发送大量的文本、包含非ASCII字符的文本或二进制数据时这种编码方式效率很低。
   2. “multipart/form-data”： 在文件上载时，所使用的编码类型应当是“multipart/form-data”，它既可以发送文本数据，也支持二进制数据上载。

   当提交为表单数据时，可以使用“application/x-www-form-urlencoded”；当提交的是文件时，就需要使用“multipart/form-data”编码类型。

9. Keep-Alive

   表示是否需要持久连接。如果web服务器端看到这里的值为“Keep-Alive”，或者看到请求使用的是HTTP 1.1（HTTP 1.1默认进行持久连接），它就可以利用持久连接的优点

### response header

| Header             | 解释                                                         | 示例                                                  |
| ------------------ | ------------------------------------------------------------ | ----------------------------------------------------- |
| Accept-Ranges      | 表明服务器是否支持指定范围请求及哪种类型的分段请求           | Accept-Ranges: bytes                                  |
| Age                | 从原始服务器到代理缓存形成的估算时间（以秒计，非负）         | Age: 12                                               |
| Allow              | 对某网络资源的有效的请求行为，不允许则返回405                | Allow: GET, HEAD                                      |
| Cache-Control      | 告诉所有的缓存机制是否可以缓存及哪种类型                     | Cache-Control: no-cache                               |
| Content-Encoding   | web服务器支持的返回内容压缩编码类型。                        | Content-Encoding: gzip                                |
| Content-Language   | 响应体的语言                                                 | Content-Language: en,zh                               |
| Content-Length     | 响应体的长度                                                 | Content-Length: 348                                   |
| Content-Location   | 请求资源可替代的备用的另一地址                               | Content-Location: /index.htm                          |
| Content-MD5        | 返回资源的MD5校验值                                          | Content-MD5: Q2hlY2sgSW50ZWdyaXR5IQ==                 |
| Content-Range      | 在整个返回体中本部分的字节位置                               | Content-Range: bytes 21010-47021/47022                |
| Content-Type       | 返回内容的MIME类型                                           | Content-Type: text/html; charset=utf-8                |
| Date               | 原始服务器消息发出的时间                                     | Date: Tue, 15 Nov 2010 08:12:31 GMT                   |
| ETag               | 请求变量的实体标签的当前值                                   | ETag: “737060cd8c284d8af7ad3082f209582d”              |
| Expires            | 响应过期的日期和时间                                         | Expires: Thu, 01 Dec 2010 16:00:00 GMT                |
| Last-Modified      | 请求资源的最后修改时间                                       | Last-Modified: Tue, 15 Nov 2010 12:45:26 GMT          |
| Location           | 用来重定向接收方到非请求URL的位置来完成请求或标识新的资源    | Location: http://www.zcmhi.com/archives/94.html       |
| Pragma             | 包括实现特定的指令，它可应用到响应链上的任何接收方           | Pragma: no-cache                                      |
| Proxy-Authenticate | 它指出认证方案和可应用到代理的该URL上的参数                  | Proxy-Authenticate: Basic                             |
| refresh            | 应用于重定向或一个新的资源被创造，在5秒之后重定向（由网景提出，被大部分浏览器支持） | Refresh: 5; url=http://www.zcmhi.com/archives/94.html |
| Retry-After        | 如果实体暂时不可取，通知客户端在指定时间之后再次尝试         | Retry-After: 120                                      |
| Server             | web服务器软件名称                                            | Server: Apache/1.3.27 (Unix) (Red-Hat/Linux)          |
| Set-Cookie         | 设置Http Cookie                                              | Set-Cookie: UserID=JohnDoe; Max-Age=3600; Version=1   |
| Trailer            | 指出头域在分块传输编码的尾部存在                             | Trailer: Max-Forwards                                 |
| Transfer-Encoding  | 文件传输编码                                                 | Transfer-Encoding:chunked                             |
| Vary               | 告诉下游代理是使用缓存响应还是从原始服务器请求               | Vary: *                                               |
| Via                | 告知代理客户端响应是通过哪里发送的                           | Via: 1.0 fred, 1.1 nowhere.com (Apache/1.1)           |
| Warning            | 警告实体可能存在的问题                                       | Warning: 199 Miscellaneous warning                    |
| WWW-Authenticate   | 表明客户端请求实体应该使用的授权方案                         | WWW-Authenticate: Basic                               |



## 请求method

### GET和POST

相同：都是http请求，tcp连接

不同：

| GET                                      | POST                                     |
| ---------------------------------------- | ---------------------------------------- |
| 参数再URL上                                  | 参数在request body里                         |
| 参数有长度限制                                  | 长度没有限制                                   |
| 参数只允许ASCII字符                             | post没有限制                                 |
| 明文传输，参数暴露                                | 更安全                                      |
| 点击回退或刷新时，get请求不会再次提交表单，get回退无害           | 点击回退或刷新时，post请求会再次提交表单，post是回退有害的        |
| get能被缓存，可以收藏为书签，参数保留在浏览器历史中              | post不能被缓存，不可收藏为书签，参数不会保留在浏览器历史中          |
| get请求只发送一个tcp数据包，即http header和data共同发送给web服务器，服务器响应200 OK. | post请求发送两个tcp数据包，第一次发送http header，如果web服务器予以响应100 continue，则发送第二个数据包data，服务器响应200 OK. |

### 其他请求method

#### HEAD

类似于get请求，只不过返回的响应中没有具体的内容，用于获取报头。

#### PUT

put请求与post一样都会改变服务器的数据，但是put的侧重点在于对于数据的修改操作，但是post侧重于对于数据的增加。

#### DELETE

delete请求用来删除服务器的资源。

#### CONNECT

HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器。

#### PATCH

向服务器端提交数据，请求数据在报文body里。发送一个修改数据的请求，需求数据更新（部分更新）

#### OPTIONS

options请求属于浏览器的预检请求，查看服务器是否接受请求，预检通过后，浏览器才会去发get，post，put，delete等请求。至于什么情况下浏览器会发预检请求，浏览器会会将请求分为两类，简单请求与非简单请求，非简单请求会产生预检options请求。

#### TRACE

回显服务器收到的请求，主要用于测试或诊断。

### 总结

这些不同方式的请求形式，只是一种规范定义而已，并不是说get请求无法修改服务器的数据，只是一种规范，比如你也可以所有的请求都通过post方式来访问，实现功能上面没有任何问题，只是说这种做不符合了规范而已，我们平常编码还是尽量符合规范比较好。


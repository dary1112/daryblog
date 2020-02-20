---
title: uniapp中引入高德地图以及安卓打包配置
date: 2019-11-27 18:10

categories:
- 大前端
tags:
- uniapp
---

**本文主要说明在uniapp中如何使用高德地图定位功能以及打包安卓apk时的配置**
@[toc]
<br>

uniapp官网提供了map组件，在安卓平台上集成的是高德地图，官网提供的使用方式非常简单😉：

```xml
<template>
    <view>
        <view class="page-body">
            <view class="page-section page-section-gap">
                <map style="width: 100%; height: 300px;" :latitude="latitude" :longitude="longitude" :markers="markers">
                </map>
            </view>
        </view>
    </view>
</template>
```

```javascript
export default {
    data() {
        return {
            latitude: 39.909,
            longitude: 116.39742,
            markers: []
        }
    }
}
```

<br>

现在地图上显示的位置是我们在data里面指定的经纬度，如果想要定位到设备位置我们需要调用`uni.getLocation` 方法，修改JavaScript代码码如下😁：

```javascript
export default {
    data() {
        return {
            title: 'map',
            latitude: 39.909,
            longitude: 116.39742,
            markers: []
        }
    },
    onLoad () {
        this.getMyLocation()
    },
    methods: {
		getMyLocation () {
            uni.getLocation({
                // map组件默认为国测局坐标gcj02，调用 uni.getLocation返回结果传递给组件时，需指定 type 为 gcj02 
                type: 'gcj02',
                success: ({ longitude, latitude }) => {
                    // 定位得到的经纬度
                    console.log('当前位置的经度：' + longitude)
                    console.log('当前位置的纬度：' + latitude)
                    this.longitude = longitude
                    this.latitude = latitude
                    // 画出定位位置
                    this.markers[0] = {
                        longitude,
                        latitude,
                        title: '我的位置',
                        iconPath: '/static/images/icons/myLocation.png'
                    }
                }
            })
        }
    }
}

```

 现在用真机运行就已经可以定位了，但是要在打包之后实现定位还得做进一步的配置🧐

首先需要在 [高德地图开放平台]( https://lbs.amap.com/ 'https://lbs.amap.com/')上注册成为开发者，然后进入控制台，点击应用管理-->我的应用-->创建新应用，然后填入应用名称，选择行业。

![](/img/article/uniapp-amap-1.png)

创建好了应用以后要继续创建key，这个key是我们打包安卓应用的时候必须要配置的一个东西，否则打包之后是不能定位的。按顺序填好key的名称，SHA1码和PackageName，这里的key名称是你自己填的一个标识，随便写写就行，SHA1码接下来我会说怎么获取😊，**PackageName必须跟你打包发布时的Andoid包名一致，否则也会导致不能正常使用。**

![](/img/article/uniapp-amap-2.png)

接下来我们看看怎么获取这个SHA1码，我这里就提供了一套最简洁的方案，按照步骤来绝对不会出问题😜：

### 一、安装JRE环境（如已有此环境可跳过）

从Oracle官方下载JRE安装包：[ https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html  ]( https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html   '下载JRE')

![](/img/article/uniapp-amap-3.png)

全部下一步安装即可，记住安装目录，需要配置环境变量，比如：我的安装目录是`C:\Program Files\Android\Android Studio\jre\bin`

右键我的电脑，进入属性-->高级系统设置-->高级-->环境变量-->找到Path-->新建，把这个路径粘贴进去🥴

![](/img/article/uniapp-amap-4.png)

### 二、生成证书

打开命令行窗口，执行

```powershell
keytool -genkey -alias testalias -keyalg RSA -keysize 2048 -validity 36500 -keystore test.keystore
```

这里 `-alias testalias` 中的 `testalias` 是证书别名，你可以换成其他的，最后的`-keystore test.keystore`这里的`test.keystore`是证书文件的名称，你可以换成你自己的文件名🤤，如`myapp.keystore`，回车后会提示：

```powershell
Enter keystore password:  输入证书文件密码，输入完成回车  
Re-enter new password:    再次输入证书文件密码，输入完成回车  
What is your first and last name?  
  [Unknown]:  输入名字和姓氏，输入完成回车  
What is the name of your organizational unit?  
  [Unknown]:  输入组织单位名称，输入完成回车  
What is the name of your organization?  
  [Unknown]:  输入组织名称，输入完成回车  
What is the name of your City or Locality?  
  [Unknown]:  输入城市或区域名称，输入完成回车  
What is the name of your State or Province?  
  [Unknown]:  输入省/市/自治区名称，输入完成回车  
What is the two-letter country code for this unit?  
  [Unknown]:  输入国家/地区代号（两个字母），中国为CN，输入完成回车  
Is CN=XX, OU=XX, O=XX, L=XX, ST=XX, C=XX correct?  
  [否]:  确认上面输入的内容是否正确，输入是，回车
```

 以上命令运行完成后就会生成证书，证书文件默认在执行命令的目录也就是用户名文件夹下。

然后再执行

```powershell
keytool -list -v -keystore test.keystore
```

后面`-keystore test.keystore`中的`test.keystore`就是你上面生成的证书名称，比如`myapp.keystore`，然后输入证书文件密码，就可以得到如下信息🤗：

```po
Keystore type: PKCS12    
Keystore provider: SUN    

Your keystore contains 1 entry    

Alias name: test    
Creation date: 2019-10-28    
Entry type: PrivateKeyEntry    
Certificate chain length: 1    
Certificate[1]:    
Owner: CN=Tester, OU=Test, O=Test, L=HD, ST=BJ, C=CN    
Issuer: CN=Tester, OU=Test, O=Test, L=HD, ST=BJ, C=CN    
Serial number: 7dd12840    
Valid from: Fri Jul 26 20:52:56 CST 2019 until: Sun Jul 02 20:52:56 CST 2119    
Certificate fingerprints:    
         MD5:  F9:F6:C8:1F:DB:AB:50:14:7D:6F:2C:4F:CE:E6:0A:A5    
         SHA1: BB:AC:E2:2F:97:3B:18:02:E7:D6:69:A3:7A:28:EF:D2:3F:A3:68:E7    
         SHA256: 24:11:7D:E7:36:12:BC:FE:AF:2A:6A:24:BD:04:4F:2E:33:E5:2D:41:96:5F:50:4D:74:17:7F:4F:E2:55:EB:26    
Signature algorithm name: SHA256withRSA    
Subject Public Key Algorithm: 2048-bit RSA key    
Version: 3
```

其中的 `SHA1: BB:AC:E2:2F:97:3B:18:02:E7:D6:69:A3:7A:28:EF:D2:3F:A3:68:E7 `就是我们高德里面需要的SHA1码了。

当我们把这个SHA1码正确的填到高德里面以后就可以看到key，接下来我们就可以去打包了。

### 三、打包之前的配置

在打包之前先要在项目中对manifest.json做一些配置，使用HBuilderX的可视化操作：

1. 在App模块权限配置里把Maps打开

   ![](/img/article/uniapp-amap-5.png)

2. 在App SDK里勾选高德地图，把刚刚在高德上创建的key复制进来就行了，ios的比较复杂，本文只介绍安卓，如果你只做安卓ios可以随便填一个，比如：`cd362f5dae1ab5b54edb0784f77fd748`

   ![](/img/article/uniapp-amap-6.png)

3. 现在可以开始打包了，找到状态栏 发行 --> 原生App-云打包，弹出下面弹框：

   ![](/img/article/uniapp-amap-7.png)

   

   **注意：**
   
   * Android包名这一项必须跟高德api创建key的时候的packageName一致！！！前文也提到过这件事。
   * 选择自有证书
   * 证书别名、证书私钥和证书文件就是第二步生成的证书的信息，文件路径就在你执行命令的路径，一般在`C:/Users/[你的用户名]`下，找到keystore文件就行

至此，就可以愉快的打包了，一会儿就会给你返回下载链接可以下载apk文件，这是一个临时链接，只能下载五次。
---
title: 使用video.js视频播放器以及绘制声纹图
date: 2023-01-06 18:11

categories:
- 大前端

tags:
- html5
- JavaScript
- vue
---
**本文介绍使用video.js实现视频播放器以及使用wavesurfer.js实现对声纹图的绘制。**

<br>

@[toc]

<br>

video.js 是最强大的网页嵌入式 HTML 5 视频播放器的组件库之一，也是大多数人首选的网页视频播放解决方案。本文介绍使用video.js实现视频播放器以及使用wavesurfer.js实现对声纹图的绘制，如果你只需要视频播放不需要声纹图可直接跳过wavesurfer.js部分。

## 首先安装

```bash
npm i video.js wavesurfer.js
```

## 一、视频播放器

### HTML

```html
<video ref= "video" controls></video>
```

### JS

```javascript
import Videojs from 'video.js'
import 'video.js/dist/video-js.css'

export default {
  data () {
    return {
      myPlayer: null, // 视频播放器对象
      wavesurfer: null, // 声纹图对象
    }
  },
  mounted () {
    this.initVideo()
  },
  methods: {
    initVideo () {
      // 视频叁数
      this.myPlayer = Videojs(this.$refs.video, {
        playbackRates: [0.5, 1.0, 1.5, 2.0], // 可选的播放速度
        autoplay: true, // 如果为true,刘览器准备好时开始回放，
        muted: false, // 默认情况下将会消除任何音频。
        loop: false, // 是否视频一结束就重新开始。
        preload: 'auto', // 建议浏览器在*video-加载元素后是否应该开始下载视频数据。auto浏览器选择最佳行为,立即开始加载视频（如果浏览器支持)
        language: 'zh-CN',
        aspectRatio: '16:9', // 将播放器置于流畅模式，并在计算播放器的动态大小时使用该值。值应该代表 个比例 - 用冒号分隔的两个数宇（例如'16:9"'或'4:3')
        fluid: true, // 当true时，Video.js player将拥有流体大小,换句话说，它将按比例縮放以适应其容器。
        sources: [{
          type: 'video/mp4', // 类型
          src: 'src', // url地址
        }],
        poster: '', // 封面地址
        notSupportedMessage: '此视频暂无法播放，请稍后再试', // 允许覆盖Video.js无法播放媒体源时显示的默认信息。
        controlBar: {
          timeDivider: true, // 当前时间和持续时间的分隔符
          durationDisplay: true, // 显示持续时间
          remainingTimeDisplay: true,// 是否品示剩余时问功能
          fulscreenToggle: true, // 是否显示全屏按钮
        }
      })
    }
  }
}
```

> video.js在原生video的基础上封装了很多功能，可以通过初始化的时候option来配置，更多功能可查看官网文档配置项：[https://videojs.com/guides/options/](https://videojs.com/guides/options/ "Video.js Guides")

## 二、声纹图

### HTML

```html
<div ref= "waveform" style= "pointer-events: none" ></div>
```

### JS

> 引入

```javascript
import WaveSurfer from 'wavesurfer.js'
```

> 在上面 `initVideo` 方法中加上如下对声纹图初始化的代码

```javascript
// 音频参数
this.wavesurfer = WaveSurfer.create({
  container: this.$refs.waveform, //必填参数，指定渲染的dom的id名、类名或者dom本身
  waveColor: 'violet', // 光标后面的波形的填充颜色
  barWidth: 1, // 如果设置，波纹的样式将变成类似柱状图的形状
  barHeight: 1.5, // 波纹桂状图的高度，当大于1的时候，将增加设置的高度
  cursorColor: 'red', // 鼠标点击的颜色
  progressColor: 'blue', // 光标后面的波形部分的填充颜色
  backend: 'MediaElement',
  height: 60, // 音频的显示高度
  audioRate: '1', // 音频的播放速度，数值越小越慢
})
this.wavesurfer.load('src')
this.wavesurfer.on('ready', () => {
  this.wavesurfer.setMute(true) // 静音，仅保留视频自带的音频即可
}
```

![声纹图示意图](/img/article/videojs.png '声纹图示意图')

## 三、视频和声纹图同歩

当我们操作视频时，比如拖动进度条，切换倍速，那么声纹图也应该联动，所以我们需要给视频组件加上对应事件从而控制声纹图，下面贴出联动的代码

### HTML

```html
<video
  ref= "video"
  controls
  @seeked="seeked($event)"
  @ratechange = "onRate($event)"
  @pause = "onPause($event)"
  @play= "onPlay($event)"
  @ended = "onPlayerEnded($event)"
></video>
```

### JS

```javascript
//视频倍速播放事件
onRate (i) {
  this.wavesurfer.setPlaybackRate(i.path[0].playbackRate) // 视频倍速切换音频对应倍速切换
},
// 视频进度条点击事件
seeked (i) {
  this.wavesurfer.seekTo(this.myPlayer.currentTime() / this.myPlayer.duration()) // 视频进度条拖动 音频对应位置
},
// 暂停音频
onPause(){
  this.wavesurfer.pause() // 视频暂停，音频对应暂停
},
// 播放调动音频
onPlay(){
  this.wavesurfer.play() // 视频播放 音频对应播放
},
// 视频播完回调事件
onPlayerEnded($event) {
  console.log($event)
},
```

## 四、附录1：video.js 常用功能

### 1. 配置项option

```javascript
autoplay: false, // 自动播放：true/false
controls: true, // 是否显示底部控制栏：true/false
width: 300, // 视频播放器显示的宽度
height: 300, // 视频播放器显示的高度
loop: false, // 是否循环播放:true/false
muted: false, // 设置默认播放音频：true/false
poster: '', // 视频开始播放前显示的图像的URL。这通常是一个帧的视频或自定义标题屏幕。一旦用户点击“播放”图像就会消失
src: '', // 要嵌入的视频资源url，The source URL to a video source to embed.
techOrder: ['html5', 'flash'], // 使用播放器的顺序,下面的示例说明优先使用html5播放器，如果不支持将使用flash
notSupportedMessage: false, // 是否允许重写默认的消息显示出来时，video.js无法播放媒体源
plugins: {}, // 插件
sources: [{src: '//path/to/video.mp4', type: 'video/mp4'}], // 资源文件等价于html中的形式source标签 
aspectRatio: '16:9', //  将播放器置于流体模式下，计算播放器动态大小时使用该值，该值应该是比用冒号隔开的两个数字（如“16:9”或“4:3”）。
fluid: false,	// 是否自适应布局,播放器将会有流体体积。换句话说，它将缩放以适应容器，如果<video>标签有“vjs-fluid”样式时，这个选项会自动设置为true。
preload: "metadata", // 建议浏览器是否在加载<video>元素时开始下载视频数据。(预加载)
                      // auto:立即加载视频（如果浏览器支持它）。一些移动设备将不会预加载视频，以保护用户的带宽/数据使用率。这就是为什么这个值被称为“自动”，而不是更确凿的东西
                      // metadata:只加载视频的元数据，其中包括视频的持续时间和尺寸等信息。有时，元数据会通过下载几帧视频来加载。
                      // none
```

### 2. 事件

```javascript
this.myPlayer.on('suspend', function() { // 延迟下载
    console.log("延迟下载")
});
this.myPlayer.on('loadstart', function() { // 客户端开始请求数据
    console.log("客户端开始请求数据")
});
this.myPlayer.on('progress', function() { // 客户端正在请求数据
    console.log("客户端正在请求数据")
});
this.myPlayer.on('abort', function() { // 客户端主动终止下载（不是因为错误引起）
    console.log("客户端主动终止下载")
});
this.myPlayer.on('error', function() { // 请求数据时遇到错误
    console.log("请求数据时遇到错误")
});
this.myPlayer.on('stalled', function() { // 网速失速
    console.log("网速失速")
});
this.myPlayer.on('play', function() { // 开始播放
    console.log("开始播放")
});
this.myPlayer.on('pause', function() { // 暂停
    console.log("暂停")
});
this.myPlayer.on('loadedmetadata', function() { // 成功获取资源长度
    console.log("成功获取资源长度")
});
this.myPlayer.on('loadeddata', function() { // 渲染播放画面
    console.log("渲染播放画面")
});
this.myPlayer.on('waiting', function() { // 等待数据，并非错误
    console.log("等待数据")
});
this.myPlayer.on('playing', function() { // 开始回放
    console.log("开始回放")
});
this.myPlayer.on('canplay', function() { // 可以播放，但中途可能因为加载而暂停
    console.log("可以播放，但中途可能因为加载而暂停")
});
this.myPlayer.on('canplaythrough', function() { // 可以播放，歌曲全部加载完毕
    console.log("可以播放，歌曲全部加载完毕")
});
this.myPlayer.on('seeking', function() { // 寻找中
    console.log("寻找中")
});
this.myPlayer.on('seeked', function() { // 寻找完毕
    console.log("寻找完毕")
});
this.myPlayer.on('timeupdate', function() { // 播放时间改变
    console.log("播放时间改变")
});
this.myPlayer.on('ended', function() { // 播放结束
    console.log("播放结束")
});
this.myPlayer.on('ratechange', function() { // 播放速率改变
    console.log("播放速率改变")
});
this.myPlayer.on('durationchange', function() { // 资源长度改变
    console.log("资源长度改变")
});
this.myPlayer.on('volumechange', function() { // 音量改变
    console.log("音量改变")
});
```

### 3.常用方法

```javascript
const whereYouAt = this.myPlayer.currentTime() // 获取播放进度
const howLongIsThis = this.myPlayer.duration() // 获取视频持续时间，加载完成视频才可以知道视频时长，且在flash情况下无效: 
const whatHasBeenBuffered = this.myPlayer.buffered() // 缓冲，就是返回下载了多少，单位：字节数
const howMuchIsDownloaded = this.myPlayer.bufferedPercent() // 百分比的缓冲，单位：百分比
const howLoudIsIt = this.myPlayer.volume() // 声音大小（0-1之间）
const howWideIsIt = this.myPlayer.width() // 取得视频的宽度，单位：px
const howTallIsIt = this.myPlayer.height() // 获取高度，单位：px
this.myPlayer.play() // 开始播放
this.myPlayer.pause() // 播放暂停
this.myPlayer.currentTime(120) // 设置播放进度，单位：秒
this.myPlayer.volume(0.5) // 设置声音大小（0-1之间）
this.myPlayer.width(640) // 设置宽度
this.myPlayer.height(480) // 设置高度
this.myPlayer.size(640,480) // 一步到位的设置大小，（width, height）
this.myPlayer.enterFullScreen() // 全屏
this.myPlayer.enterFullScreen() // 退出全屏
```

### 4. 网络状态

```javascript
this.myPlayer.currentSrc // 返回当前资源的URL
this.myPlayer.src = value // 返回或设置当前资源的URL
this.myPlayer.canPlayType(type) // 是否能播放某种格式的资源
this.myPlayer.networkState // 0.此元素未初始化 1.正常但没有使用网络 2.正在下载数据 3.没有找到资源
this.myPlayer.load() // 重新加载src指定的资源
this.myPlayer.buffered // 返回已缓冲区域，TimeRanges
this.myPlayer.preload // none:不预载 metadata:预载资源信息 auto:立即加载视频
```

### 5. 播放状态

```javascript
this.myPlayer.currentTime = value // 当前播放的位置，赋值可改变位置
this.myPlayer.startTime // 一般为0，如果为流媒体或者不从0开始的资源，则不为0
this.myPlayer.duration // 当前资源长度 流返回无限
this.myPlayer.paused // 是否暂停
this.myPlayer.defaultPlaybackRate = value // 默认的回放速度，可以设置
this.myPlayer.playbackRate = value // 当前播放速度，设置后马上改变
this.myPlayer.played // 返回已经播放的区域，TimeRanges，关于此对象见下文
this.myPlayer.seekable // 返回可以seek的区域 TimeRanges
this.myPlayer.ended // 是否结束
this.myPlayer.autoPlay // 是否自动播放
this.myPlayer.loop // 是否循环播放
```

### 6. 视频控制

```javascript
this.myPlayer.controls //是否有默认控制条
this.myPlayer.volume = value //音量（0-1）
this.myPlayer.muted = value //静音（true为静音，false取消静音）
```

## 五、附录2：wavesurfer.js 常用功能

### 1. option配置项

* container：必填，可以是唯一的css3选择器，也可以是DOM元素
* scrollParent：true/false,要使波形滚动。
* audioRate：播放音频的速度。数字越小越慢。
* backgroundColor：更改波形容器的背景颜色。
* barGap：波浪条之间的可选间距（如果未提供）将以旧格式计算。
* barHeight：波形条的高度。大于 1 的数字将增加波形条的高度。
* barMinHeight：绘制波形条的最小高度。默认行为是在静音期间不绘制条形图。
* barRadius：使条形变圆的半径。
* barWidth：波的每一条线宽度
* cursorColor：指示播放头位置的光标填充颜色。
* cursorWidth：指示的宽度
* forceDecode：缩放时使用网络音频强制解码音频以获得更详细的波形。
* height：波形的高度。以像素为单位。
* hideScrollbar：是否在正常显示时隐藏水平滚动条。
* hideCursor：将鼠标悬停在波形上时隐藏鼠标光标。默认情况下会显示。
* interact：是否在初始化时启用鼠标交互。您可以在以后随时切换此参数。
* loopSelection：（与区域插件一起使用）启用所选区域的循环。
* maxCanvasWidth：单个画布的最大宽度（以像素为单位），不包括小的重叠（2 * pixelRatio，四舍五入到下一个偶数整数）。如果波形比此值长，则将使用额外的画布来渲染波形，这对于浏览器无法在单个画布上绘制的非常大的波形非常有用。该参数仅适用于MultiCanvas渲染器。
* mediaControls：这将启用媒体元素的本机控件。
* mediaType：`audio`或 `video`。
* minPxPerSec：每秒音频的最小像素数。
* partialRender：使用 PeakCache 提高大波形的渲染速度。
* progressColor：光标后面波形部分的填充颜色。当progressColor和waveColor相同时，根本不会渲染进度波
* waveColor：光标后波形的填充颜色
* responsive：如果设置为true调整波形大小，则在调整窗口大小时。默认情况下，这是使用 100 毫秒超时去抖动的。如果此参数是一个数字，则表示该超时。

### 2.常用方法

```javascript
this.wavesurfer.cancelAjax() // 取消音频文件加载过程。
this.wavesurfer.destroy() // 删除事件、元素并断开 Web 音频节点。
this.wavesurfer.empty() // 清除波形，就像加载了零长度音频一样。
this.wavesurfer.getActivePlugins() // 返回当前初始化的插件名称映射。
this.wavesurfer.getBackgroundColor() // 返回波形容器的背景颜色。
this.wavesurfer.getCurrentTime() // 以秒为单位返回当前进度。
this.wavesurfer.getCursorColor() // 返回指示播放头位置的光标的填充颜色。
this.wavesurfer.getDuration() // 以秒为单位返回音频剪辑的持续时间。
this.wavesurfer.getPlaybackRate() // 返回音频剪辑的播放速度。
this.wavesurfer.getProgressColor() // 返回光标后面波形的填充颜色。
this.wavesurfer.getVolume() // 返回当前音频剪辑的音量。
this.wavesurfer.getMute() // 返回当前静音状态。
this.wavesurfer.getFilters() // 返回当前设置过滤器的数组。
this.wavesurfer.getWaveColor() // 返回光标后波形的填充颜色。
this.wavesurfer.exportPCM(length, accuracy, noWindow, start) // 将 PCM 数据导出为 JSON 数组。可选参数length [number] - default: 1024, accuracy [number] - default: 10000, noWindow [true|false] - default: false,start [number] - default: 
this.wavesurfer.exportImage(format, quality, type) // 将波形图像作为数据 URI 或 Blob 返回。
this.wavesurfer.isPlaying() // true如果当前正在播放则返回，否则返回false。
this.wavesurfer.load(url, peaks, preload) // URL通过 XHR加载音频,可选数组peaks,可选preload参数[none|metadata|auto]，如果使用后端 MediaElement ，则传递给Audio 元素。
this.wavesurfer.loadBlob(url) // 从Blob或File对象加载音频。
this.wavesurfer.on(eventName, callback) // 订阅事件,有关所有事件的列表，请参阅WaveSurfer事件。
this.wavesurfer.un(eventName, callback) // 取消订阅事件。
this.wavesurfer.unAll() // 取消订阅所有事件。
this.wavesurfer.pause() // 停止播放。
this.wavesurfer.play([start[, end]]) // 从当前位置开始播放，可选start且end以秒为单位测量可用于设置要播放的音频范围。
this.wavesurfer.playPause() // 暂停时播放，播放时暂停。
this.wavesurfer.seekAndCenter(progress) // 寻求进度和中心视图[0…1]（0 = 开始，1 = 结束）。
this.wavesurfer.seekTo(progress) // 寻求进步[0…1]（0 = 开始，1 = 结束）。
this.wavesurfer.setBackgroundColor(color) // 设置波形容器的背景颜色。
this.wavesurfer.setCursorColor(color) // 设置指示播放头位置的光标的填充颜色。
this.wavesurfer.setHeight(height) // 设置波形的高度。
this.wavesurfer.setFilter(filters) // 用于将您自己的 WebAudio 节点插入图中，请参阅下面的连接过滤器。
this.wavesurfer.setPlaybackRate(rate) // 设置播放速度（0.5半速、1正常速度、2双速等）。
this.wavesurfer.setPlayEnd(position) // 设置播放停止点（以秒为单位）。
this.wavesurfer.setVolume(newVolume) // 将播放音量设置为新值[0…1]（0 = 静音，1 = 最大）。
this.wavesurfer.setMute(mute) // 静音当前声音。可以是true静音或false取消静音的布尔
this.wavesurfer.setProgressColor(color) // 设置光标后面波形的填充颜色。
this.wavesurfer.setWaveColor(color) // 设置光标后波形的填充颜色。
this.wavesurfer.skip(offset) // 从当前位置跳过几秒钟（使用负值向后移动）。
this.wavesurfer.skipBackward() // 倒带skipLength秒。
this.wavesurfer.skipForward() // 跳过skipLength几秒钟。
this.wavesurfer.setSinkId(deviceId) // 设置接收器 ID 以更改音频输出设备。
this.wavesurfer.stop() // 停止并转到开头。
this.wavesurfer.toggleMute() // 打开和关闭音量。
this.wavesurfer.toggleInteraction() // 切换鼠标交互。
this.wavesurfer.toggleScroll() // 切换scrollParent。
this.wavesurfer.zoom(pxPerSec) // 水平放大和缩小波形。该参数是每秒音频的水平像素数。它还会更改参数minPxPerSec并启用该 scrollParent选项
```

### 3. 常用事件

> 使用on()和un() 方法订阅和取消订阅各种播放器事件

* audioprocess– 在音频播放时持续触发。也在寻找上火。
* dblclick – 双击实例时。
* destroy – 当实例被销毁时。
* error– 发生错误。回调将收到（字符串）错误信息。
* finish – 当它完成播放时。
* interaction – 与波形交互时。
* loading– 使用 fetch 或 drag’n’drop 加载时连续触发。回调将以百分比 [0…100] 接收（整数）加载进度。
* mute– 静音更改。回调将收到（布尔值）新的静音状态。
* pause – 音频暂停时。
* play – 播放开始时。
* ready– 加载音频、解码并绘制波形时。使用 MediaElement 时，这会在绘制波形之前触发，请参阅waveform-ready。
* scroll- 当滚动条移动时。回调将接收一个ScrollEvent对象。
* seek– 在寻求。回调将收到（浮动）进度[0…1]。
* volume– 关于音量变化。回调将接收（整数）新卷。
* waveform-ready– 在使用 MediaElement 后端绘制波形后触发。如果您使用的是 WebAudio 后端，则可以使用ready.
* zoom– 关于缩放。回调将接收（整数）minPxPerSec。

---
title: vue3.0新特性
date: 2020-04-22 20:50

categories:
- 大前端
tags:
- vue
---

**本文聊聊昨晚尤雨溪直播vue3.0的一些新特性。**

<br>

@[toc]

<br>

## 前言

在4月21日晚，Vue作者尤雨溪在哔哩哔哩直播分享了`Vue.js 3.0 Beta`最新进展。 以下是直播内容整理

## 一、全新文档`RFCs`

![RFCs](/img/article/vue3-RFCs.jpg)

`Vue.js 3.0 Beta`发布后的工作重点是保证稳定性和推进各类库集成

所有的进度和文档都将在全新`RFCs`文档可以看到

## 二、六大亮点

![六大亮点](/img/article/vue3-六大亮点.jpg)

- `Performance`：性能更比`Vue 2.0`强。
- `Tree shaking support`：可以将无用模块“剪辑”，仅打包需要的。
- `Composition API`：组合`API`
- `Fragment, Teleport, Suspense`：“碎片”，`Teleport`即`Protal传送门`，“悬念”
- `Better TypeScript support`：更优秀的Ts支持
- `Custom Renderer API`：暴露了自定义渲染`API`

下面将按顺序分别描述。

### `1. Performance - 性能`

![Performance](/img/article/vue3-Performance.jpg)

1. 重写了虚拟`Dom`的实现（且保证了兼容性，脱离模版的渲染需求旺盛）。
2. 编译模板的优化。
3. 更高效的组件初始化。
4. `update`性能提高1.3~2倍。
5. `SSR`速度提高了2~3倍。

下面是各项性能对比

![各项性能对比](/img/article/vue3-各项性能对比.jpg)

#### 要点1：编译模板的优化

![编译模板的优化](/img/article/vue3-编译模板的优化.jpg)

假设要编译以下代码

```html
<div>
  <span/>
  <span>{{ msg }}</span>
</div>
```

将会被编译成以下模样：

```javascript
import {
    createVNode as _createVNode,
    toDisplayString as _toDisplayString,
    openBlock as _openBlock,
    createBlock as _createBlock
} from 'vue'

export function render (_ctx, _cache) {
  return (_openBlock(), _createBlock("div", null, [
    _createVNode("span", null, "static"),
    _createVNode("span", null, _toDisplayString(_ctx.msg), 1 /* TEXT */)
  ]))
}
```

注意看第二个`_createVNode`结尾的“1”：

Vue在运行时会生成`number`（大于0）值的`PatchFlag`，用作标记。

![编译案例1](/img/article/vue3-编译案例1.jpg)

仅带有`PatchFlag`标记的节点会被真正追踪，且**无论层级嵌套多深，它的动态节点都直接与`Block`根节点绑定，无需再去遍历静态节点**

再看以下例子：

![编译案例2](/img/article/vue3-编译案例2.jpg)

```html
<div>
  <span>static</span>
  <span :id="hello" class="bar">{{ msg }}   </span>
</div>
```

会被编译成：

```javascript
import {
    createVNode as _createVNode,
    toDisplayString as _toDisplayString,
    openBlock as _openBlock,
    createBlock as _createBlock
} from "vue"

export function render(_ctx, _cache) {
  return (_openBlock(), _createBlock("div", null, [
    _createVNode("span", null, "static"),
    _createVNode("span", {
      id: _ctx.hello,
      class: "bar"
    }, _toDisplayString(_ctx.msg), 9 /* TEXT, PROPS */, ["id"])
  ]))
}
```

> PatchFlag` 变成了`9 /* TEXT, PROPS */, ["id"]

它会告知我们不光有`TEXT`变化，还有`PROPS`变化（id）

这样既跳出了`virtual dom`性能的瓶颈，又保留了可以手写`render`的灵活性。 等于是：既有`react`的灵活性，又有基于模板的性能保证。

#### 要点2: 事件监听缓存：`cacheHandlers`

![事件监听缓存](/img/article/vue3-事件监听缓存.jpg)

假设我们要绑定一个事件：

```html
<div>
  <span @click="onClick">
    {{msg}}
  </span>
</div>
```

关闭`cacheHandlers`后：

```javascript
import {
    toDisplayString as _toDisplayString,
    createVNode as _createVNode,
    openBlock as _openBlock,
    createBlock as _createBlock
} from "vue"

export function render(_ctx, _cache) {
  return (_openBlock(), _createBlock("div", null, [
    _createVNode("span", { onClick: _ctx.onClick }, _toDisplayString(_ctx.msg), 9 /* TEXT, PROPS */, ["onClick"])
  ]))
}
```

`onClick`会被视为`PROPS`动态绑定，后续替换点击事件时需要进行更新。

开启`cacheHandlers`后：

```javascript
import { toDisplayString as _toDisplayString, createVNode as _createVNode, openBlock as _openBlock, createBlock as _createBlock } from "vue"

export function render(_ctx, _cache) {
  return (_openBlock(), _createBlock("div", null, [
    _createVNode("span", {
      onClick: _cache[1] || (_cache[1] = $event => (_ctx.onClick($event)))
    }, _toDisplayString(_ctx.msg), 1 /* TEXT */)
  ]))
}
```

`cache[1]`，会自动生成并缓存一个内联函数，“神奇”的变为一个静态节点。 Ps：相当于`React`中`useCallback`自动化。

并且支持手写内联函数：

```html
<div>
  <span @click="()=>foo()">
    {{msg}}
  </span>
</div>
```

#### 补充：`PatchFlags`枚举定义

而通过查询`Ts`枚举定义，我们可以看到分别定义了以下的追踪标记：

```javascript
export const enum PatchFlags {
  
  TEXT = 1,// 表示具有动态textContent的元素
  CLASS = 1 << 1,  // 表示有动态Class的元素
  STYLE = 1 << 2,  // 表示动态样式（静态如style="color: red"，也会提升至动态）
  PROPS = 1 << 3,  // 表示具有非类/样式动态道具的元素。
  FULL_PROPS = 1 << 4,  // 表示带有动态键的道具的元素，与上面三种相斥
  HYDRATE_EVENTS = 1 << 5,  // 表示带有事件监听器的元素
  STABLE_FRAGMENT = 1 << 6,   // 表示其子顺序不变的片段（没懂）。 
  KEYED_FRAGMENT = 1 << 7, // 表示带有键控或部分键控子元素的片段。
  UNKEYED_FRAGMENT = 1 << 8, // 表示带有无key绑定的片段
  NEED_PATCH = 1 << 9,   // 表示只需要非属性补丁的元素，例如ref或hooks
  DYNAMIC_SLOTS = 1 << 10,  // 表示具有动态插槽的元素
  // 特殊 FLAGS -------------------------------------------------------------
  HOISTED = -1,  // 特殊标志是负整数表示永远不会用作diff,只需检查 patchFlag === FLAG.
  BAIL = -2 // 一个特殊的标志，指代差异算法（没懂）
}
```

> 感兴趣的可以看源码：`packages/shared/src/patchFlags.ts`

### 2. `Tree shaking support`

![Tree shaking support](/img/article/vue3-Treeshakingsupport.jpg)

- 可以将无用模块“剪辑”，仅打包需要的（比如`v-model,`，用不到就不会打包）。
- 一个简单'`HelloWorld`'大小仅为：13.5kb
  - 11.75kb，仅`Composition API`。
- 包含运行时完整功能：22.5kb
  - 拥有更多的功能，却比`Vue 2`更迷你。

很多时候，我们并不需要 `vue`提供的所有功能，在 `vue 2` 并没有方式排除掉，但是 3.0 都可能做成了按需引入。

### 3.  `Composition API`

![Composition API](/img/article/vue3-CompositionAPI.jpg)

与`React Hooks` 类似的东西，实现方式不同。

- 可与现有的 `Options API`一起使用
- 灵活的逻辑组合与复用
- `vue 3`的响应式模块可以和其他框架搭配使用

混入(`mixin`) 将不再作为推荐使用， `Composition API`可以实现更灵活且无副作用的复用代码。

感兴趣的可以查看：[composition-api.vuejs.org/#summary](https://composition-api.vuejs.org/#summary)

![API官网截图](/img/article/vue3-API官网截图.jpg)

`Composition API`包含了六个主要`API`

可以到这里查看：[composition-api.vuejs.org/api.html#se…](https://composition-api.vuejs.org/api.html#setup)

Ps：其它的均为常见的工具函数，可先忽略不看。

### 4.  `Fragment`

![Fragment](/img/article/vue3-Fragment.jpg)

`Fragment`翻译为：“碎片”

- 不再限于模板中的单个根节点
- `render` 函数也可以返回数组了，类似实现了 `React.Fragments` 的功能 。
- '`Just works`'

#### 4.1  `<Teleport>`

![Teleport](/img/article/vue3-Teleport.jpg)

- 以前称为`<Portal>`，译作传送门
- 原先是对标 `React Portal`（增加多个新功能，更强）

*但因为`Chrome`有个提案，会增加一个名为`Portal`的原生`element`，为避免命名冲突，改为`Teleport`*

#### 4.2  `<Suspense>`

![Suspense](/img/article/vue3-Suspense.jpg)

`Suspense`翻译为：“悬念”

- 可在嵌套层级中等待嵌套的异步依赖项
- 支持`async setup()`
- 支持异步组件

虽然`React 16`引入了`Suspense`，但直至现在都不太能用。如何将其与异步数据结合，还没完整设计出来。

Vue 3 的`<Suspense>`更加轻量：

* 仅5%应用能感知运行时的调度差异，综合考虑下，Vue3 的`<Suspense>` 没和React一样做运行调度处理

### 5. 更好的`TypeScript`支持

![更好的TypeScript支持](/img/article/vue3-更好的TypeScript支持.jpg)

- `Vue 3`是用`TypeScript`编写的库，可以享受到自动的类型定义提示
- `JavaScript`和`TypeScript`中的API是相同的。
  - 事实上，代码也基本相同
- 支持`TSX`
- `class`组件还会继续支持，但是需要引入`vue-class-component@next`，该模块目前还处在 alpha 阶段。

还有`Vue 3 + TypeScript` 插件正在开发，有类型检查，自动补全等功能。目前进展可喜。

![ts类型检查插件](/img/article/vue3-ts类型检查插件.jpg)

### 6. `Custom Renderer API`

![Custom Renderer API](/img/article/vue3-CustomRendererAPI.jpg)

自定义渲染器API

- 正在进行`NativeScript Vue`集成
- 用户可以尝试`WebGL`自定义渲染器，与普通Vue应用程序一起使用（`Vugel`）。

意味着以后可以通过 `vue`， `Dom` 编程的方式来进行 `webgl` 编程 。感兴趣可以看这里：[Getting started vugel](https://vugel.planning.nl/#application)

## 三、剩余工作

![What's Left](/img/article/vue3-What'sLeft.jpg)

### 1. `Docs & Migration Guides`

![Docs & Migration Guides](/img/article/Docs&MigrationGuides.jpg)

- 新的文档编写交由`@NataliaTepluhina, @sdras, @bencodezen & @phanan` 负责
- `@sdras` 正在做自动升级迁移工具
- `@sodatea` 已经开始研究`CodeMods`

### 2. `Router`

![Router](/img/article/vue3-Router.jpg)

下一代 `Router`：`vue-router@next`已在`alpha`阶段，感谢`@posva`

有部分的`API`变动，可到`RFC`上看。

### 3. `Vuex`

![Vuex](/img/article/vue3-Vuex.jpg)

- 下一代`Vuex`：，`vuex@next`（与`Vue 3 compat`相同的API），已在`alpha`阶段，感谢`@KiaKing`。
- 团队正在为下一次迭代试验`Vuex API`的简化

目前以兼容`Vue 3`为主，基本上没有`API`变动，莫慌。

### 4. `CLI`

![CLI](/img/article/vue3-CLI.jpg)

- `CLI`插件：`vue-cli-plugin-vue-next`by `@sodatea`
- （wip）`CodeMods`支持升级`Vue 2`应用

### 5. 新工具：`vite`（法语 “快”）

vite

地址：[github.com/vuejs/vite](https://github.com/vuejs/vite)



一个简易的`http`服务器，无需`webpack`编译打包，根据请求的`Vue`文件，直接发回渲染，且支持热更新（非常快）

### 6. `vue-test-utils`

![vue-test-utils](/img/article/vue3-vue-test-utils.jpg)



- 下一代`test-utils`by `@lmiller1990, @dobromir-hristov, @afontcu & @JessicaSachs`

### 7. `DevTools`

![DevTools](/img/article/vue3-DevTools.jpg)

早期的原型已经由@Akryum 完成，当我们到`beta`时，将完全集成。

目前需要花更多精力去完善。

### 8. `IDE Support (Vetur)`

![IDE Support](/img/article/vue3-IDESupport.jpg)

- `@znck`目前正在试验模板的类型检查
- `@octref`将在5月为`Vue 3`进行`Vetur`集成

### 9. `Nuxt`

![Nuxt](/img/article/vue3-Nuxt.jpg)

目前`Nuxt`的整合工作也正在进行中，内部团队已经跑起来了。还需要时间磨合

## 四、`Vue 2.x`还有2.7版本

![Vue2.x还有2.7版本](/img/article/vue3-Vue2.x还有2.7版本.jpg)

- 将有最后一个小版本（2.7）
- 从`Vue 3`向后移植兼容的改进(不损坏兼容性前提下)
- 加上在`Vue 3`中删除的功能的弃用警告
- `LTS1` 18个月。

最后建议：`Vue 3`虽好，如果你的项目很稳定，且对新功能无过多的要求或者迁移成本过高，则不建议升级。



> 参考资料：https://juejin.im/post/5e9f6b3251882573a855cd52
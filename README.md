- 看了前端FE目录部分，非常值得借鉴的写法。使用beyond compare 对比platform/web和platform/qy 会有惊喜：
modules(attr, props, class, style)操作几乎没有改；node-ops接口一一对应。这里最漂亮的实现，抽象了一层QNode。nodeops.js里边的createElement使用的是new QNode()。这样，vnode.el变成QNode类型，那么原有modules上的操作，例如modules/style.js里边的setProperty, el.setStyle(), modules/events.js里边的addEventListener/removeEventListener等，直接转换到QNode的实现里，无需改动modules文件。node-ops非常好理解，就是普通的节点操作。
- QNode在js层做了id维护。
- addDirect是jsBridge的通讯方法，分为dom, attr, com, api, native5类。前面2类用在node-ops和modules，后面应该是native特殊能力的调用，源码使用很少。addDirect会将操作存放到一个数组中，那何时出发主动的更新呢？看源码就是watcher.run()，qy源码的实现上是这样的

```
Vue.prototype.__patch__ = patch

// public mount method
Vue.prototype.$mount = function (
  hydrating?: boolean
): Component {
  // regist mount patch for root
  bridge.readyToPatch();
  return mountComponent(this, undefined, hydrating)
}

// regist update patch
const _update = Vue.prototype._update;
Vue.prototype._update = function (vnode: VNode, hydrating?: boolean) {
  _update.call(this,vnode,hydrating);
  bridge.readyToPatch();
}
```
- 概括就是$mount的时候触发一次，_update的时候触发一次。但是我理解，_update应该就够了。$mount最终也会触发到_update的。_update是template更新时候的执行的方法。之前跟踪源码就知道这里，但是不是有其他更好的地方可以注入更新呢？可能更多的框架选择直接将操作发送过去了。不会等待。这里有点批量发送的意味。

- 入口与环境判定。Android和iOS往v8或者JSC的global对象中，注入了__base__对象，通过__base__对象判断。具体看env文件实现，另外vue本身的env可能有冲突，未查看，应该是个小问题。

- platform/qy/webview应该是无用的，未看到哪个文件有所引用。

- npm run build直接只build出qy相关vue runtime，rollup的entry就是src/platform/node-ops.

- 这个项目确实足够简单，未写入过多繁琐逻辑，比weex容易看多啦 ~ weex有些为了复杂而复杂的感觉。我认为前端部分是最薄的才对，不要整那么多逻辑。重心还是要下沉都native。

# Introduction

<p align="center" >
  <img src="https://github.com/iqiyi/LiteApp/blob/master/Images/logo.png?raw=true" alt="LiteApp" title="LiteApp">
</p>

**LiteApp is a high performance mobile cross-platform framework. The implementation of its cross-platform functionality is based on webview but improved with novel ideas and solutions for better performance.**

`LiteApp` dedicates to enable developers to use modern web development technology to build applications on both Android and iOS with a single codebase. More specifically, you can use javascript and modern front-end framework Vue.js to develop mobile apps by using LiteApp, with which, productivity and performance can coexist ,application you build will be running on web with performance close to native. We achieve this by decoupling the render engine from the syntax layer, see more detail below.

## Requirements

| Platform |        System        |       Notes        |
| :------: | :------------------: | :----------------: |
|   iOS    |   iOS 9.0 or later   | Xcode 9.0 or later |
| Android  | Android 4.0 or later |        n/a         |
|   Web    |         n/a          |        n/a         |

## Architecture

`系统架构图`

<div align=center>
<img src="https://github.com/iqiyi/LiteApp/blob/master/Images/Architecture.png?raw=true" alt="Architecture"/>
</div>`

## 

## Demo Project

[ iOS Display ]() 

> <div align=center>
>
> <img src="https://github.com/iqiyi/LiteApp/blob/master/Images/iOS_Video.gif?raw=true" width = "300" height = "300*16/9"  alt="iOS Demo"/>
>
> </div>`

[Android Display]() 

<div align=center>

<img src="https://github.com/iqiyi/LiteApp/blob/master/Images/Andriod_Video.gif?raw=true" width = "300" height = "300*16/9" alt="Android Demo"/>
</div>`

## Features

- [x] **High Performance**: Writing on web, have the same performance as the native app
- [x] **Load Fast** Fast rendering  for all pages , especially for the first time
- [x] **Mobile Cross-platform**  Build both Android and iOS  with a single codebase
- [x] **Asynchronous Threads** the render engines are separated from the syntax layer 
- [x] **Simple Code**  Few code but powerful
- [x] **Expandable**   Proprietary API for extension and it can add more features
- [x] **Complete Documentation** Each section has a corresponding document and easy to understand

## How To Get Started

- [Download LiteApp](https://github.com/iqiyi/LiteApp/wiki/common-Download-LiteApp) and try out the included Mac and iPhone example apps
- Read the ["Getting Started" guide](https://github.com/iqiyi/LiteApp/wiki/common-How-To-Get-Started), or [other articles on the Wiki](https://github.com/iqiyi/LiteApp/wiki/home)
- Check out the [documentation](https://github.com/iqiyi/LiteApp/wiki/common-Documents) for a comprehensive look at all of the APIs available in LiteApp

## Installation

```
$ git clone https://github.com/iqiyi/LiteApp.git
$ cd liteApp
```

## Communication

- If you **need help**, use [Email](zhangyanqiang@qiyi.com) or [Github](https://github.com/iqiyi/LiteApp) . (Tag 'LiteApp')
- If you'd like to **ask a general question**, use [Email](zhangyanqiang@qiyi.com) or [Github](https://github.com/iqiyi/LiteApp).
- If you **found a bug**, _and can provide steps to reliably reproduce it_, open an issue.
- If you **have a feature request**, open an issue.
- If you **want to contribute**, submit a pull request.

## Performance Test

`LiteApp VS H5 App Results：70% reduction in first load time，Switch Page is 60 FPS`

| Project   | Loading Time/ms |         Switch Page/fps          |
| :-------- | :-------------: | :------------------------------: |
| LiteApp   |   250-500 ms    |            Perfect/60            |
| HTML5 App |    > 1000ms     | White screen for a short time/53 |

`LiteApp`

> <div align=center>
>
> <img src="https://github.com/iqiyi/LiteApp/blob/master/Images/liteApp.gif?raw=true" width = "300" height = "300*16/9"  alt="iOS Demo"/>
>
> </div>`

`HTML5 App`

<div align=center>

<img src="https://github.com/iqiyi/LiteApp/blob/master/Images/html5.gif?raw=true" width = "300" height = "300*16/9" alt="Android Demo"/>
</div>`

## 

## Credits

LiteApp was originally created by [Guodong Chen](www.breakerror.com)  [Chen Zhang](https://github.com/zhch0633)  [Jingyuan Zhou](https://github.com/pricelesss)   [Yanqiang Zhang](https://github.com/Richard-zhang-iOS) .

LiteApp's logo was designed by [Guodong Chen](www.breakerror.com)

And most of all, thanks to LiteApp's Contributors

### Security Disclosure

If you believe you have identified a security vulnerability with LiteApp, you can contact  [Guodong Chen](www.breakerror.com) as soon as possible. Please do not post it to a public issue tracker.

## License

LiteApp is released under the Apache License, Version 2.0. See [LICENSE](http://gitlab.qiyi.domain/cross-team/lite-app/blob/master/LICENSE) for details.

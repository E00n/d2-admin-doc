---
sidebar: auto
---

# 常见问题

本章总结收到的用户反馈问题，集中展示，方便后续用户自助解决问题

## 代码下载慢

建议使用 [Free Download Manager](http://www.freedownloadmanager.org/download.htm) 下载，速度会有显著提升

![](https://qiniucdn.fairyever.com/20180722210734.png?imageMogr2/auto-orient/thumbnail/1480x/blur/1x0/quality/100|imageslim)

## 无法启动项目

如果在 `run dev` 或者 `npm i` 的过程中报错，首先建议您升级 node 版本 > 8，在以下环境测试可用

``` {10}
➜  ~ npm -v
5.6.0
➜  ~ node -v
v8.11.1
➜  ~ nrm -V
1.0.2
➜  ~ nrm ls
  npm ---- https://registry.npmjs.org/
  cnpm --- http://r.cnpmjs.org/
* taobao - https://registry.npm.taobao.org/
  nj ----- https://registry.nodejitsu.com/
  rednpm - http://registry.mirror.cqupt.edu.cn/
  npmMirror  https://skimdb.npmjs.com/registry/
  edunpm - http://registry.enpmjs.org/
```

::: tip
推荐使用 [nrm](https://github.com/Pana/nrm) 管理 npm 源，不建议使用 cnpm
:::

## 没有自动打开浏览器

如果您运行

``` bash
npm run serve
npm run start
npm run dev
```

其中任何一个，编译完成后没有自动打开浏览器，请尝试在本机安装 [vue-cli3](https://cli.vuejs.org/zh/) 后重新启动项目。

## 热更新失效

如果使用了 cnpm，cnpm 使用 symlink，导致 webpack 打包的时候抒符号链接的包打包了两份，由于webpack/hot/emitter.js 为两个不同实例，导致 webpack hot/server webpackHotUpdate 无法触发，热更新失效。解决方法：设置 registry，不使用 cnpm。

``` bash
npm config set registry https://registry.npm.taobao.org
npm install
```

如果上面的方法无效，尝试在 `vue.config.js` 中如下设置：

``` js
module.exports = {
  chainWebpack: config => {
    config.resolve
      .symlinks(true)
      return config
  }
}
```

如果不清楚怎么写可以参考新版 D2Admin 的配置。

> There is a resolve.symlinks in webpack configuration, and it's default value is true. Howerver, project created by vue-cli, the resolve.symlinks is set to false.

其它解决办法请参考 [vue-cli/issues/1559](https://github.com/vuejs/vue-cli/issues/1559)。

## node-sass 安装失败

由于某些不可描述的原因，利用 npm 进行安装模块的时候会发生包下载失败的情况，node-sass 尤其的频繁，或者说 node-sass 的二进制文件是接近百分百失败的，即使用 yarn 安装也依旧在这个点失败，给出以下建议

**方法1**

首先，需要提前下载 node-sass 的二进制文件，这个文件可以去 cnpm 仓库下载或者 node-sass 的 github 上去下载，在下载之前我们需要先查看电脑的系统的版本，来确定适合哪个版本的二进制文件，查看版本的指令如下：

```
node -p "[process.platform, process.arch, process.versions.modules].join('-')"
```

输入这个指令后会弹出一个系统版本，然后在下面两个地址中选择一个去下载对应系统版本的后缀为 .node 的 node-sass 文件

[cnpm https://npm.taobao.org/mirrors/node-sass/](https://npm.taobao.org/mirrors/node-sass/)

[github https://github.com/sass/node-sass/releases](https://github.com/sass/node-sass/releases)

下载完保存到任意位置，最好放置到 package.json 所在位置。然后我们需要手动指定 node-sass 二进制文件的下载源为下载的那个文件，以下是npm与yanr的指令：

npm

```
npm config set sass-binary-path 你存放刚才下载的二进制文件的目录
// 例如 npm config set sass-binary-path e:/web/win32-x64-48_binding.node
```

yarn

```
yarn config set sass-binary-path 你存放刚才下载的二进制文件的目录
// 例如 yarn config set sass-binary-path e:/web/win32-x64-48_binding.node
```

然后我们即可用正常指令下载了

::: tip 注意
此方法会绑定为本地文件，即无法更新 node-sass 了。如果不希望这样，请使用第二种方法
:::

**方法2**

此方案将把下载源指定为cnpm仓库，更建议使用这种方法

全部的下载源指向cnpm的指令

npm

```
npm config set registry http://registry.npm.taobao.org
```

yarn

```
yarn config set registry http://registry.npm.taobao.org
```

只指定node-sass的下载源（建议使用）

npm

```
npm config set sass-binary-site http://npm.taobao.org/mirrors/node-sass
```

yarn

```
yarn config set sass-binary-site http://npm.taobao.org/mirrors/node-sass
```

## 如何关闭 ESLint

由于使用者技术水平参差不齐，有些朋友希望在开发时关闭 ESLint，虽然我不建议这样做，在这里也给出以下方法，或者你也可以在下面的链接里找到答案

[ESLint 中文](http://eslint.cn/) | [ESLint 英文](https://eslint.org/)

* 方法 1

在根目录的 `.eslintignore` 中添加 `*.vue`，就会忽略所有 vue 文件后缀的检查，js 文件同理。修改后重启本地服务。

* 方法 2

在根目录中的 `.eslintrc.js` 找到 `'@vue/standard'` 并注释掉，修改后重启本地服务。

## 如何修改 ESLint 规则

在根目录中的 `.eslintrc.js` 配置规则

**语法**

``` js
rules: {
  "规则名": [规则值, 规则配置]
}
```

**规则值**

``` js
"off" 或者 0 //关闭规则关闭
"warn" 或者 1 //在打开的规则作为警告（不影响退出代码）
"error" 或者 2 //把规则作为一个错误（退出代码触发时为1）
```

* [规则列表 中文](http://eslint.cn/docs/rules/)
* [规则列表 英文](https://eslint.org/docs/rules/)

## 无法跳转路由

有可能你在 D2Admin 的基础上进行你的开发时，发现在登录页面进行

``` js
this.$router.push({ name: 'index' })
```

跳转的时候页面并没有跳转，这是因为你很有可能没有进行下面的操作：

``` js
Cookies.set('token', res.token, setting)
```

原因根源是在 `src/router/index.js` 中有如下一段代码，根据 `token` 判断用户是否登录

``` js {3-9}
router.beforeEach((to, from, next) => {
  if (to.matched.some(r => r.meta.auth)) {
    if (Cookies.get('token')) {
      next()
    } else {
      next({
        name: 'login'
      })
    }
  } else {
    next()
  }
})
```

所以如果你没有存 token 字段在 cookie 中，路由鉴权机制将会重定向到登录页

如果你想修改基于 token 验证用户登录状态的机制，请在 `./src` 下搜索 `token` 关键字并修改他们，但是我**十分不建议你修改它们**

最好的做法是在登录后返回本次登录的 token 并且存储在 cookie 中，然后在每次 ajax 请求时都携带这个 token 给后端做权限验证（必要的话你可以还可以增加 token 的更新机制）

::: tip 同样需要注意的地方
除了需要在 cookie 中保存 token，你还要保存 uuid 字段，意为“用户唯一标识”

``` js
Cookies.set('uuid', res.uuid, setting)
```

D2Admin 会在很多地方使用 cookie 中的此字段区分用户，比如不同用户选择的不同主题的数据持久化，还有不同用户打开的多标签页数据的持久化存储。
:::

## 异步请求无法携带 cookie

请先确认您已经给 axios 设置了 `axios.defaults.withCredentials = true`：

::: tip
一般您应该在 src/plugin/axios/index.js 文件中设置 axios。
:::

较新的 D2Admin：

``` js {4}
const service = axios.create({
  baseURL: process.env.VUE_APP_API,
  timeout: 5000, // 请求超时时间
  withCredentials: true
})
```

或者在较老的 D2Admin 版本 axios 配置文件中添加高亮部分：

``` js {3}
import axios from 'axios'

axios.defaults.withCredentials = true

axios.interceptors.response.use(res => {
  return res.data
}, err => {
  return Promise.reject(err)
})

export default {
  install (Vue, options) {
    Vue.prototype.$axios = axios
  }
}
```

通常这样您已经可以在发送异步请求的时候携带 cookie，但是在 D2 中很可能不行，您还需要禁用掉 mock.js，通常在 main.js 中，请注释掉相关引用代码，并保证 mock.js 没有在项目中其它任何地方被引用。

::: tip 为什么这样做
只要调用过 mock.js 的 mock 方法，他就会劫持 window 的 XMLHttpRequest 对象，用一个自己的 XHR 替换，那里面withCredentials 是 false 的，所以无法携带 cookie。
:::

::: tip 补充
此问题在 [版本 1.5.3](https://github.com/d2-projects/d2-admin/releases/tag/1.5.3) 已经提供解决方案。
:::

## 删除页面右上角 github 链接

在 `src/components/demo/d2-demo-page-cover/index.vue` 中删除相关代码即可

## 兼容性

首先 vue.js 和 ElementUI 做不到兼容的，D2Admin 肯定也兼容不了，实测在 macOS 下 Chrome 和新版本的火狐浏览器以及 Safari 都正常使用，这类管理系统一般是内部使用，通常不必太纠结兼容低版本浏览器，如果你发现了显示的 bug，可以加 QQ 群反应，如果你可以修复这个 bug 使其在你的浏览器上显示正常，欢迎你的 pr。

## 项目里有未完成的代码

有些示例（比如 v-charts 和 ElementUI）是很方便就可以找到官网示例和文档的，这些插件和组件的示例在本项目中就可能处于未完成的状态，但是以后会完成

## unexpected end of file

报错代码

```
E:\VS\TFS_FREE_Z\BaseProjects\VUE\d2-admin-z>npm i
npm WARN tar zlib error: unexpected end of file
npm ERR! cb() never called!
npm ERR! This is an error with npm itself. Please report this error at:
npm ERR!     <https://github.com/npm/npm/issues>
npm ERR! A complete log of this run can be found in:
npm ERR!     C:\Users\ZHZ\AppData\Roaming\npm-cache\_logs\2018-07-27T13_13_56_693Z-debug.log
```

解决方法

目前只有一位朋友遇到这个问题，最后使用 cnpm 绕过了这个错误

```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

## 打包后 CSS 文件位置出错

如果您发现打包后 CSS 文件出现在 dist 文件夹目录内，请检查 `d2-admin/vue.config.js` 中 `baseUrl` 的设置。

`baseUrl` 为项目部署的基础路径，应该以 '/' 开始并且以 '/' 结束

假设你的应用将会部署在域名的根部，比如 https://www.my-app.com/，那么 `baseUrl` 应该值为 '/'

如果你的应用时部署在一个子路径下，那么你需要在这里指定子路径。比如，如果你的应用部署在 https://www.foobar.com/my-app/，那么将这个值改为 `/my-app/`

## 打包后无法运行

打包后会生成 dist 文件夹，请在本地开启一个 http 服务来运行打包后的项目。

如果您不清楚如何操作，建议您使用 [browsersync](http://www.browsersync.cn/)

![](https://qiniucdn.fairyever.com/20180821144014.png?imageMogr2/auto-orient/thumbnail/1480x/blur/1x0/quality/100|imageslim)

## 灰度模式下弹出框被遮住

如果您发现 [dialog](http://element.eleme.io/#/zh-CN/component/dialog) 在 D2Admin 灰度模式打开情况下被遮罩遮住，可以尝试在弹出框上添加 `:append-to-body="true"`，将 Dialog 自身是插入至 body 元素上。
---
title: 'Vue起项目，我做了这些事'
date: 2021-08-28 10:11:15
tags: [经验技巧]
description: Vue2起项目的时候往往要做很多基建工作，在这里我详细的将其列出。
---

![](https://gitee.com/Lainbo/image-beds/raw/master/img/vueProjectConfig.jpg)

## 技术栈
+ 就像标题上所说的，当然是vue了
+ 考虑到现在vue3的生态还不全面，所以我们选择了vue2+elemnt UI
+ 工具库使用Lodash

## 样式
+ windicss
+ scss
+ less
为什么我们使用了两个预处理器呢，因为scss在功能上更加优秀，但是我们公司的业务，需要大量用到Ant design的TreeSelect组件，Ant design需要使用到less，至于为什么使用windicss而不是tailwind的。是因为windicss拥有更优秀的变量用法，并且速度要远超tailwind

### 全局样式 目录：@/style
+ variable.scss: 全局变量管理
+ mixins.scss: 全局 Mixins 管理
+ global.scss: 全局样式
+ coverComponents.scss: 用于全局覆盖组件库的样式
+ 其中 variable.scss 和 mixins.scss 会优先于 global.css 加载，并且可以不通过 import 的方式在项目中任何位置使用这些变量和 mixins。
**注意：sass-loader>9.0.0时，不再拥有prependData这个属性，需要使用下面第2种写法**
``` js
// vue.config.js
module.exports = {
  css: {
    loaderOptions: {
      // sass混入和变量引入
      sass: {
        prependData: `
          @import "~@style/mixin.scss";
          @import "~@style/variable.scss";
          @import "~@style/coverComponents.scss";
        `
      },
      less: {
        javascriptEnabled: true,
        modifyVars: {
          // antd的主色调
          'primary-color': '#1890ff',
          'warning-color': '#faad14',
          'error-color': '#f5222d',
          'success-color': '#52c41a'
        }
      }
    }
  },
}
```
```js
loaderOptions: {
  // sass混入和变量引入
  sass: {
    additionalData: `
      @import "./src/styles/mixin.scss";
      @import "./src/styles/variable.scss";
    `
  }
}
```

### 基础css样式
``` scss
@font-face {
  font-display: swap;
  font-family: 'HarmonyOS';
  src: url('../assets/font/HarmonyOS_Sans_SC_Regular.woff2') format('woff2');
}

body {
  font-family: 'HarmonyOS', 'Microsoft YaHei', -apple-system, BlinkMacSystemFont, 'Segoe UI', 'PingFang SC', 'Hiragino Sans GB', 'Helvetica Neue', Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';
}
// 全局滚动条
::-webkit-scrollbar {
  width: 7px;
  height: 7px;
}

::-webkit-scrollbar-thumb {
  border-radius: 4px;
  box-shadow: inset 0 0 10px rgba(0, 0, 0, 0.1);
}

::-webkit-scrollbar-thumb:hover {
  background: #b6b7b9;
}
```

+ 我们在项目中全局使用了华为的鸿蒙字体，带给windows用户更好的使用体验，使用font-display: swap;让项目中，在字体没有完全加载时，先使用默认字体显示。
+ 美化了滚动条

### 静态资源加载页面
首次加载页面时，会产生大量的白屏时间，这时做一个 loading 效果看起来会很友好，其实很简单，直接在 public/index.html 里写一些静态的样式即可。

### 组件库ant design的处理
+ 通过 babel-plugin-import 使 Ant Design Vue 的组件样式可以按需加载
+ 可以按需引入，打包后的 CSS 体积取决于你引用了多少个组件
```js
// babel.config.js
module.exports = {
  presets: [
    '@vue/cli-plugin-babel/preset',
  ],
  plugins: [
    [
      'import',
      { libraryName: 'ant-design-vue', libraryDirectory: 'es', style: true },
    ],
  ],
};
```

### 干掉无用的图标
+ Ant Design Vue 把所有的 Icon 一次性引入（不管你因用了多少个组件），这使得体积打包后图标所占的体积竟然有几百 kb 之多。这些图标大多数不会被设计师所采纳，所以部分图标都应该被干掉：
+ 创建一个 icons.js 来管理 Ant Design Vue 图标，这里以一个 Loading 图标为例：
```js
// @/src/assets/icons.js
export { default as LoadingOutline } from '@ant-design/icons/lib/outline/LoadingOutline';
```

### 如何知道你要加载的图标在什么路径下？
+ 在 @ant-design/icons/lib 目录下有三种风格的图标，分别是 fill、outline、twotone，这里面内部的文件并不是 svg 格式，而是 js 和 ts 格式，这就是为什么我们可以这么引入图标的关键所在了。下一步是通过配置 vue.config.js 将这个文件引入进来：
```js
// vue.config.js
module.exports = {
  configureWebpack: {
    resolve: {
      alias: {
        // 这里是你定义的其他别名，在此省略
        '@ant-design/icons/lib/dist$': path.resolve(__dirname, './src/assets/icons.js'),
      },
    },
  },
}
```


## 构建优化
### 包分析工具
构建代码之后，到底是什么占用了这么多空间？靠直觉猜测或者使用 webpack-bundle-analyzer
```js
const WebpackBundleAnalyzer = require('webpack-bundle-analyzer');

module.exports = {
  chainWebpack: (config) => {
    if (process.env.use_analyzer) {
      config
        .plugin('webpack-bundle-analyzer')
        .use(WebpackBundleAnalyzer.BundleAnalyzerPlugin);
    }
  },
};
```

### 开启 Gzip
``` js
chainWebpack: (config) => {
  config
    .plugin('CompressionPlugin')
    .use(CompressionPlugin, []);
},
```

### 路由懒加载
这块 @vue/cli 已经帮忙处理好了，但也需要了解一下他的原理和如何配置。
```js
{
  path: 'home',
  name: 'Home',
  component: () => import(
    /* webpackChunkName: "home" */ '@/views/home/index.vue'
  ),
},

```
webpackChunkName 这条注释还是很有必要加的，至少你打包后知道又是哪个页面变得又臭又大。


### 解决 Moment 多国语
解决到这之后，Ant Design Vue 居然还很大，这是因为 moment 是 Ant Design Vue 中有强依赖该插件，所以使用 webpack 插件减小打包体积，这里我们只保留 zh-cn 语言包：
```js
// vue.config.js
module.exports = {
  chainWebpack: (config) => {
    config
      .plugin('ContextReplacementPlugin')
      .use(webpack.ContextReplacementPlugin, [/moment[/\\]locale$/, /zh-cn/]);
  },
}
```

### Lodash的Tree Shaking
要借助vue-lodash这个依赖做到
``` js
import Vue from 'vue'
import VueLodash from 'vue-lodash'
// lodash想要tree shking必须要这样使用路径单独引入
import map from 'lodash/map'
import cloneDeep from 'lodash/cloneDeep'
import sortBy from 'lodash/sortBy'

Vue.use(VueLodash, {
  name: 'lo',
  lodash: {
    map,
    cloneDeep,
    sortBy,
  }
})
```

## 项目自动格式化
网上很多格式化都是依赖于编辑器的各种配置，这里说一个不依赖于编辑器，哪怕是记事本也能自动格式化的方法，依赖于eslint-loader
1. 安装eslint-loader  npm i eslint-loader -D
2. 在vue.config.js中配置
```js
// vue.config.js
chainWebpack: config => {
  // 保存自动格式化代码
  config.module
    .rule('eslint')
    .use('eslint-loader')
    .loader('eslint-loader')
    .tap(options => {
      options.fix = true
      return options
    })
}
```
这样做以后即使是记事本，只要项目运行起来了，在保存的瞬间也可以被格式化。不过根据编辑器不同，在编辑代码界面中，代码的更新速度可能略有不同。 Vs code可能会在热更新到一半的时候发生变动，webstorm则需要数秒后或者切换一下文件再切回来才发生变动，记事本则需要关闭重新打开。当然我们更推荐使用编辑器的格式化功能，这个只作为一个兜底方案。

## 开发规范
### ESLint
不管是多人合作还是个人项目，代码规范都是很重要的。这样做不仅可以很大程度地避免基本语法错误，也保证了代码的可读性。我们使用了https://github.com/standard/standard

### Vuex
当项目越来越大是如果Vuex中的东西过多，会导致难以维护。我们将它拆分成模块化,使用自动引入的方式，进行使用。
```js
// store/index.js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

// 自动注册vuex的modules
// 只扫描modules文件夹根目录所有的js文件
const modulesFiles = require.context('./modules', false, /\.js$/)
const modules = modulesFiles.keys().reduce((modules, modulePath) => {
  const moduleName = modulePath.replace(/^\.\/(.*)\.\w+$/, '$1')
  const value = modulesFiles(modulePath)
  modules[moduleName] = value.default
  return modules
}, {})

// 下面是index的vuex，不推荐使用，能用，别用多了，可以用一点点
const store = new Vuex.Store({
  state: {},
  mutations: {},
  modules
})
export default store

```
```js
const bar = {
  state: {
    foo: ''
  },
  mutations: {
    changeFoo (state, e) {
      state.foo = e
    }
  }
}

export default bar
```

### CSS 规范
正常情况下，使用windicss进行css的编写，如果确实需要编写额外的css使用BEM规范

## 提交规范
我们使用了Husky、lint-staged、commitlint以及上面的代码自动格式化，来保证提交到仓库中的代码不会是💩
### 配置Husky
+ 首先执行安装命令 npm install husky --save-dev
+ 要在安装后自动启用钩子，我们需要执行npm set-script prepare "husky install"
+ 如果上面这一行npm set-script命令不能执行，可在package.json中手动添加，也可升级npm版本至7.x，执行npx husky-init命令，快速初始化一个husky，会看到目录下生成了一个.husky文件夹
``` js
"scripts": {
    "prepare": "husky install"
  },
```

### 配置lint-staged
执行 npm install lint-staged -D 命令, 在package.json添加如下代码(指定检查文件，详细配置可见官网)：
```
"script": {...},
"lint-staged": {
    "src/**/*.{js,jsx,ts,tsx,json,vue}": [
      "prettier --write",
      "eslint",
      "git add"
    ]
  },
```

### 利用commitlint配置commit规范
+ 执行安装命令npm install -g @commitlint/cli @commitlint/config-conventional
+ 在根目录新建commitlint.config.js文件，添加如下代码(这里@commitlint/config-conventional 提供了官方的规则扩展，更多配置见官网)：
```js
module.exports = {
  extends: ['@commitlint/config-conventional'],
  // 自定义规则, name: [0, 'always', 3], 数组第一位0为disable，1为warning，2为error
  // 第二位 应用为否 值为, always|never
  rules: {
    'type-enum': [
      2,
      'always',
      [
        'feat', // 新功能（feature）
        'fix', // 修补bug
        'docs', // 文档
        'style', // 格式（不影响代码运行的变动）
        'refactor', // 重构（即不是新增功能，也不是修改bug的代码变动）
        'test', // 增加测试
        'chore' // 构建过程或辅助工具的变动
      ]
    ]
  }
}
```

### 实现部分

1. 添加钩子，执行npx husky add .husky/pre-commit "npx lint-staged"(注意，如果使用项目使用的是npm，此处使用的是npx，而非npm,且后面内容部分一定是双引号） 执行命令后会在.husky文件下生成一个pre-commit文件，内容如下：
``` js
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npx lint-staged
```
2. 执行npx husky add .husky/commit-msg "npx commitlint -e $1",执行命令后会在.husky文件下生成一个commit-msg,内容如下：
```js
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"
npx commitlint -e $1
```
3. 执行npx husky add .husky/post-commit "git pull, git push",执行命令后会在.husky文件下生成一个post-commit,内容如下：
```js
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

git pull
git push
```
+ 生成的钩子文件里的命令可以根据自己需要随意修改。
+ 此后commit规范必须为commitlint.config.js文件中声明的前缀加冒号空格。比如 [feat: 新功能] 否则将会提交失败。
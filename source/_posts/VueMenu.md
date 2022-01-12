---
title: '在Vue中实现右键菜单'
date: 2019-03-25 20:46:53
tags: []
description: Vue中实现右键菜单的方式。
---

![](https://gitee.com/Lainbo/image-beds/raw/master/img/vueMenu.jpg)

### **使用vue-contextmenu.js，可以做到Vue实现右键菜单组件**

## 首先需要npm
``` js
npm i vue-contextmenujs
```
当然也可以从公共库进行引入，但是目前只发现UNPKG有该文件
``` HTML
<script src="https://unpkg.com/vue-contextmenujs/dist/contextmenu.umd.js">
```
## 引入
``` js
import Contextmenu from "vue-contextmenujs"
Vue.use(Contextmenu)
```
## 接着就是使用
``` HTML
<template>
  <div id="app"
       style="width:100vw;height:100vh"
       @contextmenu.prevent="onContextmenu"></div>
</template>

<script>
export default {
  methods: {
    onContextmenu (event) {
      this.$contextmenu({
        items: [
          {
            label: '返回(B)',
            onClick: () => {
              this.message = '返回(B)'
              console.log('返回(B)')
            }
          },
          { label: '前进(F)', disabled: true },
          { label: '重新加载(R)', divided: true },
          { label: '另存为(A)...' },
          { label: '打印(P)...', icon: '' }, // 需要给菜单图标可以像这样在后面写icon，值为字体图标的类名
          { label: '投射(C)...', divided: true },
          {
            label: '使用网页翻译(T)',
            divided: true,
            minWidth: 0,
            children: [{ label: '翻译成简体中文' }, { label: '翻译成繁体中文' }]
          },
          {
            label: '截取网页(R)',
            minWidth: 0,
            children: [
              {
                label: '截取可视化区域',
                onClick: () => {
                  this.message = '截取可视化区域'
                  console.log('截取可视化区域')
                }
              },
              { label: '截取全屏' }
            ]
          },
          { label: '查看网页源代码(V)' },
          { label: '检查(N)' }
        ],
        event,
        // x: event.clientX,
        // y: event.clientY,
        customClass: 'class-a',
        zIndex: 3,
        minWidth: 230
      })
      return false
    }
  }
}
</script>
```

## 参数说明
### Menu
|     属性      |         描述          | 类型         | 可选值  | 默认值  |
| :---------: | :-----------------: | ---------- | :--: | :--: |
|    items    |       菜单结构信息        | MenuItem[] |  -   |  -   |
|    event    |       鼠标事件信息        | Event      |  -   |  -   |
|      x      | 菜单显示X坐标, 存在event则失效 | number     |  -   |  0   |
|      y      | 菜单显示Y坐标, 存在event则失效 | number     |  -   |  0   |
|   zIndex    |     菜单样式z-index     | number     |  -   |  2   |
| customClass |     自定义菜单class      | string     |  -   |  -   |
|  minWidth   |       主菜单最小宽度       | number     |  -   | 150  |

<br />

### MenuItem
| 属性          | 描述                              | 类型         | 可选值  | 默认值   |
| ----------- | ------------------------------- | ---------- | ---- | ----- |
| label       | 菜单项名称                           | string     | -    | -     |
| icon        | 菜单项图标, 生成<i class="icon"></i>元素 | string     | -    | -     |
| disabled    | 是否禁用菜单项                         | boolean    | -    | false |
| divided     | 是否显示分割线                         | boolean    | -    | false |
| customClass | 自定义子菜单class                     | string     | -    | -     |
| minWidth    | 子菜单最小宽度                         | number     | -    | 150   |
| onClick     | 菜单项点击事件                         | Function() | -    | -     |
| children    | 子菜单结构信息                         | MenuItem[] | -    | -     |
<br />
效果就是这样子的

![vueMenu.png](https://i.loli.net/2020/01/05/VmDqOvfAebpM9ch.png)
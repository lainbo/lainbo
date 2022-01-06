---
title: '常用两栏布局、三栏布局写法汇总'
date: 2018-03-13 00:34:23
tags: [学习记录]
---

![](https://gitee.com/Lainbo/image-beds/raw/master/img/layout.png)

## 两栏布局（左侧固定，右侧自适应）
```html
<body>
  <div class="wrapper">
    <div class="left"></div>
    <div class="right"></div>
  </div>
</body>
```
### 浮动
```CSS
.left{
    float:left;
    width:200px;
    height:400px;
    background:black;
}

.right{
    background:red;
    height:400px;
}
```
### flex
```CSS
.wrapper{
    display:flex;
}

.left{
    width:200px;
    height:400px;
    background:black;
}

.right{
    flex:1;
    height:400px;
    background:red;
}
```
### BFC
```CSS
.left{
    width:200px;
    height:400px;
    float:left;
    background:black;
}

.right{
    overflow:hidden;
    height:400px;
    background:red;
}
```
### position绝对定位
```CSS
.wrapper{
    position:relative;
}

.left{
    width:200px;
    height:400px;
    background:black;
}

.right{
    position:absolute;
    top:0;
    left:200px;
    right:0;
    bottom:0;
    background:red;
}
```
### grid布局
```CSS
.wrapper{
    display:grid;
    width:100%;
    height:400px;
    grid-template-columns:200px auto;
}

.left{
    background:black;
}

.right{
    background:red;
}

```
### table布局
```CSS
.wrapper{
    display:table;
    width:100%;
}

.left,
.right{
    display:table-cell;
    height:400px
}

.left{
    background:black;
}

.right{
    background:red;
}
```
<br />

## 三栏布局（左右固定，中间自适应）

### 浮动
```HTML
<div class="wrapper">
  <!-- 顺序是左,右,中 -->
  <div class="left">左边</div>           
  <div class="right">右边</div>
  <div class="center">中间</div>
</div>
```
```CSS
.left{
    float:left;
    width:200px;
    height: 400px;
    background: blue;
}

.right{
    float: right;
    width: 200px;
    height: 400px;
    background: red;;
}

.center{
    height: 400px;
    background: yellow;
}
```
***

``` HTML
<div class="wrapper">
  <!-- 除了浮动，其他顺序均为左,中,右 -->
  <div class="left">左边</div>     
  <div class="center">中间</div>      
  <div class="right">右边</div>           
</div>
```
### flex
```CSS
.wrapper{
    display: flex;
}

.left,
.right{
    height: 400px;
    width: 200px;
}

.left{
    background: blue;
}

.right{
    background: red;
}

.center{
    flex:1;
    height: 400px;
    background: yellow;
}
```
### 绝对定位
```CSS
.wrapper{
    position: relative;
}

.left,.right,.center{
    position: absolute;
    height: 400px;
}

.left{
    left:0;
    background: blue;
    width: 200px;
}

.right{
    right: 0;
    background: red;
    width: 200px;
}

.center{
    left: 200px;
    right: 200px;
    background: yellow;
}
```
### table布局
```CSS
.wrapper{
    display: table;
    width: 100%;
}

.left,
.right,
.center{
    display: table-cell;
    height: 400px;
}

.left{
    background: blue;
    width: 200px;
}

.right{
    background: red;
    width: 200px;
}

.center{
    background: yellow;
}
```
### grid布局
```CSS
.wrapper{
    display: grid;
    width: 100%;
    grid-template-rows: 400px;
    grid-template-columns: 200px auto 200px;
}

.left{
    background: blue;
}

.right{
    background: red;
}

.center{
    background: yellow;
}
```
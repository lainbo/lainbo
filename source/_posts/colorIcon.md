---
title: '除了Symbol，还能这样实现多彩字体图标'
date: 2018-07-09 15:27:25
tags: []
published: true
hideInList: false
banner_img: https://lainbo.oss-cn-beijing.aliyuncs.com/Blog/colorIcon.jpg
index_img: https://lainbo.oss-cn-beijing.aliyuncs.com/Blog/colorIcon.jpg
top_img: https://lainbo.oss-cn-beijing.aliyuncs.com/Blog/colorIcon.jpg
cover: https://lainbo.oss-cn-beijing.aliyuncs.com/Blog/colorIcon.jpg
isTop: false
---
## 引
　　我们都知道，iconfont字体图标，除非使用Symbol类型，且下载一个本身就带有多种颜色的图标才可以让他在页面中以彩色的形式显示出来，不然就只能使用color属性给字体图标一个颜色，所以也有很多开发者使用svg来让图标变的丰富，但是svg如果节点太多的话会造成性能问题，接下来就是本文的主角——background-clip: text
## 第一步
+ 我们的字体图标的外面必须有一个div包裹这个图标，我们的代码结构是
``` HTML
<div class="box">
    <i class="iconfont icon-new"></i>
</div>
```
&nbsp;

## 第二步
+ 我们给这个外面的盒子设置一个多彩的颜色，设置颜色只是最后一行，其他属性不影响
``` CSS
.box {
      width: 150px;
      height: 150px;
      display: flex;
      align-items: center;
      justify-content: center;
      background-image: linear-gradient(to right, #f83600 0%, #f9d423 100%);
    }
```
&nbsp;
## 第三步
+ 将字体图标的颜色设置为透明，这样图标的颜色就不会受到color属性的影响，字体大小为其他样式，不影响这个效果的实现
``` CSS
.icon-new {
      font-size: 80px;
      color: transparent;
    }
```
&nbsp;
## 第四步
+ 给外面这个盒子加一个属性-webkit-background-clip，也就是最后一行
``` CSS
.box {
      width: 150px;
      height: 150px;
      display: flex;
      align-items: center;
      justify-content: center;
      background-image: linear-gradient(to right, #f83600 0%, #f9d423 100%);
      -webkit-background-clip: text;
    }
```
&nbsp;

于是咱们得到了一个多彩渐变的字体图标

<br />

![colorIcon-demo.png](https://i.loli.net/2020/01/04/8kwO3mH1znCvbhM.png)

　　这个方法的兼容性还是不错的，除了ie6,7,8,各大浏览器都是支持的，后面的盒子不仅可以使用渐变色，还可以使用图片，具体创意就看自己发挥了~
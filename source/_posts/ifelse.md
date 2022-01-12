---
title: 'JavaScript复杂判断的优雅写法'
date: 2019-07-10 17:26:47
tags: [经验技巧]
description: JavaScript中需要判断很多情况的时候，我们往往会使用很多if else，那么如何优雅的写大量的判断呢？
---
![](https://gitee.com/Lainbo/image-beds/raw/master/img/ifelse.jpg)

我们在编写JavaScript代码的时候经常遇见要写复杂的判断情况，通常是用多个if/else或者switch来实现多个条件判断，但是随着代码量的增加，代码块会越来越臃肿，到最后变的难以维护，现在我们来写一下如何优雅的实现复杂判断
首先先看一个例子，这是最原始的if/else
### if/else写法
``` js
/**
 * 按钮点击事件
 * @param {number} status 活动状态：1 开团进行中 2 开团失败 3 商品售罄 4 开团成功 5 系统取消
 */
const onButtonClick = (status)=>{
  if(status == 1){
    sendLog('processing')
    jumpTo('IndexPage')
  }else if(status == 2){
    sendLog('fail')
    jumpTo('FailPage')
  }else if(status == 3){
    sendLog('fail')
    jumpTo('FailPage')
  }else if(status == 4){
    sendLog('success')
    jumpTo('SuccessPage')
  }else if(status == 5){
    sendLog('cancel')
    jumpTo('CancelPage')
  }else {
    sendLog('other')
    jumpTo('Index')
  }
}
```
通过代码可以看到这个按钮的点击逻辑：根据不同活动状态做两件事情，发送日志埋点和跳转到对应页面，大家可以很轻易的提出这段代码的改写方案，switch出场：
### switch写法
``` js
/**
 * 按钮点击事件
 * @param {number} status 活动状态：1 开团进行中 2 开团失败 3 商品售罄 4 开团成功 5 系统取消
 */
const onButtonClick = (status)=>{
  switch (status){
    case 1:
      sendLog('processing')
      jumpTo('IndexPage')
      break
    case 2:
    case 3:
      sendLog('fail')
      jumpTo('FailPage')
      break  
    case 4:
      sendLog('success')
      jumpTo('SuccessPage')
      break
    case 5:
      sendLog('cancel')
      jumpTo('CancelPage')
      break
    default:
      sendLog('other')
      jumpTo('Index')
      break
  }
}
```
这样看起来比if/else清晰多了，case 2和case 3逻辑一样的时候，可以省去执行语句和break，则case 2的情况自动执行case 3的逻辑。

虽然清晰多了，但是还是有更简单的写法
### 存到Object里
``` js
const actions = {
  '1': ['processing','IndexPage'],
  '2': ['fail','FailPage'],
  '3': ['fail','FailPage'],
  '4': ['success','SuccessPage'],
  '5': ['cancel','CancelPage'],
  'default': ['other','Index'],
}
/**
 * 按钮点击事件
 * @param {number} status 活动状态：1开团进行中 2开团失败 3 商品售罄 4 开团成功 5 系统取消
 */
const onButtonClick = (status)=>{
  let action = actions[status] || actions['default'],
      logName = action[0],
      pageName = action[1]
  sendLog(logName)
  jumpTo(pageName)
}
```
上面代码确实看起来更清爽了，这种方法的聪明之处在于：将判断条件作为对象的属性名，将处理逻辑作为对象的属性值，在按钮点击的时候，通过对象属性查找的方式来进行逻辑判断，这种写法特别适合一元条件判断的情况。

这就有了另一种写法
### 存到Map里
``` js
const actions = new Map([
  [1, ['processing','IndexPage']],
  [2, ['fail','FailPage']],
  [3, ['fail','FailPage']],
  [4, ['success','SuccessPage']],
  [5, ['cancel','CancelPage']],
  ['default', ['other','Index']]
])
/**
 * 按钮点击事件
 * @param {number} status 活动状态：1 开团进行中 2 开团失败 3 商品售罄 4 开团成功 5 系统取消
 */
const onButtonClick = (status)=>{
  let action = actions.get(status) || actions.get('default')
  sendLog(action[0])
  jumpTo(action[1])
}
```
这样写用到了es6里的Map对象，是不是更爽了？Map对象和Object对象有什么区别呢？
+ 一个对象通常都有自己的原型，所以一个对象总有一个"prototype"键。
+ 一个对象的键只能是字符串或者Symbols，但一个Map的键可以是任意值。
+ 你可以通过size属性很容易地得到一个Map的键值对个数，而对象的键值对个数只能手动确认。
***
** 现在把问题升级一下，我们需要在点击按钮的时候不仅要判断status，还需要判断用户身份
``` js
/**
 * 按钮点击事件
 * @param {number} status 活动状态：1开团进行中 2开团失败 3 开团成功 4 商品售罄 5 有库存未开团
 * @param {string} identity 身份标识：guest客态 master主态
 */
const onButtonClick = (status,identity)=>{
  if(identity == 'guest'){
    if(status == 1){
      //do somethis
    }else if(status == 2){
      //do somethis
    }else if(status == 3){
      //do somethis
    }else if(status == 4){
      //do somethis
    }else if(status == 5){
      //do somethis
    }else {
      //do somethis
    }
  }else if(identity == 'master') {
    if(status == 1){
      //do somethis
    }else if(status == 2){
      //do somethis
    }else if(status == 3){
      //do somethis
    }else if(status == 4){
      //do somethis
    }else if(status == 5){
      //do somethis
    }else {
      //do somethis
    }
  }
}
```
这里已经不能写里面的逻辑代码了，太冗长了
从上面的例子我们可以看出，当逻辑升级为二元判断的时候，代码量和判断量都将加倍，那这时候应该怎么做呢？
### 将条件拼接成字符串存到Object里
``` js
const actions = new Map([
  ['guest_1', ()=>{/*do sth*/}],
  ['guest_2', ()=>{/*do sth*/}],
  ['guest_3', ()=>{/*do sth*/}],
  ['guest_4', ()=>{/*do sth*/}],
  ['guest_5', ()=>{/*do sth*/}],
  ['master_1', ()=>{/*do sth*/}],
  ['master_2', ()=>{/*do sth*/}],
  ['master_3', ()=>{/*do sth*/}],
  ['master_4', ()=>{/*do sth*/}],
  ['master_5', ()=>{/*do sth*/}],
  ['default', ()=>{/*do sth*/}],
])

/**
 * 按钮点击事件
 * @param {string} identity 身份标识：guest客态 master主态
 * @param {number} status 活动状态：1 开团进行中 2 开团失败 3 开团成功 4 商品售罄 5 有库存未开团
 */
const onButtonClick = (identity,status)=>{
  let action = actions.get(`${identity}_${status}`) || actions.get('default')
  action.call(this)
}
```
上面这段代码的核心是：把两个条件拼接成字符串，并通过以条件拼接的字符串为key，处理函数作为value的Map对象进行查找并执行，这种写法在多元条件判断的时候尤为好用
<br />
当然上面的代码也是可以使用Object来实现的

``` js
const actions = {
  'guest_1':()=>{/*do sth*/},
  'guest_2':()=>{/*do sth*/},
  //....
}

const onButtonClick = (identity,status)=>{
  let action = actions[`${identity}_${status}`] || actions['default']
  action.call(this)
}
```
<br />
当然有人还是认为把查询条件拼成字符串有点别扭，那其实还有一种方案，就是使用Map对象，以Object作为key

### 将条件存为Object存到Map里
``` js
const actions = new Map([
  [{identity:'guest',status:1},()=>{/*do sth*/}],
  [{identity:'guest',status:2},()=>{/*do sth*/}],
  //...
])

const onButtonClick = (identity,status)=>{
  let action = [...actions].filter(([key,value])=>(key.identity == identity && key.status == status))
  action.forEach(([key,value])=>value.call(this))
}
```
这里也看出来Map与Object的区别，Map可以用任何类型的数据作为key。
***
现在再把难度升级一下：假如guest情况下，status1-4的处理逻辑都一样怎么办，最差的情况是这样：
``` js
const actions = new Map([
  [{identity:'guest',status:1},()=>{/* functionA */}],
  [{identity:'guest',status:2},()=>{/* functionA */}],
  [{identity:'guest',status:3},()=>{/* functionA */}],
  [{identity:'guest',status:4},()=>{/* functionA */}],
  [{identity:'guest',status:5},()=>{/* functionB */}],
  //...
])
```
&nbsp;
好一点的写法是将处理逻辑函数进行缓存：
``` js
const actions = ()=>{
  const functionA = ()=>{/*do sth*/}
  const functionB = ()=>{/*do sth*/}
  return new Map([
    [{identity:'guest',status:1},functionA],
    [{identity:'guest',status:2},functionA],
    [{identity:'guest',status:3},functionA],
    [{identity:'guest',status:4},functionA],
    [{identity:'guest',status:5},functionB],
    //...
  ])
}

const onButtonClick = (identity,status)=>{
  let action = [...actions()].filter(([key,value])=>(key.identity == identity && key.status == status))
  action.forEach(([key,value])=>value.call(this))
}
```
&nbsp;
这样写已经能满足日常需求了，但认真一点讲，上面重写了4次functionA还是有点不爽，假如判断条件变得特别复杂，比如identity有3种状态，status有10种状态，那你需要定义30条处理逻辑，而往往这些逻辑里面很多都是相同的，这似乎也不可接受的，那可以这样实现:
### 将条件写作正则存到Map里
``` js
const actions = ()=>{
  const functionA = ()=>{/*do sth*/}
  const functionB = ()=>{/*do sth*/}
  return new Map([
    [/^guest_[1-4]$/,functionA],
    [/^guest_5$/,functionB],
    //...
  ])
}

const onButtonClick = (identity,status)=>{
  let action = [...actions()].filter(([key,value])=>(key.test(`${identity}_${status}`)))
  action.forEach(([key,value])=>value.call(this))
}
```
&nbsp;
这里Map的优势更加凸显，可以用正则类型作为key了，这样就有了无限可能，假如需求变成，凡是guest情况都要发送一个日志埋点，不同status情况也需要单独的逻辑处理，那我们可以这样写:

``` js
const actions = ()=>{
  const functionA = ()=>{/*do sth*/}
  const functionB = ()=>{/*do sth*/}
  const functionC = ()=>{/*send log*/}
  return new Map([
    [/^guest_[1-4]$/,functionA],
    [/^guest_5$/,functionB],
    [/^guest_.*$/,functionC],
    //...
  ])
}

const onButtonClick = (identity,status)=>{
  let action = [...actions()].filter(([key,value])=>(key.test(`${identity}_${status}`)))
  action.forEach(([key,value])=>value.call(this))
}

```
也就是说利用数组循环的特性，符合正则条件的逻辑都会被执行，那就可以同时执行公共逻辑和单独逻辑，因为正则的存在，你可以打开想象力解锁更多的玩法
***
### 总结
所以一共写了8种逻辑判断的方法
+ if/else
+ switch
+ 一元判断时：存到Object里
+ 一元判断时：存到Map里
+ 多元判断时：将条件拼接成字符串存到Object里
+ 多元判断时：将条件拼接成字符串存到Map里
+ 多元判断时：将条件存为Object存到Map里
+ 多元判断时：将条件写作正则存到Map里

### 补充
在平时如果只有普通的一元判断时，也可以将条件和处理函数放在数组中
``` js
const Arr = [
    {
        condition: () => a > b,
        fn: () => do something
    },
    {
        condition: () => a === b,
        fn: () => do something
    },
    {
        condition: () => a < b,
        fn: () => do something
    }
]

Arr.map(item => {
    item.condition() && item.fn()
})
``` 
这样也是一个避免大量if/else的好方法
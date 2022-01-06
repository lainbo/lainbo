---
title: '几种数组去重的方式'
date: 2018-06-27 20:47:00
tags: []
published: true
hideInList: false
banner_img: https://lainbo.oss-cn-beijing.aliyuncs.com/Blog/...set.jpg
index_img: https://lainbo.oss-cn-beijing.aliyuncs.com/Blog/...set.jpg
top_img: https://lainbo.oss-cn-beijing.aliyuncs.com/Blog/...set.jpg
cover: https://lainbo.oss-cn-beijing.aliyuncs.com/Blog/...set.jpg
isTop: false
---
## 1) 双重for循环数组去重
+ 双重for循环可以说是一个比较笨重的方法来数组去重，时间复杂度是O(n²)，如果数组很长，他的性能将会很差
+ 原理：先定义一个包含原始数组第一个元素的数组，然后遍历原始数组，将原始数组中的每个元素与新数组中的每个元素进行比对，如果不重复则添加到新数组中，最后返回新数组
``` JavaScript
function unique(arr) {
  if (!Array.isArray(arr)) {
    console.log('type error!')
    return
  }
  let res = [arr[0]]
  for (let i = 1; i < arr.length; i++) {
    let flag = true
    for (let j = 0; j < res.length; j++) {
      if (arr[i] === res[j]) {
        flag = false;
        break
      }
    }
    if (flag) {
      res.push(arr[i])
    }
  }
  return res
}
```

## 2) indexOf方法去重
+ 利用indexOf检测元素在数组中第一次出现的位置是否和元素现在的位置相等，如果不等则说明该元素是重复元素
+ 还有一种方法是利用indexOf和lastIndexOf，判断这两个是否相等，如果不相等，则该元素是重复元素，这里演示一下第一种方式
``` JavaScript
function unique(arr) {
    if (!Array.isArray(arr)) {
        console.log('type error!')
        return
    }
    return Array.prototype.filter.call(arr, function(item, index){
        return arr.indexOf(item) === index;
    });
}
```

## 3) 相邻元素去重
+ 这种方法首先调用了数组的排序方法sort()，然后根据排序后的结果进行遍历及相邻元素比对，如果相等则跳过改元素，直到遍历结束
``` JavaScript
function unique(arr) {
    if (!Array.isArray(arr)) {
        console.log('type error!')
        return
    }
    arr = arr.sort()
    let res = []
    for (let i = 0; i < arr.length; i++) {
        if (arr[i] !== arr[i-1]) {
            res.push(arr[i])
        }
    }
    return res
}
```

### 4) set与解构赋值去重
ES6中新增了数据类型set，set的一个最大的特点就是数据不重复。Set函数可以接受一个数组（或类数组对象）作为参数来初始化，利用该特性也能做到给数组去重
``` JavaScript
function unique(arr) {
    if (!Array.isArray(arr)) {
        console.log('type error!')
        return
    }
    return [...new Set(arr)] 
    //这里是使用了...解构，将Set变为一个数组，还可以使用Array.from进行转换
}
```

### 5) Lodash一把梭😏
lodash 如何实现去重
简单说下 lodash 的 uniq 方法的源码实现。
这个方法的行为和使用 Set 进行去重的结果一致。
当数组长度大于等于 200 时，会创建 Set并将 Set 转换为数组来进行去重（Set 不存在情况的实现不做分析）。当数组长度小于 200 时，会使用类似前面提到的 双重循环 的去重方案，另外还会做 NaN 的去重

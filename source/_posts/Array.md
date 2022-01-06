---
title: '数组相关的杂七杂八'
date: 2019-04-03 10:05:41
tags: [学习记录]
description: 这是和数组相关的一些知识整理。
---

![](https://gitee.com/Lainbo/image-beds/raw/master/img/newArray.jpg)

## 数组的构造器
　　数组的构造器其实要比一般写的更简单点，除了字面量的声明方法，其实可以使用Array()，你可能会说：我觉的字面量的方法也很方便呀，那么来看看下面的代码


``` JavaScript
var a = Array(8); // [undefined × 8]
// 使用对象字面量
var b = [];
b.length = 8; // [undefined × 8]
```
　　在这里看来使用Array的方法要让代码更加有辨识度，其实也各有各的好处，如果在声明的时候直接赋值那可能还是字面量的形式更加容易书写和辨认。
***
　　可能你注意到了这一点，我并没有写new Array(8)，而是写了Array(8)，实际上这并没有影响，这得益于Array构造函数内部对this的判断，在ECMA的规范中大概说了一个
>When Array is called as a function rather than as a constructor, it creates and initialises a new Array object. Thus the function call Array(…) is equivalent to the object creation expression new Array(…) with the same arguments.

大概意思是说，如果你直接用了Array()，而没有用new Array()，他也将创建一个新的Array对象，等效于new Array()

***
## ES6新增的构造函数方法
### Array.of() | Chrome 45+支持
Array.of()用于将参数依次转化为数组中的一项，不管这个参数是数字还是其他，基本上和Array一致，只不过对单个参数的情况做了处理
``` JavaScript
var a = Array.of(8)
console.log(a) //[8],而不是长度为8的数组
```
### Array.from
Array.from()可以快速的从一个类似数组的可迭代对象创建一个新的数组实例，说人话就是只要这个对象可以循环，就能用，甚至是一个字符串都可以，Array.from()可以把他变成一个数组(生成新的数组，不改变原来的对象)
>语法：Array.from(arrayLike[, processingFn[, thisArg]])

Array.from()有3个形参，第1个为类似数组的对象，第2个为加工函数，新生成的对象会经过这个函数的加工再返回，这个函数必须要return，不然会返回undefined，第3个参数为this的作用域，表示加工函数执行时this的值，后两个参数可选
``` JavaScript
var obj = {
  0: '第一条',
  1: '第二条',
  2: '第三条',
  length: 3
}
var temp = Array.from(obj)
console.log(temp) //[ '第一条', 第二条, '第三条' ]
```
　　值得注意的是，属性名必须是数字或者可以转换成数值的，比如例子中的0,1,2，且必须有length属性，实际上，如果不需要指定this，加工函数完全可以是一个箭头函数，除了上述obj对象以外，拥有迭代器的对象还包括这些：String，Set，Map，arguments
### Array.isArray() | IE 9+支持
顾名思义，这个是用来判断是不是数组的，他比instanceof更加准确一些，先来看看ES5中怎么判断一个值是否为数组
``` javaScript
var a = [];

// 1.基于instanceof
a instanceof Array;

// 2.基于constructor
a.constructor === Array;

// 3.基于Object.prototype.isPrototypeOf
Array.prototype.isPrototypeOf(a);

// 4.基于getPrototypeOf
Object.getPrototypeOf(a) === Array.prototype;

// 5.基于Object.prototype.toString
Object.prototype.toString.apply(a) === '[object Array]';
```
以上方法中，除了Object.prototype.toString外，其它方法都不能正确判断变量的类型，其他方法只要我们手动指定了__proto__为Array，这些基于继承判断数组的方法就失去了意义
+ Array.isArray()用法
``` JavaScript
Array.isArray([]); // true
Array.isArray({0: 'a', length: 1}); // false
```
实际上，通过Object.prototype.toString去判断一个值的类型，也是各大主流库的标准,只要别轻易去动这个toString()就行
***
# 数组方法
### 1）Array.push()（末尾添加元素）
+ 作用：向数组的末尾添加n个元素
+ 参数：参数时新增的元素，可以串多个
+ 返回值：新数组的数组成员的个数
+ 原数组发生变化
+ 实际上，push方法是根据length属性来决定从哪里开始插入给定的值。
``` JavaScript
let arr = [1,2,3,4,5];
	console.log(arr.push(5));   // 6
  console.log(arr); // [1,2,3,4,5,5]
  ```
+ 利用push根据length属性插入元素这个特点，可以实现数组的合并，如下：
``` JavaScript
var array = ["football", "basketball"];
var array2 = ["volleyball", "golfball"];
var i = Array.prototype.push.apply(array,array2);
console.log(array); // ["football", "basketball", "volleyball", "golfball"]
console.log(i); // 4
``` 
其实好像还是concat()性能更好点

### 2）Array.pop()（删除最后一项）
+ 作用：删除数组的最后一项
+ 参数：不需要传参数
+ 返回值：被删除的那一项
+ 原数组发生改变
``` JavaScript
let arr = [1,2,3,4,5];
	console.log(arr.pop());// 5
  console.log(arr);  //[1,2,3,4]
  ```

### 3) Array.unshift()（开始位置添加一项）
+ 作用：向数组的开始位置添加n个元素
+ 参数：参数是新增的元素，可以是多个
+ 返回值：新数组的长度
+ 原数组发生改变
``` JavaScript
let arr = [1,2,3,4,5]
	console.log(arr.unshift(2))    // 6
  console.log(arr)  //[2,1,2,3,4,5]
  ```
### 4) Array.shift()（开始位置删除一项）
+ 作用：删除数组的第一项
+ 参数：不需要传参数
+ 返回值：被删除的那一项
+ 原数组发生改变
``` JavaScript
let arr = [1,2,3,4,5]
	console.log(arr.shift())  // 1
  console.log(arr)   // [2,3,4,5]
  ```

### 5） Array.splice()（删除、添加、修改元素）
+ 作用：删除元素，并向数组添加新元素
+ 参数：
  + splice(m,n);从索引m开始，删除n项
  + splice(m);从索引m开始删除到末尾
  + splice(m,n,x,...);从索引m开始删除n项，并将x添加到原来位置；添加项可以是多个（如果删除项为0个，那么添加是在m元素前面）
+ 返回值：是个数组，数组中是被删除的项
+ 原数组发生变化

### 6） Array.indexOf()（从左向右查询）不兼容IE低版本浏览器：IE6,7,8
+ 作用：检测数组中是否存在某个元素
+ 参数：被查询的元素（m,n）从索引n开始，m第一次出现的索引位置
+ 返回值：
  + 返回数组中第一次匹配到的元素的索引
  + 如果数组中没有匹配项返回-1
+ indexOf使用严格相等（即使用 === 去匹配数组中的元素）。
+ 原数组不发生变化
``` JavaScript
let arr = [1,2,3,4,5,2]
	let arr1 = arr.indexOf(2)
	console.log(arr1)  // 1
	let arr2 = arr.indexOf(9)
	console.log(arr2)  // -1
```

### 7） Array.lastIndexOf()（从左到右查询）不兼容IE低版本浏览器：IE6,7,8
+ 作用：检测数组中是否存在某个元素
+ 参数：被检测的元素
+ 返回值：
  + 返回数组中最后一次匹配到的元素的索引
  + 如果数组中没有匹配项返回-1
+ lastIndexOf使用严格相等（即使用 === 去匹配数组中的元素）。
+ 原数组不发生变化
``` JavaScript
let arr = [1,2,3,4,5,2]
	let arr1 = arr.lastIndexOf(2)
	console.log(arr1)  // 5
	let arr2 = arr.lastIndexOf(9)
	console.log(arr2)  // -1
```

### 8） Array.slice()（截取）
+ 作用：按照起始和结束位置的索引截取数组
+ 参数：
  + 有两个参数slice(m,n)：从索引m开始，截取到索引n；（包前不包后）
  + 有一个参数slice(m)：从索引m开始截取到末尾；
没有参数：数组的克隆；(slice(0)也是数组的克隆)；
以上情况参数都支持负数，负数情况会默认被加上数组的长度，处理成正数
+ 返回值：截取的数组
+ slice方法参数为空时，同concat方法一样，都是浅复制生成一个新数组。
+ 原数组不发生变化
+ 同时，稍微利用下 slice 方法第一个参数为负数时的特性，我们可以非常方便的拿到数组的最后一项元素
``` JavaScript
console.log([1,2,3].slice(-1));//[3]
```
+ 用法
``` JavaScript
let arr = [1,2,3,4,5]
	console.log(arr.slice(1,3))   // [2,3]
  console.log(arr)    //  [1,2,3,4,5]
  ```

### 9) Array.sort()（排序）
+ 作用：对数组的元素进行排序
+ 参数：

  + 没有参数sort()：只能排序数组成员项是相同位数的数字
  + sort(function(a,b){return a-b})：从小到大排序
  + sort(function(a,b){return b-a})：从大到小排序
+ 返回值：排序之后的数组
+ 如果传进来的是汉字，则会按照字符串的Unicode(万国码)位点顺序排序
+ 原数组发生变化
``` JavaScript
 var ary=[1,3,10,50,2,120,30];
 ary.sort(function fn(a,b){
           return a-b;//这里是一个回调函数，把函数当作实参传给sort的方法
       })
       console.log(ary); //这里是升序，如果b-a则为降序
```

### 10) Array.reverse()（倒序）
+ 作用：使数组中元素倒序
+ 参数：不需要参数
+ 返回值：成员倒序的数组
+ 原数组发生变化
``` JavaScript
let arr = [1,2,3,4,5]
	console.log(arr.reverse())    // [5,4,3,2,1]
  console.log(arr)    // [5,4,3,2,1]
  ```

### 11) Array.concat()（拼接数组）
+ 作用：拼接两个或多个数组
+ 参数：
  + 不传参数：数组的克隆
  + 传参数：将传入的参数拼接到数组中、可以传多个
+ 返回值：拼接之后的新数组
+ 原数组不发生变化
``` JavaScript
let arr = [1,2,3,4,5]
	console.log(arr.concat([1,2]))  // [1,2,3,4,5,1,2]
  console.log(arr)   // [1,2,3,4,5]
  ```
### 12) Array.join()（数组拼接成字符串）
+ 作用：将数组的成员项通过制定字符拼接成字符串 参数：
  + 不传参数：会默认按照逗号拼接
  + 传参数：会按照参数字符拼接
+ 返回值：拼接之后的字符串
+ 原数组不发生变化
``` JavaScript
var arr = [1,2]
	var result = arr.join();
  console.log(result);//'1,2'
  ```




### 13) Array.flat()(拉平数组)
+ 作用：用于将嵌套的数组“拉平”，变成一维数组
+ 返回值：返回一个新数组;
+ 参数：有参数，默认是一层，如果有多层，就需要传层数；如果不管有多少层嵌套，都要转成一维数组，可以用Infinity关键字作为参数。
+ 原数组不改变

+ 第一种
``` JavaScript
let arr = [12,3,[1,2,3],6,78];
    let ary=arr.flat()
    console.log(ary);//[12,3,[1,2,3],6,78]
    console.log(arr)// [12, 3, 1, 2, 3, 6, 78]
```
+ 第二种
``` JavaScript
    var arr = [ [1, 2, 2], [3, 4, 5, 5], [6, 7, 8, 9, [11, 12, [12, 13, [14] ] ] ], 10];
consolelog(arr.flat(Infinity))//[1, 2, 2, 3, 4, 5, 5, 6, 7, 8, 9, 11, 12, 12, 13, 14, 10]
```
***
## 数组的遍历方法

### 1) Array.map()（映射）
+ 作用：方法返回一个新数组，数组中的元素为原始数组元素调用函数处理后的值；
+ 参数：参数是一个回调函数；数组有几项，回调函数就执行多少次；在回调函数中处理数组中的每项；
+ 返回值：映射的新数组；
+ 原数组不发生变化；
+ 示例：var ary = [1,2,3]; ary.map(function(item,index,arr){});

  + item是遍历数组的每一项
  + index是遍历数组的索引
  + arr就是原数组
``` JavaScript
let ary = [1,2,3,4,5]
	ary.map( (item,index,arr)=>{
	        item= item* 2
	        console.log(`item:${item}    index:${index}     arr:${arr}`)
	})   
	console.log(ary)
```

### 2） Array.forEach()（遍历数组）
+ 作用：遍历数组；
+ 参数：参数是一个回调函数；数组有几项，回调函数就执行多少+ 次；
+ 返回值：没有返回值；undefined；
+ 原数组不发生变化；
+ 示例：var ary = [1,2,3]; ary.forEach(function(item,index,arr){});

  + item是遍历数组的每一项
  + index是遍历数组的索引
  + arr就是原数组
``` JavaScript
	let ary = [1,2,3,4,5]
	ary.forEach( (item,index,arr)=>{
        console.log(`item:${item}    index:${index}     arr:${arr}`)
    })   
    //  item:1    index:0     arr:1,2,3,4,5
    //  item:2    index:1     arr:1,2,3,4,5
    //  item:3    index:2     arr:1,2,3,4,5
    //  item:4    index:3     arr:1,2,3,4,5
    //  item:5    index:4     arr:1,2,3,4,5

	let ary = [1,2,3,4,5]
	ary.forEach( (item,index,arr)=>{
       item= item* 2
       console.log(`item:${item}    index:${index}     arr:${arr}`)
    })   
    console.log(ary)
    // item:2    index:0     arr:1,2,3,4,5
    // item:4    index:1     arr:1,2,3,4,5
    // item:6    index:2     arr:1,2,3,4,5
    // item:8    index:3     arr:1,2,3,4,5
    // item:10   index:4     arr:1,2,3,4,5
    // [1, 2, 3, 4, 5]
```
>ary.forEach()和ary.map()的区别
1、 ary.forEach()是和for循环一样，是代替for。ary.map()是修改数组其中的数据，并返回新的数据。
2、ary.forEach() 没有return  ary.map() 有return。
3、map有映射，forEach没有映射。

### 3) Array.find&findIndex(查找)
+ 从左到右依次进行查找，找到符合条件的那一项，直接返回，不再进行查找；如果找不到，那么返回undefined； 返回true,说明就找到了；
+ find会根据回调函数的返回值，判断是否要继续向右查找；
``` JavaScript
let arr = [12,3,45,6,78];
	let  a =  arr.find((item,index)=>{
        console.log(100);//输出三次
        return item>20;
    })
    console.log(a);//45
```
>find() 方法基于ECMAScript 2015（ES6）规范，返回数组中第一个满足条件的元素（如果有的话）， 如果没有，则返回undefined。

>findIndex() 方法也基于ECMAScript 2015（ES6）规范，它返回数组中第一个满足条件的元素的索引（如果有的话）否则返回-1。


### 4) Array.filter(过滤)
+ 过滤;原数组不发生改变；返回一个过滤后的新数组
``` JavaScript
let arr = [12,3,45,6,78];
	let ary = arr.filter((item,index)=>{
        // 根据当前函数的返回值是true来获取这一项；如果是false，返回值中就没有这一项；
        return item>30;
    });
    console.log(arr);//[12, 3, 45, 6, 78]
    console.log(ary);//[45, 78]
```

### 5) Array.every(测试所有元素)
+ 每一个都是true则返回true，如果有一个是false，那么直接返回false；只要找到false，直接结束，不再继续向下查找；返回值是布尔值
``` JavaScript
let arr = [12,3,45,6,78];
		let e = arr.every((item,index)=>{
        	return item>13;
	    })
      console.log(e);//false
 ```

### 6) Array.includes()(包含)
+ 作用：用来判断当前数组是否包含某个指定的值
+ 参数：
  + 有两个参数:第一个参数表示需要查找的元素，第二个表示从该索引位置开始查找
  + 有一个参数：则自定义索引的参数缺省的情况下默认为0 ，从数组开始查找
+ 返回值：返回一个布尔值；如果有就返回true；，没有就返回false；
+ 原数组不会发生变化
``` JavaScript
let  arr = [12,3,45,6,78];
  console.log(arr.includes(45));//true
  ```

### 7） Array.reduce(累加器)
+ 作用：迭代数组的所有项，累加器，数组中的每个值（从左到右）合并，最终计算为一个值
+ 参数：接受两个参数：一个是对数组每个元素执行的回调方法，一个是初始值
``` JavaScript
let arr = [12,3,45,6,78];
	let  a = arr.reduce((prev,next)=>{
        // prev : 第一次代表数组的第一项，以后都代表上一次回调函数的返回值；
        console.log(prev,next);//第一次输出：112 3，第二次输出：115 45，第三次输出：160 6，第四次输出：166 78
        return prev +next;
    });
    console.log(a);//244
```
+ reduce回调函数后面可以传一个参数
``` JavaScript
let arr = [12,3,45,6,78];
	let  a = arr.reduce((prev,next)=>{
        console.log(prev,next);//第一次输出：100 12，第二次输出：100 3，第三次输出：100 45，第四次输出：100 6，第五次输出：100 78
        return 100;
    },100);
    console.log(a);//100
```
### 8) Array.some()
+ 返回一个布尔值；只要有一个符合条件就返回true；找到true，就不再向右进行查找；
+ some() 方法刚好同 every() 方法相反，some 测试数组元素时，只要有一个函数返回值为 true，则该方法返回 true，若全部返回 false，则该方法返回 false
``` JavaScript
let arr = [12,3,45,6,78];
	let a  = arr.some((item,index)=>{
        console.log(100);
        return item>10;
    })
    console.log(a);//true
```

### 9) Array.keys()
+ 返回一个数组索引的迭代器
``` JavaScript
var array = ["abc", "xyz"];
var iterator = array.keys();
console.log(iterator.next()); // Object {value: 0, done: false}
console.log(iterator.next()); // Object {value: 1, done: false}
console.log(iterator.next()); // Object {value: undefined, done: false}
```
+ 我们可以利用Array.keys()轻松的创建一个从0开始的数组，上面我们说了new Array 和 Array 对单个数字的处理相同
``` JavaScript
[...Array(10).keys()]; // [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
[...new Array(10).keys()]; // [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```


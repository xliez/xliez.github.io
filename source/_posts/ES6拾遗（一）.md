---
title: ES6拾遗（一）
date: 2017-05-09 22:11:08
tags:
- javascript
catalogies:
- 学习笔记
---
# 前言

准备将ES6中一些新的，但是平时还没有有意识的去使用的一些语法糖或新特性再拎出来一下，新增的api和重点特性就不谈了。

[阮一峰的ES6入门手册](http://es6.ruanyifeng.com/)

<!--more-->

# rest函数与拓展运算符

## rest函数

rest函数是个很酷的特性（虽然我好像一次都没用过）。

众所周知，js不像Java或C++一样规范函数定义的参数，也不存在重载；js其实是不在意函数参数的，定义函数时就算没有参数，使用时依然可以传参数进去（传入arguments）。

此语法糖就是用`...变量名`来将多的参数放入到以此变量名命名的数组中去。

**当你需要这样一个函数：一个确定的参数和不确定数量的其余变量；不要多想，用rest函数吧！**

```js
function f(x, ...rest) {
    for (let i of rest) {
        //......
    }
}
```

## 拓展运算符

拓展运算符就和rest函数符号相同，但意义相反，rest函数将参数变成数组，那么拓展运算符就是把数组变成独立的参数。

它的主要用处有：（具体请看阮一峰的入门手册）

- 替代数组的apply方法
- 合并数组
- 解构赋值
- 作为函数参数

**总结一下，当你有一个数组，并且你想要利用数组中的值时，想想能不能用拓展运算符吧**

# 对象与属性


关于属性的比较多，首先将`属性的简洁表示法`、`属性名表达式`和`方法的name属性`放在一起讲下。

```js
//之前的用法

let f = 'fuck'

let obj = {
    a : f,
    b : function (){},
    fuck: 'shit'
}

obj.a
obj.b()
obj.fuck

//时髦的用法

let obj = {
    f, //属性的简洁表示法
    b(){}, //方法的name属性 + 属性的简洁表示法
    ['f'+'u'+'c'+'k']: 'shit' //属性名表达式
}

obj.f
obj.b()
obj.fuck

//甚至还可以这样
function shit() {
    return 'fuck'
}

obj[shit()] //其实我不确定这个用法是不是新特性

```

## Symbol

然后是Symbol用法。Symbol是一种新原始数据结构，前六种是：undefined、 null、 Boolean、String、 Number、 Object。

Symbol是为了方式属性名的冲突而实现的保证属性名字独一无二的机制。*虽然我至今没用过*

看看最常见的用法

```js
let s = Symbol('s')

let obj = {}

obj[s] = 'shit'

//or

let obj = {
    [s] : 'shit'
}

obj[s] //注意不能用点运算符
```

# To be continued
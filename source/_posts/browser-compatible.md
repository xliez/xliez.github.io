---
title: 浏览器兼容
date: 2017-03-23 20:16:53
tags:
- js
categories:
- 学习笔记
---

## 前言

前一篇博文里单独分出来的一章，记录浏览器兼容处理这个大坑的知识，以后若是学习到新的相关知识，也会在此文更新。

<!-- more -->

## js事件兼容

> 以下来自js高程三

### 添加事件、移除事件

chrome
```js
element.addEventListener('event', handleEvent);
element.removeEventListenr('event', handleEvent);

function handleEvent(e){
    console.log(this);
}

//this指向事件目标元素
```
IE
```js
element.attachEvent('onevent', handleEvent);
element.detachEvent('onevent', handleEvent);

function handleEvent(e){
    console.log(this);
}
//this指向window
```

跨浏览器

```js
var EventUtil = {
    addHandler: function(element, event, handler){
        if (element.addEventListener){
            element.addEventListener(event, handler)
        } else if (element.attachEvent){
            element.attachEvent('on'+event, handler)
        } else {
            element['on'+ event] = handler;
        }
    },
    removeHandler: function(element, type, handler){
        if (element.removeEventListener){
            element.removeEventListener(event, handler)
        } else if (element.detachEvent){
            element.detachEvent('on'+ event, handler)
        } else {
            element['on' + event] = null;
        }
    }
}
```

### 事件对象

chrome:
```js
element.onclick = function(e){
    console.log(e.type) // 'click'
}
```

所有事件属性/方法:

属性/方法 | 类型 | 读/写 | 说明
:--: | :--: | :--: | :--:
bubbles | Boolean | 只读 | 表明事件是否冒泡
cancelable | Boolean | 只读 | 是否可以取消事件默认行为
currentTarget | Element | 只读 | 当前正在处理事件的元素
defaultPrevented | Boolean | 只读 |是否已经调用preventDefault()
detail | Integer | 只读 | 事件相关细节
eventPhase | Integer | 只读 | 事件处理阶段:1=捕获2=处于目标3=冒泡阶段
preventDefault() | Function | 只读 | 取消默认行为
stopImmediatePropagation() | Function | 只读 | 取消事件进一步捕获或冒泡
stopPropagation() | Function | 只读  | 取消事件进一步捕获或冒泡
target | Element | 只读 | 事件目标
trusted | Boolean | 只读 |为true表示事件是浏览器生成,false表示事件是开发人员通过js创建
type | String | 只读 | 事件类型

IE:

属性/方法 | 类型 | 读/写 | 说明
:--: | :--: | :--: | :--:
cancelBubble | Boolean | 读/写 | 默认false,设置为true取消冒泡
returnValue | Boolean | 读/写 | 默认true,设置false取消事件默认行为
srcElement | Element |  只读 | 事件目标
type | String | 只读 | 触发事件类型

这里兼容性主要就是事件目标与取消默认行为

```js
var EventUtil = {
    getEvent: function(event){
        return event ? event: window.event
    },
    getTarget: function(event){
        return  event.target || event.srcElement
    },
    preventDefault:function(event){
        if (event.preventDefault){
            event.preventDefault()
        } else{
            event.returnValue = false
        }
    },
    stopPropagation: function(event){
        if (event.stopPropagation){
            event.stopPropagation()
        } else {
            event.cancelBubble = true;
        }
    }
}
```

---

## 更新：2017-3-28

> 知乎上看到的，有待实践

###一些关于跨浏览器/设备的工具

1. modernizr.js 特性检测器，有就使用原生，没有就加载polyfill
2. polyfill/shim 向后兼容的浏览器的js补丁，一般和modernizr一起用
3. jshint.js js语法检测器
4. Boilerplate 开发的最佳实践的初始模板
5. 阅读第三方库关于最低版本支持
6. 使用js单元测试，测试目标浏览器
7. Responsive Design （针对屏幕大小）
8. normalize.css 统一浏览器基本元素的风格

作者：caoglish
链接：https://www.zhihu.com/question/19736007/answer/29275630
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
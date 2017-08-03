---
title: 阿里巴巴前端测试
date: 2017-03-23 14:11:53
tags:
- js
categories:
- 学习笔记
---

## 前言

参加了阿里巴巴实习生招聘……原地爆炸了。

<!-- more -->

## 题目

如下两个div，让两个div随着鼠标滚轮滚动，第一个div以滚轮两倍滚动，第二个div以滚轮三倍滚动。

```html
<div id='wrap'>
  <div id='box1'></div>
  <div id='box2'></div>
</div>

<style>
#wrap{
  height: 5000px;
  width: 100%;
}
#box1{
  height: 200px;
  width: 300px;
  background-color: red;
}
#box2{
  height: 300px;
  width: 300px;
  background-color: blue;
}
</style>
```

看到题目我有点崩溃，光滚动就难住我了，根本不知道滚动时间的api，此外我的鼠标滚轮正好是坏的……

虽然思路是有的，但是脑子很乱，落实不到具体的代码。

## 事后

事后理了下思路。

### 实现滚动。

**mousewheel**能触发滚轮滚动事件，event则包含了滚动事件的信息。其中wheelDelta属性则包含了滚动距离，当用户向前滚动时，wheelDelta是120的倍数，像后滚动时，wheelDelta是-120的倍数。

最基本的功能实现：

```js
function $(id){
    return document.getElementById(id);
}

document.addEventListener('mousewheel', function(e){
    let w = e.wheelDeltaY;
    $('box1').style.transform = 'translateY('+ w*(-2) + 'px)';
    $('box2').style.transform = 'translateY(' +w*(-3) + 'px)';
})
```

以上就是我写的答案，用jsfiddle测试，因为鼠标滚轮的原因，不知道具体体验如何。

接下来就是改进。

###　实现兼容性

兼容性这块因为自娱自乐以前一直不怎么注意。

#### 添加事件、移除事件

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

#### 事件对象

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

处理了兼容后的代码:
```js
function addEvent(element, event, handler){
    if (element.addEventListener){
        element.addEventListener(event, handler)
    } else if (element.attachEvent){
        element.attachEvent('on'+event, handler)
    } else {
        element['on'+event] = handler;
    }
}

function $(id){
    return document.getElementById(id);
}

addEvent(document, mousewheel, function(e){
    let w = e.wheelDeltaY;
    $('box1').style.transform = 'translateY('+ w*(-2) + 'px)';
    $('box2').style.transform = 'translateY(' +w*(-3) + 'px)';
});
```

### 越界处理

只是以上的代码仍然有bug，那就是当到顶或底后，块仍然会移动，而不会停靠，因此要处理越界。

- box1的界为top:0～(5000-200-300)
- box2的界为top:200～(5000-300)

```js
function addEvent(element, event, handler){
    if (element.addEventListener){
        element.addEventListener(event, handler)
    } else if (element.attachEvent){
        element.attachEvent('on'+event, handler)
    } else {
        element['on'+event] = handler;
    }
}

function $(id){
    return document.getElementById(id);
}

var box1 = $('box1')
var box2 = $('box2')
var wrap = $('wrap')

addEvent(document, mousewheel, function(e){
    if(e.wheelDe)
    let w = e.wheelDeltaY;

    if (box1.offsetTop - wrap.offsetTop < 0){
        box1.style.transform = 'translateY(0)';
    } else if (box1.offsetTop - wrap.offsetTop > 4500){
        box1.style.transform = 'translateY(4500px)';
    } else {
    box1.style.transform = 'translateY('+ w*(-2) + 'px)';        
    }

    if (box2.offsetTop - wrap.offsetTop < 200){
        box2.style.transform = 'translateY(0)';
    } else if (box2.offsetTop - wrap.offsetTop > 4700){
        box2.style.transform = 'translateY(4700px)';
    } else {
        box2.style.transform = 'translateY(' +w*(-3) + 'px)';
    }

});

```

*有一点存疑，那就是鼠标的滚动是否以px单位计算？等我的新鼠标到了再测试下。。。*

基本上就差不多了吧，最后还可以在加上些**特效**

```js
box1.style.transition = 'all 0.8s ease';
box2.style.transition = 'all 0.8s ease';
```

## 更新

对于mousewheel这个事件，其实仍然存在浏览器兼容性问题。
推介一篇[文章](http://www.planabc.net/2010/08/12/mousewheel_event_in_javascript/)
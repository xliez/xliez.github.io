---
title: js常见排序算法
date: 2017-08-07 23:31:36
tags:
- js
catalogies:
- 面试
---

```js
function swap (arr, i, j) {
    var temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;
}


//bubble sort
//时间复杂度： 平均n2 最好n 最差n2 空间复杂度1

function bubble(arr) {
    for (var i = arr.length - 1; i >= 0; i--) {
        var flag = false;
        for (var j = 0; j < i; j++) {
            if (arr[j+1] < arr[j] ) {
                flag = true;
                var temp = arr[j+1];
                arr[j+1] = arr[j];
                arr[j] = temp;
            }
        }
        if (!flag) {
            break;
        }
    }
}

//select sort
//时间复杂度 n2 最好n2 最差n2 空间复杂度1

function select(arr) {

    for (var i = 0; i < arr.length; i++) {
        var min = arr[i];            
        var index = i;
        for (var j = i; j < arr.length; j++) {
            if (arr[j] < min) {
                min = arr[j];
                index = j;
            }
        }
        swap(arr, i, index);
    }
}

//insert sort
//时间复杂度 平均n2 最好n 最差n2 空间复杂度 1

function insert(arr) {

    for(var i = 1; i < arr.length; i++) {
        var temp = arr[i]
        for (var j = i; j > 0 && temp < arr[j-1]; j--) {
            arr[j] = arr[j-1];
        }
        arr[j] = temp;
    }
}


//merge sort
//时间复杂度 平均nlogn 最差nlogn 最好nlogn 空间复杂度 n

function mergeSort(arr) {
    
    var len = arr.length;
    if (len === 1)
        return arr;

    var mid = len >> 1;
    var l = arr.slice(0, mid);
    var r = arr.slice(mid);
    return merge(mergeSort(l), mergeSort(r));
}

function merge(left, right) {
    var temp = [];
    while (left.length && right.length) {
        var min = left[0] <= right[0] ? left.shift() : right.shift();
        temp.push(min);
    }
    return temp.concat(left, right);
}

//quick sort
//时间复杂度 平均nlogn 最好nlogn 最差n2 空间复杂度nlogn
function quick(arr,left, right) {

    if (left < right) {
        var partIndex = part(arr, left, right);
        quick(arr, left, partIndex -1);
        quick(arr, partIndex + 1, right);
    }
    return arr;
}

function part(arr, left, right) {
    for (var i = left + 1, j = left; i <= right; i++) {
        if (arr[i] < arr[left])
            swap(arr, ++j, i);
    }
    swap(arr, left, j);
    return j;
}

var arr = [2, 5, 6, 1, 4, 6];
arr = quick(arr, 0, arr.length-1)
console.log(arr)

```

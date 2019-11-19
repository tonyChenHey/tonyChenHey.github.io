---
title: ES6项目学习笔记（二）let和const 变量声明的扩展
date: 2019-11-18 16:13:46
tags:
---
let，const和var一样，是对变量的一种声明方式，都可以用来声明变量。
先说说let，用来**声明仅可在块状作用域中使用的变量**。直接上代码说：
![var声明的变量不受块状作用域限制](https://upload-images.jianshu.io/upload_images/5106777-749435c595b5b095.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
所以，不在块状作用域用，let就没什么意义。
另外,同一作用域内,不能用let反复声明同一个变量,会报错的
![](https://upload-images.jianshu.io/upload_images/5106777-4a36eaaba24ca8ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
用*const*用来声明创建一个常量,除了同一作用域下不能反复声明,还不能重新赋值,这里的值指的是变量引用的内存地址,在引用内容是对象的情况下，可以改变对象里面的内容,看代码就清楚了:
![](https://upload-images.jianshu.io/upload_images/5106777-bf8bb8d7ba540abb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
以上，有其它的再讨论补充。
---
title: ES6项目学习笔记（一）作用域 定义的补充完善
date: 2019-11-18 16:13:20
tags:
---
作用域是什么？
抱着这个疑问，咱们不妨先看看下面的一段代码
```
var name = 'tony';
function hello(age){
	console.log(name);
	console.log(age);
	console.log(fullName);
}
hello();
//tony
//undefined
//fullName is not defined
```
![实际运行结果](https://upload-images.jianshu.io/upload_images/5106777-26d49a73643e41b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
如果不了解作用域的看到代码可能会和我一样疑惑，为什么fullname“no defined”而age是"undefined"，有什么区别，以及为啥是"no defined"？
有了这样的疑问我们再来看看我们所看到的一些解释：
![作用域解释1](https://upload-images.jianshu.io/upload_images/5106777-482ac5610c5afe2b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![作用域解释2](https://upload-images.jianshu.io/upload_images/5106777-9fe6c719aba92a08.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
从这个两个解释里面我们可以提取出来两个要点，**规则** **集合**

规则其实不难理解，就如同我们人在社会中一样，受到各种规则限制，而在js中处处存在的变量，也同样受到一套规则所限，同样有“生老病死”（变量生命周期），同样有自己的“人生轨迹”（作用域链）。
在js中变量所受的作用域规则主要有了 **全局作用域**，**局部作用域**，**块状作用域**，**动态作用域**，

这些作用域在上面的代码就有体现，我们可以先从我们最容易看懂的name变量入手，变量name是在整个代码中的最外层的,即在**函数外**,**代码块``{}``外**(es6新增)的,这就是全局作用域.
我们说作用域是变量在整个js代码中运行的规则，这时候name在全局作用域的规则下就是：**网页中所有脚本和函数均可使用**，
![全局作用域声明的变量可以在函数内部修改](https://upload-images.jianshu.io/upload_images/5106777-b9b9a3344fec8a1c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
我们再把这段代码修改一下，就可以通过对比加深对全局作用域的理解，同时知道什么是函数作用域了
![全局作用域的变量和函数作用域的变量](https://upload-images.jianshu.io/upload_images/5106777-9fd270f73dd404b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
函数内部定义的变量age，**在函数内部定义**的，就是局部作用域，很明显，它的规则就是**只能在函数内部访问**，你看在其它函数内修改也改变不了内部27的事实。在修改age的这个函数内，涉及到了js的变量提升编译词法等（[看这里](https://www.runoob.com/js/js-hoisting.html)），你在函数外打印age这个变量是可以得到20的，这更加说明了作用域的存在，超出了作用域，即便名字相同，也是不同的变量。这也是由于不同作用域可访问的变量不同导致的，可以再看一眼作用域的解释：
![作用域为可访问对象，变量，函数的集合](https://upload-images.jianshu.io/upload_images/5106777-2379c3518739236a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在函数内部，存在的可访问变量有name，age，在函数内部查找不到name这个变量时，会找到上一层全局作用域中，而查找age时，在局部作用域中就找到了，所以不会再在全局作用域中进行查找。
作为局部作用域定义的变量可以很好的受到保护，不在外部就被随意更改，但是比如年龄这个东西，总不能每年询问的时候总是那个岁数吧
![局部作用域的变量每次访问都被重置](https://upload-images.jianshu.io/upload_images/5106777-5e7d373f0a1fc8c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
所以我们需要每次访问的时候让函数内部自己+1。类似这样
![](https://upload-images.jianshu.io/upload_images/5106777-572053c97e185d26.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
plus函数就包含了它上一层的作用域中的变量age，但由于plus函数也是局部作用域中，所以每次访问也是同样的值，如果能够将plus这个操作，做成类似全局作用域，那么每次执行就不会是重复的值了
![闭包实际就是将局部作用域的集合调到全局作用域中，使局部作用域中的变量不被销毁](https://upload-images.jianshu.io/upload_images/5106777-5a8252d93a79bb09.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
闭包是解决这个问题的一个方法，用立即执行函数返回的一个函数，成为了全局作用域下的一个变量集合，集合也可以是函数噢，所以，这个函数内部使用的变量，虽然是在局部作用域下的，但是却不会被销毁。

这两个作用域基本上就是es5中经常使用的了，在es6中，新增了块状作用域， 起初我以为这个作用域的规则是针对所有变量的，但经过试验，这个规则主要配合`let`声明的变量使用，用来解决以前es5中一些场景下变量使用的作用域模糊不清的问题，比如常见的这种
```
for(var i = 0;i < 3;i++){}
console.log(i)  //3
```
如果换成let来声明i变量，就会报错`ReferenceError`，另外还有一种变量声明const，同样受此规则限制，所以，用let，const声明的变量仅在当前`{}`中可以访问。

大概就说这些作用域了，总结：**作用域是变量在一段代码中的访问规则，是可访问变量的集合**。
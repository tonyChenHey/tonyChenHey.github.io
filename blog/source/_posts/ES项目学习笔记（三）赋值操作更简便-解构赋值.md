---
title: ES项目学习笔记（三）赋值操作更简便 解构赋值
date: 2019-11-18 16:16:55
tags:
---
用一个经常遇到的问题来说明这一赋值的好处之一，**对两个变量的值进行替换**，通常我们得另外声明一个变量来作为两个变量交换的媒介，而现在如下代码就可以解决：
```
{
  let a=1;
  let b=2;
  [a,b]=[b,a];
  console.log(a,b);  //2,1
}
```
解构赋值上述的用法是对**数组的解构赋值**，总的来说结构赋值大致可以分为**数组解构赋值**，**对象解构赋值**，**字符解构赋值**，**数值和布尔解构赋值**，**函数参数解构赋值**。

无论何种解构赋值，解构赋值表达式的左边部分，即**解构的目标**，而右边的值，即左边**解构的源**，都遵循一个左右互相匹配的规则，只要等号两边的模式相同，左边的变量就会被赋予对应的值。

#### 数组解构赋值
上述的代码就是数组解构赋值的**基本用法**。以下是数组解构赋值的其它使用方式
```
//嵌套
{
  let [a, [[b], c]] = [1, [[2], 3]];
  console.log(a,b,c); //1,2,3
}
//可忽略
{
  let [a, , b] = [1, 2, 3];
  console.log(a,b);
}
//剩余运算符
{
  let a,b,rest;
  [a,b,...rest] = [1,2,3,4,5,6];
  console.log(a,b,rest); //1,2,[4,5,6]
}
//不完全解构
{
	let [a,b] = [1,2,3];
}
```
上述的变量全都可以成功赋值，也就是成功解构，但也有无法解构成功的，比如：
```
let [a] = [];
let [b, d,...c] = [1];
console.log(a);  //undefined
console.log(b,d,c); //1,undefined,[]
```
当解构目标在解构的源对应位置找不到值,则无法成功解构,变量的值就会变成`undefined`,...剩余运算符的变量为`[]`.
在实际开发场景中返回的值可能有多种情况,可能你想要的值在某个位置确实没有,那么还可以使用**默认值**。
```
let [a,b = 1] = [1];
console.log(a,b); //1,1
let [c = 1,d = 2] = [undefined,null];
console.log(c,d); //1,null
```
*ES6 内部使用严格相等运算符（===），判断一个位置是否有值。只有当一个数组成员严格等于`undefined`，默认值才会生效*
默认值可以引用解构赋值的其他变量，但该变量必须已经声明。
```
let [a = 1, b = a] = [];     // a=1; b=1
let [a = 1, b = a] = [2];    // a=2; b=2
let [a = 1, b = a] = [1, 2]; // a=1; b=2
let [a = b, b = 1] = [];     // ReferenceError: b is not defined
```
如果解构的源不是数组，就会报错
```
let [foo] = 1;
let [foo] = false;
let [foo] = NaN;
let [foo] = undefined;
let [foo] = null;
let [foo] = {};
```
#### 对象解构赋值
对象解构赋值同样遵循左右互相匹配的规则，类似数组一样有嵌套，默认值等，但要注意的是**变量名称需要和对象中的属性值要对应，因为数组的位置相对应来说是有序的，而对象中是无序的，只能通过属性来作为位置查找。**如果要使用其它的变量名称，可以使用`:`重新指定变量名称，注意此时引用地址已被新变量接收,`:`左边的变量就没有地址了，再访问的话是空的，是真的输出为空的字符，在下面示例代码中有对应的写法：
```
//基本
{
  let {a,b} = {a:1,b:2}
  console.log(a,b);
  let {name:newName} = {name:'tony'}
  console.log(name)//''
  console.log(newName)//tony
}
//默认值
{
  let {x = 3} = {};
  let {a,b = 2}={a:1};
}
//嵌套
{
  const obj = {
    name:{
      chinese_name:{
        first_name:'Chen',
        last_name:'chuhai'
      },
      english_name:{
        first_name:'Chen',
        last_name:'tony'
      }
    }
  };
  let {name,name:{chinese_name},name:{chinese_name:{last_name:my_name}}} = obj;
  console.log(name);//{chinese_name: {...},english_name: {...}}
  console.log(chinese_name);//{first_name: "Chen",last_name: "chuhai"}
  console.log(my_name)//chuhai
}
```
和数组一样，对象解构也有嵌套，只是这个嵌套容易让人犯晕，别怕，回顾着之前讲的，一层层剥析。
先看解构赋值的左边，前面说了叫做解构的目标，第一个为name变量，这个很简单了，这个就对应着右边解构源叫做name的属性值，然后是第二个解构目标，根据左右匹配的规则，可以得出chinese_name就是右边解构源name属性值对象中chinese_name的属性值，最后第三个，last_name就是对应结构源上"chuhai"这个值了，只是用`:`重新指定了变量的名称为my_name。
结构目标最后只有一个，即得出最后的变量只有一个，第二次和第三次过程中分别出现的name和chinese_name，只是得到解构目标过程中为了和解构源相匹配而出现的，这样说应该就能解决大部分人对在解构目标出现的多个变量的迷惑了（可能只是我刚一接触时有这种迷惑），那些不是最终的变量，而是为了**匹配解构源对应位置的**

解构赋值也并不是一定需要声明变量来赋值的，还可以通过下面的方式进行赋值
```
let obj = {};
let arr = [];
({ foo: obj.prop, bar: arr[0] } = { foo: 123, bar: true });
obj // {prop:123}
arr // [true]
```
代码很容易看明白，抛出一个注意点，解构赋值的表达式用了一个括号，为什么呢？
因为不那么写会报错。解构赋值用的变量在之前有声明过，而`{}`恰好会被作为代码块理解,这样上下文出现两个相同名称的变量解析时就会报错，放在一个圆括号里面，就可以正确执行。详细原因请大家看[大神阮一峰es6入门--圆括号问题](https://es6.ruanyifeng.com/#docs/destructuring#%E5%9C%86%E6%8B%AC%E5%8F%B7%E9%97%AE%E9%A2%98)
对象解构赋值和数组差不多，有没有独特一点的？
有的，对象的解构赋值还可以拿到继承的属性，然后数组是特殊的对象，其实也能用对象解构的方式来解构数组。上代码
>//继承
const obj1 = {};
const obj2 = { foo: 'bar' };
Object.setPrototypeOf(obj1, obj2);
const { foo } = obj1;// "bar"
//数组解构
let arr = [1, 2, 3];
let {0 : first, [arr.length - 1] : last} = arr;
first // 1
last // 3

#### 字符串的解构赋值
后面的几种解构赋值基本上也是从对象解构或者数组解构延伸出来的，字符串可以看成一个被分割为数组的对象，所以，可以这么用
{
  //作为数组
  let [a,b,c,d] = 'tony';
  console.log(a,b,c,d);//t o n y
  //作为对象解构还能这么用
  let {length} = 'tony';
  console.log(length);//4
}

#### 数值和布尔解构赋值
解构赋值按照左右匹配规则来说，只要解构的源有数值，有结构，那么都可以进行解构，关键是看以什么解构规则进行解构，除去上面的数组对象字符串，其它的只要能转成对象，有结构，有数值，就可以进行解构，像`undefined`和`null`没值没结构的，就无法进行解构了，而布尔和数值可以。
{
  //转为对象后继承了字符对象的toString方法
  let {toString: s} = 123;
  s === Number.prototype.toString // true
  let {toString: s} = true;
  s === Boolean.prototype.toString // true
}
#### 函数参数解构赋值
这部分函数解构赋值并不是简单的这样
```
{
  function f(){
    return [1,2]
  }
  let a,b;
  [a,b]=f();
  console.log(a,b);
}
```
这样实际是拿函数的执行结果来进行解构赋值太小儿科，我也曾想过为什么不能是`{} = f`这样的解构赋值，方法里面虽然有结构有内容，但是，谁能告诉怎么个一一对应法，这不可能，所以，只能拿方法的传参讲讲了。
```
{
  function add([a, b]){
    return a + b;
  }
  console.log(add([1, 2])) // 3
  console.log([[1, 2], [3, 4]].map(([a, b]) => a + b)) //[3,7];
}
```
在传参的过程中进行解构赋值，原理和前面讲的解构赋值是一样的。

至此结构赋值的类型都说完了，回到最初说明这一运算的用途，除了交换变量，还有其它的用途，在此一一列举：
至此结构赋值的类型都说完了，回到最初说明这一运算的用途，除了交换变量，还有其它的用途，在此一一列举：
- 交换变量
- 处理函数返回值(见上述函数参数解构赋值的第一段代码)
- 处理函数传参(还是见上述函数参数解构赋值的代码)
- 处理JSON数据
- 函数参数的默认值
- 遍历 Map 结构
- 引入模块的指定方法
```
  import { loginHtml, loginResultPage, userWxBinding, consolidatedAccountContent} from '../../html/login';
```
以上为解构赋值的笔记内容，如有补充和需探讨的，欢迎留言评论~
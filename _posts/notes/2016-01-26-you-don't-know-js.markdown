---
layout:      post
title:       "You don't know JS 作用域"
subtitle:    "关于JS作用域的笔记"
date:        2016-01-26
author:      "BING"
header-img:  "img/YDK-js.jpg"
categories:  JavaScript
tags:
   - NOTES
  
post-permission: false 
---

## 前言

Javascript的编译与其他语言不同，不是发生在构建之前的
大部分情况下编译发生在代码执行前的几微妙的时间内

### LHS查询和RHS查询（L和R分别代表左侧和右侧）

    如果查找的目的是对变量进行赋值，使用的是LHS，如果目的是获取变量的值，使用的是RHS
    
```
function foo (a) {
   console.log(a); //2
}
foo(2);
```
首先引擎需要为foo进行RHS引用之后执行foo ——>
对a 进行LHS引用  a = 2                ——>
对console.log()进行RHS引用            ——>
对 a 进行RHS引用

作用域链：
        ![词法作用域](/img/in-post/YDK-js-post-in.jpg)

> LHS和RHS引用会在当前楼层进行查找
> 如果没有找到，就会坐电梯前往上一层
> 以此类推，直到找到则停止，到了全局作用域还没找到也停
> You Don't know JS


### 模块管理(与现代的模块机制很接近)
 比如以下代码：

```
var a = 2;
function foo() {
    var a = 3;
    console.log(a);   //3
}
foo();
console.log(a);       //2
```
这段代码导致的问题是：1.必须声明一个具名函数foo()，意味着foo这个名称本身“污染”了
                      所在作用域（全局作用域）
                    2.必须显示地通过函数名（foo()）调用才能运行

代码改进：   

```
var a = 2;
(function foo () {
   var a = 3;
   console.log(a);    //3
})();   //这个括号是调用
```
区别：下面的代码中的函数会被当成函数表达式而不是一个标准的函数声明来处理。
另一个改进的形式是：(function(){...}())



### 匿名和具名

  比如：      
  
```
         setTimeout( function(){
                  console.log("I waited 1 second");
               },1000 );
```
以上的函数时回调匿名函数
最佳实践是：始终给函数表达式命名：         

```
         setTimeout( function(){
                  console.log("I waited 1 second");
               },1000 );
```
倒置代码的运行顺序。模式是UMD（Universal Module Definition）       

```
var a = 2;
(function IIFE (def){
    def(window);
})(function def (global){
   var a = 3;
   console.log(a);    //3
   console.log(global.a);   //2
});

```
函数被当作参数传递进去 ——> IIFE函数调用def() 把window传递给def函数。       


### 块作用域
  ES 6 引入了新的 <b>let</b> 关键字，提供了除了var以外的另一种变量声明方式。
  let关键字可以将变量绑定到任意作用域中（通常是{...}内部）。      
  
```
  var foo = true;
  if (foo) {
     let bar = foo * 2;
     bar = something(bar);
  }
  console.log(bar);   //ReferenceError
  
  let循环
   for(let i = 0; i < 10; i++){
      console.log(i);
   }
   console.log(i);    //ReferenceError
```
   实际操作过程是：       
   
```
   {
     let j;
     for(j=0; j < 10; j++){
       let i = j;
       console.log(i);
     }
   }
```    

### Continue...
   
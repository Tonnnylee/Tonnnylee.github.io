---
layout:            post
title:             "Javascript中this的绑定笔记"
subtitle:          "You don't know JS 'this'"
author:            "BING"
header-img:        "img/YDK-js.jpg"
categories:        JavaScript
tags:
   - NOTES
   
post-permission: false
---

## 前言

在Javascript中，this关键字可谓是高深莫测，令人琢磨不透。这次就让我们来一探究竟！  

this即不指向函数自身也不指向函数的词法作用域，this实际上是在函数被调用时发生的绑定，它指向什么完全取决于函数在哪里被调用。

## this有四种绑定规则

### 默认绑定：
```
function foo(){
    console.log(this.a);
}
var a = 2;
foo();   //2
``` 
> 在strict mode下会把this绑定到undefined

### 隐式绑定：需要考虑的规则是调用位置是否有上下文对象
```
function foo(){
    console.log(this.a);
}
var obj = {
    a: 2;
    foo: foo
};
obj.foo();    //2
注意：foo() 函数严格来说不属于obj对象
```
隐式丢失就是被隐式绑定的函数会丢失绑定对象，也可以说它应用默认绑定，从而把this绑定到全局对象
或者undefined上。  

```
fuction foo (){
    console.log(this.a);
}
var obj = {
    a:2,
    foo:foo
};
var bar = obj.foo;    //函数别名！
var a   ="oops,global";  //a是全局对象的属性
bar();    //"oops,global"
```  

因为bar引用的是foo函数本身，bar()和foo()没有任何区别，关键是在引用之后调用的位置

### 显示绑定：
思考下面的代码：  

```
function foo(){
    console.log(this.a);
}
var obj = {
    a:2
};
foo.call(obj);     //2
```
call()和apply()方法的第一个参数是一个对象，它们会把这个对象绑定到this，接着再调用函数时指定这个this.  

ES5中提供了内置的方法Function.prototype.bind,它的用法如下：  

```
function foo(something){
    console.log(this.a,something);
    return this.a + something;
}
var obj = {
    a:2
}
var bar = foo.bind(obj);
var b = (3);  //2  3
console.log(b);   //5
```

### new 绑定

JavaScript中 new的机制实际上和面向类的语言完全不同。  
它们只是被new操作符调用的普通函数而已。  

使用new来调用，或者说发生构造函数调用时：
1.创建一个全新的对象。
2.这个新对象会被执行[[ prototype ]]连接。
3.这个新对象会绑定到函数调用的this。
4.如果函数没有返回其他对象，那么new表达式中的函数调用会自动返回这个新对象。

```
function foo(a){
    this.a = a;
}
var bar = new foo(2);
console.log(bar.a);   //2
```

### 优先级

显示绑定 > 隐式绑定 > 默认绑定






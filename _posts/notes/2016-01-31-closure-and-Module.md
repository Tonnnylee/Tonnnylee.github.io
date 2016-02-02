---
layout:            post
title:             "闭包的原理与模块化的笔记"
subtitle:          "You don't know JS 闭包与模块化"
date:              2016-01-31
author:            "BING"
header-img:        "img/YDK-js.jpg"
categories:        Javascript
tags:
  - NOTES
  
post-permission: false
---

## 前言

闭包一直以来都是比较难以理解的概念，但是又是Javascript十分重要的核心知识点之一
，看似隐蔽的闭包在日常的代码之中处处发挥着作用...
在被各路Javascript开发者津津乐道的模块化也是基于闭包的，只要深刻理解了也就不觉得难了
让我们一起揭开闭包和模块化的面纱

### 闭包
下面直接，清晰地展示一下闭包  

```
function foo(){
    var a = 2;
    function bar(){
        console.log(a);
    }
    return bar;
}
var baz = foo();
baz();     //2 -----23333,这就是闭包的效果啦

```
使用闭包的特征：  
（1）内部函数在自己定义的词法作用域以外的地方执行  
JavaScript引擎有垃圾回收器用来释放不再使用的内存空间。由于看上去foo()的内容不会再
被使用，所以很自然地会考虑对其进行回收。
闭包的“神奇之处”正是可以阻止这件事情的发生。事实上内部作用域依然存在，因此没有被回收。因为
bar()本身在使用  
（2）bar()依然持有对该作用域的引用，而这个引用就叫闭包。
   
     
      
简介传递函数使用闭包  

```
var fn;
function foo(){
    var a=2;
    function baz(){
        console.log(a);
    }
    fn = baz;     //将baz分配给全局变量
}
function bar(){
    fn();   //这就是闭包！
}
foo();
bar();    //2
```  



### 循环和闭包

下列代码：  

```
for(var i =1; i<=5; i++){
    setTimeout( function timer(){
        console.log(i); 
    },i*1000 );
}
```
我们预期分别输出数字1~5
但是实际这段代码在运行时6秒一次的频率输出五次6  
原因：延迟函数的回调会在循环结束时才执行。事实上，当定时器运行时即使每个迭代中执行的是  
setTimeout(..., 0);  
  
  
要想得到预期的效果，可以这样  

```
for(var i=1; i<=5; i++){
    (function(j){
        //var j=i;
        setTimeout (function timer(){
            console.log(j);
        }, j*1000);
    })(i);
}
```
当ES6 有了<b>let</b>关键词之后我们可以这样:  
    
```
for (let i =1; i<=5; i++){
    setTimeout (function timer(){
        console.log(i);
    },i*1000);
}
```

### JavaScript 模块
模块模式是利用了闭包来实现的
下例代码的实现，每次调用CoolModule()创建一个新的模块实例：
  

```
function CoolModule(){
    var someThing = "Cool";
    var another = [1,2,3];
    
    function doSometing(){
        console.log(something);
    }
    function doAnther(){
        console.log( another.join(" ! ") );
    }
    return{
        doSomething: doSomething,
        doAnother: doAnther
    };
}
var foo = CoolModule();  //调用CoolModule()
foo.doSomething();  //Cool
foo.doAnother();    //1!2!3

```
模块模式需要具备两个必要条件。  
1.必须有外部的封面函数，该函数必须被至少调用一次  
2.封闭函数必须返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并且可以访问或者修改私有的状态。  
  
从内部对模块实例进行修改：  

```
var foo = (function CoolModule(id){
    function change(){
        //修改公共API
        publicAPI.identify = identify2;
    }
    function identify1(){
        console.log(id);
    }
    function identify2(){
        console.log(id.toUpperCase());
    }
    var publicAPI = {
        change: change,
        identify: identify1
    };
    return publicAPI;
})("foo module");
foo.identify();   //foo module
foo.change();
goo.identify;     //FOO MODULE
```

### Continue...


   
















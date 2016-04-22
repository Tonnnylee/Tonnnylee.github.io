---
layout:            post
title:             "闭包的原理与模块化的笔记"
subtitle:          "You don't know JS 闭包与模块化"
date:              2016-01-31
author:            "BING"
header-img:        "img/YDK-js.jpg"
categories:        JavaScript
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

### Javascript模块的基本写法
使用的是IIFE（Immediately-Invoked Function Expression）    

```
var module = (function(){
    var count = 0;
    var m1 = function(){
        //...
    };
    var m2 = function(){
        //...
    };
    return {
        m1: m1,
        m2: m2
    }
})();
```
上面的代码，外部代码无法读取内部count变量。module.count  //undefined    
如果要为模块添加一个新的方法         
 
```
var module = (function (mod){
    mod.m3 = function () {
      //...  
    };
    return mod;
})(module);
```
模块的特点是独立性，模块内部最好不与程序的其他部分直接交互，如果要在模块内部调用全局变量，必须显示地将其他变量输入模块           

```
var module = (function($,YAHOO)) {
    //...
})(jQuery, YAHOO);
```        

上面的代码就是导入jQuery库和YUI库    

### 浅谈CommonJS     

其实在浏览器端，有没模块也都不是很大的问题，但是在服务器端就一定要有模块
node.js的模块系统是参照CommonJS规范实现的。在CommonJS中，有一个全局性方法require(),用于加载模块。    
如果有一个数学模块math.js可以像下面那样加载，然后调用模块提供的方法         
 
```
var math = require('math');
math.add(2,3);  //5
```       

第二行math.add(2,3),在第一行require('math')之后运行，因此必须等math.js加载完成，也就是说，如果加载时间很长，整个应用就会    
停在那里等。    
这对服务器端不是一个问题，因为所有的模块都存放在本地硬盘，可以同步加载完成，等待时间就是硬盘的读取时间，但是，对于浏览器，这却是    
模块都放在服务器端，等待时间取决于网速的快慢，可能要等很长时间，浏览器处于“假死”状态。    
因为浏览器端的模块，不能采用”同步加载（synchronous）“，只能采用”异步加载（asynchronous）“这就是AMD诞生的背景     

### AMD(Asynchronous Module Definition)     

采用异步方式加载模块，模块的加载不影响它后面语句的运行。所有依赖这个模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行。     
```
require([module], callback);
```     
第一个参数[module],是一个数组，里面的成员就是要加载的模块，第二个参数callback，则使加载成功之后的回调函数         

```
require(['math'], function(math){
    math.add(2, 3);
});
```      

AMD比较适合浏览器环境，require.js和curl.js实现了AMD规范    

### requireJS
requireJS的诞生，为了解决这两个问题
* 实现js文件的异步加载，避免网页失去响应
* 管理模块之间的依懒性，便于代码的编写和维护     

在网页底部写上    
<script src="js/require.js"></script>

加载require.js以后，下一步就要加载我们自己的代码了，假定我们自己的代码文件是main.js，就要写成下面这样    
<script src="js/require.js"  data-main="js/main"></script>    

data-main属性的作用是，指定网页程序的主模块。在上例中，就是js目录下面的main.js，这个文件会第一个被require.js加载。由于require.js默认的文件后缀名是js，所以可以把main.js简写成main。（可以把main模块想象成C语言的main()函数）
    

如果主模块依赖于其他模块，这时就要使用AMD规范定义的require()函数。     
```
//main.js

require(['moduleA', 'moduleB', 'moduleC'], function (moduleA, moduleB, moduleC){
    // some code here
});
```     

require()函数接受两个参数。第一个参数是一个数组，表示所有依赖的模块，上例就是['moduleA', 'moduleB', 'moduleC']，即主模块依赖这三个模块；第二个参数是一个回调函数，当前面指定的模块都加载成功后，它将被调用。加载的模块会以参数形式传入该函数，从而在回调函数内部就可以使用这些模块。   

require()异步加载moduleA，moduleB和moduleC，浏览器不会失去响应；它指定的回调函数，只有前面的模块都加载成功后，才会运行，解决了依赖性的问题。    

假定主模块依赖jQuery，underscore和backbone这三个模块，main.js就可以这么写：       
  
```
require(['jquery', 'underscore', 'backbone'], function($, _, Backbone){
    // some code here
})
```       

require.js会先加载jQuery，underscore和backbone，然后再运行回调函数。主模块的代码就写在回调函数中     

使用require.config()方法，我们可以对模块的加载行为进行自定义。require.config()就写在主模块(main.js)的头部。参数就是一个对象，     
这个对象的paths属性指定各个模块的加载路径。     

```
require.config({
    
    //baseUrl: "js/lib",
    paths: {
        jquery: "lib/jquery.min",
        underscore: "lib/underscore.min",
        backbone: "lib/backbone.min"
    }
});
```       

### AMD模块的写法    
require.js加载的模块，采用AMD规范，模块必须按照AMD规则来写。    
具体来说，就是模块必须采用特定的define()函数来定义。如果一个模块不依赖其他模块，那么可以直接定义在define()函数之中。     
假定一个math.js模块，那么它必须要这么写：       

```
//math.js

define(function(){
    var add = function (x,y){
        return x+y;
    };
    return {
        add: add
    }
})

//加载方式如下：
//main.js
require(['math'], function (math){
    alert(math.add(1,1));
});

//如果这个模块还依赖其他模块，那么define()函数的第一个参数，必须是一个数组，指明该模块的依赖性

define(['myLib'], function(myLib){
    function foo(){
        myLib.dosomething();
    }
    
    return {
        foo: foo
    };
});
//当require()函数加载上面这个模块的时候，就会先加载myLib.js文件。
```      


underscore和backbone这两个库，都没有采用AMD规范编写。如果要加载它们的话，必须先定义它们的特征      

```  
require.config({
    shim: {
        'underscore': {
            exports: '_'
        },
        
        'backbone': {
            deps ['underscore', 'jquery'],
            exports: 'Backbone'
        }
    }
});
```      

### End! see you on next posts

 




















   
















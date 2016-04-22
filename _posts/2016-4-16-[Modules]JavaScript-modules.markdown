---
layout:           post
title:           "[Modules]JavaScript模块化理论与现代模块实践"
subtitle:        "JavaScript Modules"
author:          "BING"
header-img:      "img/modules.jpg"    
categories: JavaScript
tags:
    - JavaScript
post-permission: true
---

## 前言     
如果把应用比作一个互联网公司，那么公司的部门就相当于模块，比如财务部，营销部，行政部，人事部，技术部；      
然后对于营销部来说，又可以分为市场和销售，营销部是依赖于市场部和销售部的。技术部又是依赖售前，售后和研发       
各个部门各自做着自己的本职工作，并不互相干扰，但是有些部门需要其他细分部门的合作才能工作这个叫做依赖，    
所有部门共同组建成一个公司。        
  
## JavaScript模块概念     
在一个JavaScript应用中，各个JavaScript文件分别是各个模块，各个模块相互之间可以约定好通信接口，如果你在为一个模块进行修改时    
不再需要知晓所有其他相关部件的内部实现。      

> 模块化编程的另一个重要目标是提升模块在应用程序间的复用性        

模块化的特点：
1. 在JavaScript中，模块时具有封装性的，它将自身的实现细节隐藏在内部     

2. 通过向外暴露API与外界交互   

3. 只需修改模块内部的代码就可以改变它的外在行为，而且不会对所有依赖它的其他模块造成影响        

定义模块的典型的几个规范有以下：     

* 最先的模块模式（使用闭包的模块封装）    
* AMD & CMD 模块规范    
* CommonJS 模块规范    
* 和现在定下来的的ES2015模块系统     

## 模块模式     
  IIFE（立即执行函数表达式）    
  它是使用一个包裹函数来将私有数据封装在闭包中      
  
  模块模式的版本：    

```      
//将一个外部的API传入到模块当中     
var app={};  
(function (exports) {
    
    (function (exports) {
        var api = {
            moduleExists: function test() {
                return true;
            }
        };
        $.extend(exports, api);    //exports == app
    }((typeof exports === 'undefined')?
    window : exports));
}(app));      
```        
          
## AMD规范（异步模块定义）     
异步模块加载的主要行为是：一个应用有几个模块组成，比如Twitter的应用，它有用户使用它来发布消息做状态更新模块，一个个人资料编辑模块      
我们在使用发布消息做状态的模块时，个人资料模块也在加载中    
目前实施AMD规范最著名的是requireJS它主要解决的是CommonJS同步加载脚本不合适浏览器这个问题   
同步加载是： 当我使用这个模块时应用才开始加载        
它使用名为define()的函数来做模块定义：     
  
```                 
//dependency1,2 是这个模块需要的依赖。amd1,2是它们的api参数
define(['dependency1','dependency2'], 
     //这个回调函数会在所有依赖模块加载进来时才执行    
     function myModule(amd1, amd2){
    var testResults = {
        test1: amd1.test(),  
        test2: amd2.test()
    },
    //define a public API for your module:
    api = {
        testResult: function(){
            return testResults;
        }
    };
    return api;
})     

//在一个app.js中使用上面的模块    
require(['testResult'],
  function(amd){
      var results = amd.testResults();
      results.test1;     //First dependency loaded correctly
      results.test2;    //second dependency loaded correctly 
  }
);
```       
        
AMD的缺点：     
1. 要求要为每个模块包裹额外的样板函数      
2. 迫使将整个应用代码编译打包成一个文件（一个script标签）不然你就得在浏览器中异步加载每个模块，这实际上会严重拖慢脚本的加载和执行      
3. 当模块或依赖非常多的时候，AMD方案会变得越来越不可行      

## CommonJS模块规范       
> 此段来自JavaScript应用程序设计一书       

在Node.js出现之前，早在20实际90年代，网景与微软就有尝试过把JavaScript运行环境搬迁至服务器，然而由于缺乏应用场景，实际效果并不怎么好，首到人们广泛关注     
的服务端JavaScript运行环境是Rhino，但由于其性能问题以及略显笨重的实现方式，在它之上很难构建起大型Web应用程序     

等到Node.js出现之后，市面上已经有好几种服务端JavaScript运行环境了，然而它们彼此间又拥有不同的模块加载机制与约定，CommonJS规范的出现正是为了解决这个问题,
Node.js 中的模块系统就是对CommonJS模块规范的一种实现。     

CommonJS规定文件就是模块，模块封装不再依赖包裹函数创建的临时作用域，因为每个文件都拥有自己的作用域       
浏览器不兼容CommonJS的根本原因，在于缺少四个Node.js环境的变量：module,exports,require,global    
只要提供这四个变量，浏览器就能加载CommonJS模块       

```      
var a = require('./a'); //等这句加载完a模块才往下执行
a.doSomething();

var foo = function (){
    return true;
};       
exports.foo = foo;      //暴露foo函数接口        
```       
#### CommonJS代码示例         
``` 
//guestlistmodel.js   
var api = {
    load: function load(){
        return [
            'Jimi hendrix',
            'Billie Holiday',
            'Nina Simone',
            'John Bonham',
            'zhibingpro'
        ]
    }
};     
```     
```     
//guestlistview.js     

//获取jQuery的引用
var $ = require('jquery'),
    checkedinClass = 'icon-check',
    listClass = 'dropdown-menu',
    guestClass = 'guest',
    
    toggleCheckedIn = function toggleCheckedIn(e) {
        $(this).toggleClass(checkedinClass);
    },
    
    $listView = $('<ol>', {
        id: 'guestlist-view',
        'class' : listClass
    }).on('click', '.'+ guestClass, toggleCheckedIn),
    
    render = function render(guestlist){
        
        $listView.empty();
        
        guestlist.forEach(function(guest) {
            $guest = $('<li class="' + guestClass + '">' + '<span class="name">' + guest + '</span></li>');
        });
        
        return $listView;
    },
    
    api = {
        render: render
    };
    
    module.exports = api;     
```    
```  
//app.js    
/**
 * import guestlistmodel and guestlistView
 * 
 */

var $ = require('jquery'),
    guestlistModel = require('guestlistModel'),     //src
    guestlistView = require('guestlistview'),
    $container = $('#container');
    
    $(function init() {
        var guestlistDate = guestlistModel.load();
        var $guestlist = guestlistView.render(guestlistData);
        $container.empty().append($guestlist);
    });
 
```  

## ES2015模块规范 （working）


       







  
  
  
        
  
  
  
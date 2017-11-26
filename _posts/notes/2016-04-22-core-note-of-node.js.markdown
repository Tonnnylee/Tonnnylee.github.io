---
layout:            post
title:             "[笔记]关于Node.js重要知识点归纳笔记(未完)"
subtitle:          "JavaScript: I'm not just working on browser anymore."
author:            "BING"
header-img:        "img/nodejs.jpg"
categories:        Node.js
tags:
   - NOTES
   - Node.js
   
post-permission: true
---

## 前言      
对于许多JavaScript程序员来说，Node.js在现在看来早已经司空见惯了，它目前也成为JavaScript的重要的一部分。在前言中我就简单介绍Node.js。  
Node.js是在服务端运行的JavaScript，它是为网络而生的。Node.js能做的远不止开发一个网站那么简单，使用Node.js可以轻松地开发：     
* 具有复杂逻辑的网站      
* 基于社交网络的大规模Web应用     
* Web Socket服务器  
* TCP/UDP套接字应用程序   
* 命令行工具   
* 交互式终端程序   
* 带有图形用户界面的本地应用程序   
* 单元测试工具   
* 客户端JavaScript编译器   

> 本篇笔记暂时来自对[图灵原创] Node.js开发指南 的知识整合，我自己的图文理解        

## Node.js简单概念    
Node.js是一个实时web(Real-time Web)应用开发而诞生的平台，<strong>具有实时响应</strong>,超大规模数据要求下架构的可扩展性的特点    
采用了<strong>单线程,异步式I/O，事件驱动式的程序设计模型</strong>      

Node.js内建了HTTP服务器支持，可以直接实现一个网站和服务器的组合，不像PHP等其他后端语言，它们必须先搭建一个Apache之类的HTTP服务器，    
而使用Node.js时不用额外搭建一个HTTP服务器      

<strong>异步式I/O与事件驱动</strong>        
  Node.js使用的是单线程模型，对于所有I/O都采用异步式的请求方式，每个异步式I/O请求完成后会被推送到事件队列，等待程序进程进行处理    
  ![nodejs](/img/in-post/nodejs_thread.jpg)       
  由上图可以知道，I/O请求不是先执行完成的，它们可能后与compute1,2,3,4,5..等执行
```
db.query('SELECT * from some_table', function (res){
    
    res.output();
}); 
//以下还有其他代码     
```
进程在执行到db.query的时候，不会等待结果返回，而是继续执行后面的语句，直到进入事件循环，当数据库查询结果返回时，     
会将事件发送到事件队列，等待线程进入事件循环以后，才会调用之前的回调函数    

Node.js的异步机制是基于事件的，所有的磁盘I/O，网络通信，数据库查询都是非阻塞地请求进行，返回结果由事件循环来处理。   

## Node.j核心模块（归纳笔记）      
<strong>常用工具util 是Node.js核心模块之一，提供常用函数的集合</strong>        

* util.inherits是一个实现对象间原型继承的函数实例如下       
```
function Base() {
  this.name = 'base';
  this.base = 1991;

  this.sayHello = function() {
       console.log('Hello' + this.name);
   };
}

Base.prototype.showName = function() {
   console.log(this.name);
};

function Sub() {
  this.name = 'sub';
}

util.inherits(Sub, Base);

var objBase = new Base();
objBase.showName();
objBase.sayHello();
console.log(objBase);

var objSub = new Sub();
objSub.showName();
//objSub.sayHello();
console.log(objSub);

Sub仅仅继承了Base在原型中定义的函数，而构造函数内部创造的都没有被Sub继承
    
```
* util.inspect(object, [showHidden], [depth], [colors])是一个将任意对象转换为字符串的方法       

<strong>事件驱动events</strong>     
events是node.js最重要的模块，原因是Node.js本身架构就是事件式的，它提供了唯一的接口，是Node.js事件的编程基石      
events模块只提供了一个对象：events.       
EventEmitter。EventEmitter的核心就是事件发射与事件监听器功能的封装。          
EventEmitter的每个事件由一个事件名和若干个参数组成，事件名是一个字符串，通常表达一定的语义。对于每个事件，      
EventEmitter支持若干个事件监听器。当事件发射时，注册到这个事件的事件监听器被依次调用，事件参数作为回调函数参数传递。        
例子：          
       
```
var events = require('events');
var emitter = new events.EventEmitter();
emitter.on('someEvent', function(arg1, arg2) {         //事件监听器
    console.log('listener1', arg1, arg2);
});
emitter.on('someEvent', function(arg1, arg2) {
    console.log('listener2', arg1, arg2);
});
emitter.emit('someEvent', 'zhibing', 1995);        //事件发射
运行结果：
listener1  zhibing  1995
listener2  zhibing  1995

```
API:
EventEmitter.once(event, listener)    //为指定事件注册一个单次监听器，就是监听器最多触发一次，触发后立即解除该监听器     
EventEmitter.removeListener(event, listener)   //移除指定事件的某个监听器，listener必须是该事件已经注册过的监听器    
EventEmitter.removeAllListener([event])  //移除所有事件的所有监听器      

<strong>文件系统</strong>        
API：    
fs.readfile(filename, [encoding], [callback(err, data)])    //异步文件读取    
fs.readFileSync(filename, [encoding])    //同步文件读取     
...   具体API查看官方文档     

<strong>HTTP服务器</strong>      
http.Server是http模块中的HTTP服务器对象       
```
//app.js    
var http = require('http');

http.createServer(function(req, res) {         //简易的request事件监听模式     
    res.writeHead(200, {'content-Type': 'text/html'});
    res.write('<h1>Node.js</h1>');
    res.end('<p>Hello World</p>');
}).listen(3000); 
```
req,res分别是请求对象，和响应对象      
http.Server是一个基于事件的HTTP服务器，所有的请求都被封装为独立的事件：request，connection，close     
最常用的是：request事件，上面的代码可以显示写成如下：    
```
//httpServer.js       
var http=require('http');
var server=new http.Server();    
server.on('resquest', function(req, res) {    //request可以是其他事件
    ...
});
server.listen(3000);        
```
以上代码参数中的req是 http.ServerRequest的对象      
HTTP请求分为两部分    
请求头（Request Header）:时间短解析完后立即读取    
请求体（Request Body）:时间长。
提供以下3个事件用于控制请求体传输     
* data事件：当请求体数据到来时，该事件被触发。这个事件提供了一个参数chunk，表示接收到的数据，如果该事件没有被监听，那么   
  请求体将会被抛弃。可以被调用多次。    
* end 和 close事件，end是当请求体数据传输完成时触发     
      
<strong>获取GET请求</strong>      
GET请求是直接被嵌入在路径中的，可以手动解析后面的内容，Node.js的url模块中的parse函数提供了这个功能.
```      
//httpserverrequestGET.js     
var http = require('http');
var url = require('url');
var util = require('util');

http.createServer(function(req, res) {
    res.writeHead(200, {'Content-Type': 'text/plain'});
    res.end(util.inspect(url.parse(req.url, true)));      
}).listen(3000);      

//在浏览器中访问http://127.0.0.1:3000/user?name=zhibing&email=zhibing@zhibing.com, 我们     
可以看到浏览器返回的结果：    
{
    search: '?name=zhibing&email=zhibing@zhibing.com',
    query: { name: 'zhibing', email: 'zhibing@zhibing.com' },
    pathname: '/user',    
    path: '/user?name=zhibing&email=zhibing@zhibing.com',
    href: '/user?name=zhibing&email=zhibing@zhibing.com'
    
}

```
其中query就是我们所谓的GET请求的内容，路径时pathname.     
           
<strong>获取POST请求内容</strong>     
POST请求的内容全部都在请求体中      
Node.js默认是不会解析请求体的，有需要的时候需要手动来做。     

<strong>HTTP客户端请求</strong>(Continue...)      


## Node.js不适合做什么      
 1. <strong> 计算密集型的程序</strong>         
    前面我们提到Node.js由于其单线程异步式的特性，所有遇到I/O请求就发送到操作系统，所有    
    的请求必须等待当前请求处理完毕以后进入事件循环才能响应。如果一个应用是计算密集型的，比如     
    Node.js单线程在执行首先遇到一个I/O请求，该I/O请求将被送往操作系统，接下来遇到大量的计算   
    型程序，必须等待大量计算型程序处理完毕后进入事件循环才能响应之前的I/O请求。所以所谓的单   
    线程异步式的优势就丧失了。相反，多线程同步式的处理计算密集型的程序就更快一些。    
    
    不过好在真正的Web服务器中，很少会有计算密集的部分，如果真有，那也不该被实现为即时的响应，   
    正确的方式是给用户一个提示，说服务器正在处理中，完成后会通知用户。    
   
 2. <strong>单用户多任务型应用</strong>     
    和计算密集型类似，单用户就说明大量并发请求就不存在了，单是可能会有多任务，因此处理大量     
    并发请求的优势就不复存在了。    
 3. <strong>逻辑十分复杂的事务</strong>     
    Node.js的控制流不是线性的，它被一个个事件拆散，不过人的思维却是线性的，当你试图转换思维    
    来迎合语言或编译器时，就不得不作出牺牲，举例来说，你要实现一个这样的逻辑：从银行取钱，  
    拿钱去购买某个虚拟商品，买完以后加入库存数据库，这中间的任何一步都可能会涉及数十次的I/O操作    
    ，而且任何一次操作失败都要进行回滚操作，这个过程是线性的，已经很复杂了，如果要拆分为非线性    
    的逻辑，那么其复杂度很可能就大道无法维护的地步了。
  
> Node.js更善于处理那些逻辑简单但访问频繁的任务，而不适合完成逻辑十分复杂的工作
  
   
 
 





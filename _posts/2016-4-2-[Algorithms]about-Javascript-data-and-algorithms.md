---
layout:           post
title:           "[算法]有关JavaScript数据结构与算法归纳笔记"
subtitle:        "JavaScript Data Structures and Algorithms"
author:          "BING"
header-img:      "img/Algorithms.jpg"    
categories: JavaScript
tags:
    - 算法
    - JavaScript
post-permission: true
---

## 前言    

 在任何编程语言里，数据结构与算法一直扮演着非常重要的角色，它们是决定一个软件产品性能的关键所在，尤其是在比较大的项目中更有所体现   
    JavaScript 也属于一门高级语言，也就是说Javascript底层或者说是原生已经实现了相当多的数据结构与算法，我们可以直接用来开发。但是   
 我们仅仅只是使用已经帮我们封装好的数据结构与算法的话，并不能帮助JavaScript开发者更深层地学习到JavaScript这门语言，     
 所以让我们来探讨一下一些数据结构与算法在JavaScript是如何表现的       
           
 ## 内容来源   
 本博文的内容来源是根据——[学习JavaScript数据结构与算法](https://book.douban.com/subject/26639401/),有兴趣的同学可以观看本书![JS-data](/img/in-post/JS-data.jpg)      
        
 ## Catalog    
 1. [栈类](#stack)    
 2. [队列](#queue)    
 3. [链表](#link)     
 4. [字典](#dictionary)    
 5. [散列表](#hash)   
 6. [树](#tree)    
 7. [图](#graph)      
 8. [排序和搜索算法](#sort)   
       
 <h4 id="stack"></h4>       
 ## 栈类     
 让我们看下一个简单的栈类的数据结构实现      
 ```              
 function Stack() {
     var items = [];
     this.push = function(element){
         items.push(element);
     };
     
     this.pop = function(){
         return items.pop();
     };
     
     this.peek = function(){
         return items[items.length-1];
     };
     this.isEmpty = function(){
         return items.length == 0;
     };
     this.size = function(){
         return items.length;
     }
     this.clear = function(){
         items = [];
     };
     
     this.print = function(){
         console.log(items.toString());
     };
 }
 
 ```      
 
 上面的就是一个完整的栈类数据结构，我们可以使用它来写进制转换的算法，base就是你想要转换的基数，decNumber是你要转换的数       
         
 ```     
 function baseConverter(decNumber, base){
     var remStack = new Stack(),
         rem,
         baseString = '',
         digits = '0123456789ABCDEF';
         
     while (decNumber > 0){
         rem = Math.floor(decNumber % base);
         remStack.push(rem);
         decNumber = Math.floor(decNumber / base);
     }
     
     //将栈里的数字导入字符串digits后输出，如果是十六进制则可以导出十六进制    
     while(!remStack.isEmpty()){
         baseString +=digits[remStack.pop()];
     }
     
     return baseString;
 }
 
 使用之前的算法，输出结果如下：      
 console.log(baseConverter(100345, 2));    //输出110000111111110011
 console.log(baseConverter(100345, 8));    //输出303771    
 console.log(baseConverter(100345, 16));   //输出187F9     
      
 ```       
     
 <h4 id="queue"></h4>      
## 队列     
照例看看Queue类的完整实现：       

```     
function Queue(){
    var items = [];
    this.enqueue = function(element){
        items.push(element);
    };
    this.dequeue = function(){
        return items.shift();
    };
    this.front = function(){
        return items[0];
    };
    this.isEmpty = function(){
        return items.length == 0;
    };
    this.clear = function(){
        items = [];
    };
    this.size = function(){
        return items.length;
    };
    this.print = function(){
        console.log(items.toString());
    };
}
```      
队列有很多种改版，其中一个修改版是优先队列，元素的添加和移除是基于优先级的，实现一个优先队列，有两种选项：设置优先级，然后      
在正确的位置添加元素       
      
```     
function PriorityQueue() {
    var items = [];
    function QueueElement (element, priority){
        this.element = element;
        this.priotity = priority;
    }
    
    this.enqueue = function(element, priority){
        var queueElement = new QueueElement(element, priority);
        
        if(this.isEmpty()) {
            items.push(queueElement);
        }else {
            var added = false;
            for(var i=0; i<items.length; i++) {
                if(queueElement.priority < items[i].priority){
                    //插入
                    items.splice(i,0,queueElement);
                    added = true;
                    break;
                }
            }
            if(!added){
                items.push(queueElement);
            }
        }
    };
}
```      
      
 <h4 id="link"></h4>       
       
## 链表     
单链表的源代码：     
```          
function LinkedList() {
    var Node = function(element){
        this.element = element;
        this.next = null;
    };
    
    var length = 0;
    var head = null;
    
    //向链表后面接入一个结点
    this.append = function(element){
        var node = new Node(element),
            current;
            
        if(head === null){
            head = node;
        }else{
            current = head;
            
            //循环列表，直到找到最后一项
            while(current.next){
                current = current.next;
            }
            
            //找到最后一项，将其next赋为node
            current.next = node;
            
        }
        length++;
    };
    
    //从链表中移除元素    
    this.removeAt = function(position){
        //检查是否越界
        if(position > -1 && position < length){
            var current = head,
                previous,
                index = 0;
                
            //移除第一项
            if(position === 0){
                head = current.next;
            }else{
                while (index++ <position){
                    previous = current;
                    current = current.next;
                }
                
                //将previous与current的下一项链接起来；跳过current，从而移除它
                previous.next = current.next;
            }
            length--;
            return current.element;
        }else {
            return null;
        }
    };
    
    this.insert = function(position, element){
          if(position >= 0 && position <= length){
               var node = new Node(element),
                   current = head,
                   previous,
                   index = 0;
               if(position === 0) {
                   node.next = current;
                   head = node;
               }else{
                   while (index++ < position){
                       previous = current;
                       current = current.next;
                   }
                   node.next = current;
                   previous.next = node;
               }
               length++;
               return true;
          }else{
              return false;
          }
    };
    
    this.toString = function(){
        var current = head,
            string = '';
        
        while (current) {
            string += current.element;
            current = current.next;
        }
        return string;
    };
    
    this.indexOf = function(element){
        var current = head,
            index = 0;   //书上是-1，我认为是错了
        while(current){
            if(element === current.element) {
                return index;
            }
            index++;
            current = current.next;
        }
        
        return -1;
    };
    
    this.remove = function(element){
        var index = this.indexOf(element);
        return this.removeAt(index);
    };
    
    this.isEmpty = function() {
        return length === 0;
    };
    
    this.size = function(){
        return length;
    };
    
    this.getHead = function() {
        return head;
    }
}
```    
双向链表源代码：        
```      
function DoublyLinkedList() {
    var Node = function(element){
        this.element = element;
        this.next = null;
        this.prev = null;
    };
    
    var length = 0;
    var head = null;
    var tail = null;
    
    this.insert = function(position, element){
        //检查越界值
        if(position >= 0 && position <= length){
            var node = new Node(element),
                current = head,
                previous,
                index = 0;
                
            if(position === 0){
                if(!head){
                    head = node;
                    tail = node;
                }else{
                    node.next = current;
                    current.prev = node;
                    head = node;
                }
                //最后一项
            }else if(position === length){
                current = tail;
                current.next = node;
                node.prev = current;
                tail = node;
            }else{
                while (index++ < position){
                    previous = current;
                    current = current.next;
                }
                node.next = current;
                previous.next = node;
                
                current.prev = node;
                node.prev = previous;
            }
            
            length++;
            return true;
        }else{
            return false;
        }
    };
    
    this.removeAt = function(position){
         if(position >-1 && position < length){
              var current = head,
                  previous,
                  index = 0;
                  
             //移除第一项
             if(position === 0){
                 head = current.next;
                 if(length ===1){
                     tail = null;
                 }else{
                     head.prev = null;
                 }
             }else if(position === length-1){ //最后一项
                 current = tail;
                 tail = current.prev;
                 tail.next=null  //移除
             }else{
                 while(index++ < position){
                     previous = current;
                     current = current.next;
                 }
                 //将previous与current的下一项链接起来——跳过current
                 previous.next = current.next;
                 current.next.prev = previous;
             }
             length--;
             
             return current.element;
         }else{
             return null;
         }
    }
}
```        
 <h4 id="dictionary"></h4>       
       
## 字典和散列表    
在字典中，存储的是[键，值]对，其中键名是用来查询特定元素的 
Dictionary部分代码实现       
      
```        
function Dictionary() {
    var items = {};
    
    this.has = function(key){
        return key in items;
    }
    
    this.set = function(key, value) {
        items[key] = value;
    }
    
    this.remove = function(key) {
        if(this.has(key)) {
            delete items[key];
            return true;
        }
        return false;
    };
    this.get = function(key){
        return this.has(key) ? items[key] : undefined;
    };
    
    this.values = function() {
        var values = [];
        for(var k in items) {
            if(this.has(k)){
              values.push(items[k]);
            }
        }
        return values;
    };
}
```     

 <h4 id="hash"></h4>        
       
## 散列表       
散列算法的作用是尽可能地在数据结构中找到一个值，它是以数组的方式呈现的，不像字典一样是用对象来存储数据，查找一个     
值的时候，需要遍历整个数据结构来查找，所以存储多数据的情况下散列表比字典性能好      
散列表的原理是：将键值中的每个字母的ASCLL值相加得到一个新的键，然后查找     
源代码是：      
```     
function HashTable() {
    var table = [];
    
    //散列函数（目前在社区推荐的散列函数之一）  
    var djb2HashCode = function(key) {
        var hash = 5381;
        for(var i=0; i<key.length; i++){
            hash = hash * 33 +key.charCodeAt(i);
        }
        return hash % 1013;
    };
   
   this.put = function(key, value){
       var position = djb2HashCode(key);
       console.log(position + ' - '+key);
       table[position] = value;
   };
   this.get = function(key){
       return table[djb2HashCode(key)];
   };
   this.remove = function(key){
       table[djb2HashCode(key)]=undefined;
   };
   this.print = function() {
       for(var i=0; i<table.length; i++){
           if(table[i] !== undefined){
               console.log(i + ": "+table[i]);
           }
       }
   };
   
}       
```       


<h4 id="tree"></h4>        
     
## 树      
二叉搜索树:结点的左子结点比结点小，右子结点比结点大     
让我们创建一个BinarySearchTree类      


```          
function BinarySearchTree() {
    var Node = function(key){
        this.key = key;
        this.left = null;
        this.right = null;
    };
    
    var root = null;
    //向树中插入一个键    
    this.insert = function(key){
        var newNode = new Node(key);
        
        if(root === null){
            root = newNode;
        }else{
            insertNode(root,newNode);
        }
    };
    var insertNode = function(node, newNode){
        if(newNode.key < node.key){
            if(node.left ===null){
                node.left = newNode;
            }else{
                insertNode(node.left, newNode);
            }
        }else{
            if(node.right === null){
                node.right = newNode;
            }else{
                insertNode(node.right, newNode);
            }
        }
    };
    //中序遍历：以从最小到最大的顺序访问所有结点
    this.inOrderTraverse = function(callback){
        inOrderTraverseNode(root, callback);
    };
    var inOrderTraaverseNode = function(node, callback){
        if(node !== null){
            inOrderTraverseNode(node.left, callback);
            callback(node.key);
            inOrderTraverseNode(node.right, callback);
        }
    };
    
    //先序遍历：从第一个开始，如果它有左子节点则访问它的左子节点，直到没有左子节点，返回上一个节点访问它的右子节点，以此类推
     this.preOrderTraverse = function(callback){
        preOrderTraverseNode(root, callback);
    };
    var preOrderTraaverseNode = function(node, callback){
        if(node !== null){
            callback(node.key);
            preOrderTraverseNode(node.left, callback);         
            preOrderTraverseNode(node.right, callback);
        }
    };
    
    //后序遍历：先访问节点的后代节点，再访问节点本身   
     this.postOrderTraverse = function(callback){
        postOrderTraverseNode(root, callback);
    };
    var postOrderTraaverseNode = function(node, callback){
        if(node !== null){ 
            postOrderTraverseNode(node.left, callback);         
            postOrderTraverseNode(node.right, callback);
            callback(node.key);
        }
    };
    
    //搜索树中的最大值和最小值(最左边和最右边)
    this.min = function() {
        return minNode(root);
    };
    
    var minNode = function(node){
        if(node){
            while(node && node.left !== null){
                node = node.left;
            }
            return node.key;
        }
        return null;
    };
    this.max = function(){
        return maxNode(root);
    };
    var maxNode = function(node){
        if(node){
            while(node && node.right !==null){
                node = node.right;
            }
            return node.key;
        }
        return null;
    };
    
    //搜索一个特定的值(返回true or false)
    this.search = function(key){
        return searchNode(root, key);
    };
    
    var searchNode = function(node, key){
        if(node === null){
            return false;
        }
        if(key < node.key){
            return searchNode(node.left, key);
        }else if(key > node.key){
            return searchNode(node.right, key);
        }else{
            return true;
        }
    };
    //移除一个节点,最难的部分，有许多要处理的条件  
    this.remove = function(key){
        root = removeNode(root, key);
    };
    
    var removeNode = function(node, key){
        if(node === null){
            return null;
        }
        if(key < node.key){
            node.left = removeNode(node.left, key);
            return node;
        }else if(key > node.key){
            node.right = removeNode(node.right, key);
            return node;
        }else {    //当键等于node.key时
            //第一种情况——一个叶结点（最深层的节点）
            if(node.left === null && node.right === null){
                node = null;
                return node;
            }
            
            //第二种情况——一个只有一个子节点的节点
            if(node.left ===null){
                node = node.right;
                return node;
            }else if(node.right === null){
                node = node.left;
                return node;
            }
            
            //第三种情况——一个有两个子节点的节点
            var aux = findMinNode(node.right)   //找到右节点中最小的那个节点
            node.key = aux.key;
            node.right = removeNode(node.right, aux.key);
            return node;
        }
    }
    
}
    
```     







稍后解析最难的移除部分     
         
<h4 id="graph"></h4>         

## 图    
有许多数据结构可以表示图，这里我们可以用邻接表来表示图，它就像如下图所示：     
![linjiebiao](/img/in-post/lingjiebiao.png)      

照例，我们看看图的源代码:        
        
```         
function Graph() {
    var vertices = [];
    var adjList = new Dictionary();   //我们这里有用到字典类 
    //添加新顶点   
    this.addVertex = function(v){
        vertices.push(v);
        adjList.set(v, []);
    };
    
    //添加相邻的顶点
    this.addEdge = function(v, w){
        adjList.get(v).push(w);
        adjList.get(w).push(v);
    };
    
    //可以输出邻接表  
    this.toString = function(){
        var s = '';
        for(var i=0; i<vertices.length; i++){
            s += vertices[i] + ' -> ';
             var neighbors = adjList.get(vertices[i]);
             for(var j=0; j<neighbors.length; j++){
                s += neighbors[j] + ' ';
            }
            s += '\n';
        }
        return s;
    };
}
```         
     
有两种算法可以对图进行遍历：广度优先搜索和深度优先搜索，图遍历可以用来寻找特定的顶点或寻找两个顶点之间的路径，检查图是否连通，   
检查图是否含有环等     
深度优先搜索：使用的数据结构是——栈，通过将顶点存入栈中，顶点是沿着路径被探索的，存在新的相邻顶点就去访问      
广度优先搜索：使用的数据结构是——队列，通过将顶点存入队列中，最先入队列的顶点先被探索。    
当要标注已经访问过的顶点时，我们用三种颜色反映它们的状态。      

* 白色：表示该顶点还没有被访问过     
* 灰色：表示该顶点被访问过，但并未被探索过（访问它的相邻顶点）     
* 黑色: 表示该顶点被访问过且被完全探索过     
广度优先搜索算法是，从指定的第一个顶点开始遍历图，先访问其所有的相邻点，就像访问图的一层     
广度优先搜索算法：      

```          
var initializeColor = function(){
    var color = [];
    for(var i=0; i<vertices.length; i++){
        color[vertices[i]] = 'white';
    }
    return color;
};   
this.BFS = function(v){
    var color = initializeColor(),
        queue = new Queue(),
        d = [],     //顶点到各个点的距离   
        pred = [];  //每个顶点的前溯顶点  
        queue.enqueue(v);  
        
        for(var i=0; i<vertices.length; i++){
            d[vertices[i]] = 0;
            pred[vertices[i]] =null;
        }
        
        while(!queue.isEmpty()){
            var u = queue.dequeue(),
                neighbors = adjList.get(u);
            color[u] = 'grey';
            for(i=0; i<neighbors.length; i++){
                var w = neighbors[i];
                if(color[w] === 'white'){
                    color[w] = 'grey';
                    d[w] = d[u] + 1;
                    pred[w] = u;
                    queue.enqueue(w);
                }
            }
            color[u] = 'black';
        }
        return {
            distances: d,
            predecessors: pred
        };
}
```         
         
深度优先搜索算法：会从一个指定的顶点开始遍历图，沿着路径直到这条路径最后一个顶点被访问了，接着原路回退并探索下一条路径    
它是先深度后广度地访问顶点    
         
```      
var time =0;
this.DFS = function() {
    var color = initializeColor(),
        d = [],   //距离
        f = [],   //探索时间
        p = [];   //顶点前溯
        time = 0; 
        
        for(var i=0; i<vertices.length; i++){
            f[vertices[i]] = 0;
            d[vertices[i]] = 0;
            p[vertices[i]] =null;
        }
        for(i=0; i<vertices.length i++){
            if(color[vertices[i]] === 'white'){
                DFSVisit(vertices[i], color, d, f, p);
            }
        }
        return {
            discovery: d,
            finished: f,
            predecessors: p
        };
        
};

var DFSVisit = function(u, color, d, f, p){
    console.log('discovered '+ u);
    color[u] = 'grey';
    d[u] = ++time;
    var neighbors = adjList.get(u);
    for(var i=0; i<neighbors.length; i++){
        var w = neighbors[i];
        if(color[w] === 'white'){
            p[w] = u;
            DFSVisit(w, color, d, f, p);
        }
    }
    color[u] = 'black';
    f[u] = ++time;
    console.log('explored '+ u);
};   
```           

 <h4 id="sort"></h4>     
## 排序与搜索算法 （working....）
 




 
 
 

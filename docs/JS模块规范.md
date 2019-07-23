## 前言

> 为什么模块很重要？因为有了模块，我们就可以方便的使用别人的代码，想要什么功能，就加载什么模块，但是这样做有一个前提就是大家必须以同样的方式编写模块。

## 模块的规范

目前通行的模块规范有：CommonJS, AMD, CMD, UMD, ES6 module等

## CommonJS

nodeJS 诞生后，标志着JS模块化编程正式诞生，因为老实说，在浏览器环境下,没有模块也不是特别大的问题，毕竟网页程序的复杂性有限；但是在服务端，一定要有模块，与操作系统和其他应用程序互动，否则根本没法编程。nodeJS 的模块系统就是参照CommonJS规范实现的。

在CommonJS中，有一个全局方法require()，用于加载模块，

~~~javascript
var math = require('math')
math.add(2,3)
~~~

这种加载方式在服务端不是一个问题，因为所有的模块都存放在本地硬盘，可以同步加载完成，但是对于浏览器，如果模块都放在服务器端，等待的时间取决于网速的快慢。因此，浏览器的模块不能采用`同步加载`,只能采用`异步加载`,这就是AMD规范诞生的背景。

### 特点

1. 模块可以多次加载，但是只会在第一次加载时运行一次，然后运行结果就被缓存了，以后再加载，就直接读取缓存结果，要想让模块再次运行，必须清除缓存；

2. 模块加载会阻塞接下来的代码执行，需要等到模块加载完才能继续执行，即同步加载。

3. 导入和导出

   ~~~nodejs
   require('path')
   module.exports和exports
   ~~~

## AMD

AMD是`Asynchronous Module Definition`的缩写，意思就是异步模块定义。它采用异步方式加载模块，模块的加载不影响后面语句的运行。所有依赖这个模块的语句，都定义在一个回调函数中，等到加载完成后，这个回调函数才会运行。

AMD 也采用require()语句加载模块，但是不同于CommonJS，它要求两个参数：

`require([module], callback)`

~~~javascript
require(['math'], function(math) {
  	math.add(2,3)
})
~~~

目前，主要有两个JavaScript库实现了AMD规范: `require.js`和`curl.js`

### require.js

早期，我们这样加载多个JS文件

~~~javascript
<script src="1.js"></script>
<script src="2.js"></script>
<script src="3.js"></script>
<script src="4.js"></script>
<script src="5.js"></script>
<script src="6.js"></script>
~~~

**缺点如下：**

1. 加载的时候，浏览器会停止网页渲染，加载文件越多，网页失去响应的时间就会越长；
2. 如果JS文件之间存在依赖关系，则必须严格保证加载顺序，当依赖关系很复杂的时候，代码的编写和维护都会变得困难。

require.js的诞生就是为了解决这两个问题：

1. 实现js文件的异步加载，避免网页失去响应；
2. 管理模块之间的依赖性，便于代码的编写和维护。

[详细使用如下](<http://www.ruanyifeng.com/blog/2012/11/require_js.html>)

## CMD

CMD是SeaJS在推广过程中对模块定义的规范产出

* 对于依赖的模块AMD是提前执行，CMD是延迟执行，不过RequireJS从2.0开始，也改成可以延迟执行(根据写法不同，处理方式不通过)。

* CMD推崇依赖就近，AMD推崇依赖前置。

* ```javascript
  s//AMD
  define([&#39;./a&#39;,&#39;./b&#39;], function (a, b) {
   
      //依赖一开始就写好
      a.test();
      b.test();
  });
   
  //CMD
  define(function (requie, exports, module) {
       
      //依赖可以就近书写
      var a = require(&#39;./a&#39;);
      a.test();
       
      ...
      //软依赖
      if (status) {
       
          var b = requie(&#39;./b&#39;);
          b.test();
      }
  });	s
  ```

  虽然AMD也支持CMD写法，但依赖前置是官方文档的默认模块定义方法。

* AMD的API默认是一个当多个用，CMD推崇职责单一。如：AMD里require分全局和局部的。CMD没有全局的require，提供seajs.use()来实现模块系统的加载启动。CMD里每个API都简单纯粹。

## UMD

UMD是AMD和CommonJS的糅合

UMD先判断是否支持NodeJS的模块(exports)是否存在，存在则使用NodeJS模块，再判断是否支持AMD(define是否存在),存在则使用AMD方式加载模块。

## ES6 module

1. <http://es6.ruanyifeng.com/#docs/module>
2. <http://es6.ruanyifeng.com/#docs/module-loader>

## 参考文档

1. [AMD,CMD,CommonJS,UMD](<https://www.jianshu.com/p/bd4585b737d7>)
2. [requireJS和SeaJS](<https://www.zhihu.com/question/20342350/answer/32484869>)
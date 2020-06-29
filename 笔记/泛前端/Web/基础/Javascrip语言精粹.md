





## 继承

1. JS的继承的本质是什么



## NodeJS

Node.js® is a JavaScript runtime built on [Chrome's V8 JavaScript engine](https://v8.dev/).



## Bable

1. Babel 是一个 JavaScript 编译器；

2. Babel 是一个工具链，主要用于将 ECMAScript 2015+ 版本的代码转换为向后兼容的 JavaScript 语法，以便能够运行在当前和旧版本的浏览器或其他环境中。



## WebPack

本质上，*webpack* 是一个现代 JavaScript 应用程序的*静态模块打包器(module bundler)*。当 webpack 处理应用程序时，它会递归地构建一个*依赖关系图(dependency graph)*，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 *bundle*。



## 模块

1. CommonJs——用于Node环境

   根据CommonJs规范，每个文件就是一个模块，有自己的作用域。在一个文件里面定义的变量、函数、类，都是私有的，对其他文件不可见，CommonJS规范加载模块是同步的，也就是说，加载完成才可以执行后面的操作，Node.js主要用于服务器编程，模块一般都是存在本地硬盘中，加载比较快，所以Node.js采用CommonJS规范。

   ```JS
   // module.js
   let name = '';
   let sayName = function () {
     console.log(name);
   };
   
   module.exports = { name, sayName }
   
   // 或者
   exports.sayName = sayName;
   
   
   
   // 通过 require 引入依赖
   let module = require('./module.js');
   module.sayName(); // 
   ```

2. AMD——用于浏览器环境

3. CMD——用于浏览器环境

4. ES6——用于浏览器环境

   https://es6.ruanyifeng.com/#docs/module

   在 ES6 之前，社区制定了一些模块加载方案，最主要的有 CommonJS 和 AMD 两种。前者用于服务器，后者用于浏览器。ES6 在语言标准的层面上，实现了模块功能，而且实现得相当简单，完全可以取代 CommonJS 和 AMD 规范，成为浏览器和服务器通用的模块解决方案。

   ES6 模块的设计思想是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。CommonJS 和 AMD 模块，都只能在运行时确定这些东西。比如，CommonJS 模块就是对象，输入时必须查找对象属性。

   模块功能主要由两个命令构成：`export`和`import`。`export`命令用于规定模块的对外接口，`import`命令用于输入其他模块提供的功能。

   一个模块就是一个独立的文件。该文件内部的所有变量，外部无法获取。如果你希望外部能够读取模块内部的某个变量，就必须使用`export`关键字输出该变量。

   使用`export`命令定义了模块的对外接口以后，其他 JS 文件就可以通过`import`命令加载这个模块。

   ```js
   // profile.js
   export var firstName = 'Michael';
   export var lastName = 'Jackson';
   export var year = 1958;
   
   
   // profile.js
   var firstName = 'Michael';
   var lastName = 'Jackson';
   var year = 1958;
   
   export { firstName, lastName, year };
   
   
   function v1() { ... }
   function v2() { ... }
   
   export {
     v1 as streamV1,
     v2 as streamV2,
     v2 as streamLatestVersion
   };
                  
                  
                  
   // main.js
   import { firstName, lastName, year } from './profile.js';
   
   function setName(element) {
     element.textContent = firstName + ' ' + lastName;
   }
                  
   import { lastName as surname } from './profile.js';
   ```



## 异步

1. ajax

   ```js
   ajax(url, () => {
       // 处理逻辑
   })
   ```

2. promise

   ```js
   new Promise((resolve, reject) => {
       setTimeout(() => {
           resolve('foo');
       }, 300);
   
   }).then((value) => {
       console.log('resolve ' + value);
       return new Promise((resolve, reject) => {
           resolve('foo2')
       })
   }).then((value) => {
       console.log('resolve ' + value);
       return new Promise((resolve, reject) => {
           resolve('foo3')
       })
   }).then((value) => {
       console.log('resolve ' + value);
   })
   
   
   
   ```

   

3. async

   ```js
   async function myFirstAsync() {
       return "hello async"
   }
   myFirstAsync().then((result) => {
       console.log(result)
   })
   console.log(myFirstAsync());
   console.log("我是同步函数")
   ```

4. await

   **await只能放在async里面**

   ```js
   function doublenum(num) {
       return new Promise((resolve, reject) => {
           setTimeout(
               () => {
                   resolve(2 * num)
               }, 2000)
       })
   }
   async function getDoublenum() {
       const result1 = await doublenum(5)
       const result2 = await doublenum(10)
       const result3 = await doublenum(15)
       const result4 = await doublenum(20)
       const result5 = await doublenum(25)
       const result6 = await doublenum(30)
       console.log(result1 + result2 + result3 + result4 + result5 + result6)
       console.log('我写在后面，但是我先执行')
   }
   getDoublenum()
   ```

   

## 严格模式

- 变量必须声明后再使用
- 函数的参数不能有同名属性，否则报错
- 不能使用`with`语句
- 不能对只读属性赋值，否则报错
- 不能使用前缀 0 表示八进制数，否则报错
- 不能删除不可删除的属性，否则报错
- 不能删除变量`delete prop`，会报错，只能删除属性`delete global[prop]`
- `eval`不会在它的外层作用域引入变量
- `eval`和`arguments`不能被重新赋值
- `arguments`不会自动反映函数参数的变化
- 不能使用`arguments.callee`
- 不能使用`arguments.caller`
- 禁止`this`指向全局对象
- 不能使用`fn.caller`和`fn.arguments`获取函数调用的堆栈
- 增加了保留字（比如`protected`、`static`和`interface`）



























---



```js
Object.create = function (o) {
  var F = function ( ) {};
  F.prototype = o;
  return new F( );
};



Function.prototype.method = function (name, func) {
  this.prototype[name] = func;
  return this;
};
```









```js
let module = (function () {
  let privateName = 'private'; // 私有变量
  let privateFn = function () {}; // 私有函数
  
  // 对外暴露的成员
  return {
    name: '', // 公有属性
    sayName() { // 公有方法
      console.log(this.name);
    }
  }
})();

// 外部调用
module.sayName(); // 
```




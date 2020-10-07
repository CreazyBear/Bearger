# Javascript

> 收录一个小白学习记录



## 继承

https://github.com/ljianshu/Blog/issues/20

### 继承的方式

1. 原型链继承：

   1. 子类型的原型为父类型的一个实例对象。
   2. 要想为子类新增属性和方法，必须要在`Student.prototype = new Person()` 之后执行，不能放到构造器中

   ```js
   //父类型
    function Person(name, age) {
      this.name = name,
      this.age = age,
      this.play = [1, 2, 3]
      this.setName = function () {}
    }
    Person.prototype.setAge = function () {}
    //子类型
    function Student(price) {
      this.price = price
      this.setScore = function () {}
    }
   
   
   //start
   Student.prototype = new Person() // 子类型的原型为父类型的一个实例对象
   //end
   
   var s1 = new Student(15000)
    var s2 = new Student(14000)
    console.log(s1,s2)     
   ```

   **不建议使用**

   **缺点**：

   - 来自原型对象的所有属性被所有实例共享：
     - 对于引用类似，这个就有点糟糕了。其本质问题在于，这种方式是基于对象的继承。
     - 没有私有
     - 无法调用super
   - 创建子类实例时，无法向父类构造函数传参
   - 一定要使用new，不然就GG

2. 构造函数继承

   1. 在子类型构造函数中通用call()调用父类型构造函数

   ```js
     function Person(name, age) {
       this.name = name,
       this.age = age,
       this.setName = function () {}
     }
     Person.prototype.setAge = function () {}
     function Student(name, age, price) {
       Person.call(this, name, age)  // 相当于: this.Person(name, age)
       /*this.name = name
       this.age = age*/
       this.price = price
     }
     var s1 = new Student('Tom', 20, 15000)
   ```

   **特点**：

   - 解决了原型链继承中子类实例共享父类引用属性的问题
   - 创建子类实例时，可以向父类传递参数
   - 可以实现多继承(call多个父类对象)

   **缺点**：

   - 实例并不是父类的实例，只是子类的实例
   - 只能继承父类的实例属性和方法，不能继承原型属性和方法
   - 无法实现函数复用，每个子类都有父类实例函数的副本，影响性能

3. 原型链+借用构造函数的组合继承

   这个就是1+2

   ```js
   function Person (name, age) {
     this.name = name,
     this.age = age,
     this.setAge = function () { }
   }
   Person.prototype.setAge = function () {
     console.log("111")
   }
   function Student (name, age, price) {
     Person.call(this, name, age)
     this.price = price
     this.setScore = function () { }
   }
   Student.prototype = new Person()
   Student.prototype.constructor = Student//组合继承也是需要修复构造函数指向的
   Student.prototype.sayHello = function () { }
   var s1 = new Student('Tom', 20, 15000)
   var s2 = new Student('Jack', 22, 14000)
   console.log(s1)
   console.log(s1.constructor) //Student
   console.log(p1.constructor) //Person   
   ```

   **优点**：

   - 可以继承实例属性/方法，也可以继承原型属性/方法
   - 不存在引用属性共享问题
   - 可传参
   - 函数可复用

   **缺点**：

   - 调用了两次父类构造函数，生成了两份实例

4. 组合继承优化1

   这种方式通过父类原型和子类原型指向同一对象，子类可以继承到父类的公有方法当做自己的公有方法，而且不会初始化两次实例方法/属性，避免的组合继承的缺点
   
   ```js
   function Person (name, age) {
     this.name = name,
     this.age = age,
     this.setAge = function () { }
   }
   Person.prototype.setAge = function () {
     console.log("111")
   }
   function Student (name, age, price) {
     Person.call(this, name, age)
     this.price = price
     this.setScore = function () { }
   }
   Student.prototype = Person.prototype
   Student.prototype.sayHello = function () { }
   var s1 = new Student('Tom', 20, 15000)
   console.log(s1)    
   ```
   
5. 组合继承优化2

   借助原型可以基于已有的对象来创建对象，`var B = Object.create(A)`以A对象为原型，生成了B对象。**B继承了A的所有属性和方法**。

   ```js
   function Person (name, age) {
     this.name = name,
     this.age = age
   }
   Person.prototype.setAge = function () {
     console.log("111")
   }
   function Student (name, age, price) {
     Person.call(this, name, age)
     this.price = price
     this.setScore = function () { }
   }
   Student.prototype = Object.create(Person.prototype)//核心代码
   Student.prototype.constructor = Student//核心代码
   var s1 = new Student('Tom', 20, 15000)
   console.log(s1 instanceof Student, s1 instanceof Person) // true true
   console.log(s1.constructor) //Student
   console.log(s1)       
   ```

6. ES6中class 的继承

   ES6中引入了class关键字，class可以通过extends关键字实现继承，还可以通过static关键字定义类的静态方法,这比 ES5 的通过修改原型链实现继承，要清晰和方便很多。

   ES5 的继承，实质是先创造子类的实例对象this，然后再将父类的方法添加到this上面（Parent.apply(this)）。ES6 的继承机制完全不同，实质是先将父类实例对象的属性和方法，加到this上面（所以必须先调用super方法），然后再用子类的构造函数修改this。

   需要注意的是，class关键字只是原型的语法糖，JavaScript继承仍然是基于原型实现的
   
   ```js
    class Person {
       //调用类的构造方法
       constructor(name, age) {
         this.name = name
         this.age = age
       }
       //定义一般的方法
       showName () {
         console.log("调用父类的方法")
         console.log(this.name, this.age);
       }
     }
     let p1 = new Person('kobe', 39)
     console.log(p1)
     //定义一个子类
     class Student extends Person {
       constructor(name, age, salary) {
         super(name, age)//通过super调用父类的构造方法
         this.salary = salary
       }
       showName () {//在子类自身定义方法
         console.log("调用子类的方法")
         console.log(this.name, this.age, this.salary);
       }
     }
     let s1 = new Student('wade', 38, 1000000000)
     console.log(s1)
     s1.showName()     
   ```
   
   呃，有的浏览器可能不支持。

### JS的继承的本质是什么——原型

所谓继承就是从父类那里得到已有的方法和属性。那JS要获取这个，就是从原型链。然后，变来变去的。



# this

书中没有单独讲this，但它讲的是调用。感觉一下就回到了本质。我们访问this的目的也就是访问属性或者方法。但在函数中，this和我们外面的this是不是一个this。就是需要我们思考的。在OC中，有时判断循环引用有点烦，我一般会直接无脑weakify和strongify。虽然有的地方有些多余，但如果不是性能特别的挑剔，其实也无所谓。这里的this，其实，按我的风格，大概会全量that一下。=。=

但，毕竟是初学，大概还是了解一下比较好。

按书中所说的，要确认this是指向什么，需要明确函数调用方式

> 参数this在面向对象编程中非常重要，它的值取决于调用的模式。在JavaScript中一共有4种调用模式：方法调用模式、函数调用模式、构造器调用模式和apply调用模式。

1. 方法调用模式

   当一个函数被保存为对象的一个属性时，我们称它为一个方法。当一个方法被调用时，this被绑定到该对象。

2. 函数调用模式

   当一个函数并非一个对象的属性时，那么它就是被当做一个函数来调用的：以此模式调用函数时，this被绑定到全局对象。

3. 构造器调用模式

   如果在一个函数前面带上new来调用，那么背地里将会创建一个连接到该函数的prototype成员的新对象，同时this会被绑定到那个新对象上。

4. apply调用模式。

   apply方法让我们构建一个参数数组传递给调用函数。它也允许我们选择this的值。apply方法接收两个参数，第1个是要绑定给this的值，第2个就是一个参数数组。

5. 箭头函数this指向

   箭头函数没有自己的this，箭头函数的this不是调用的时候决定的，而是在定义的时候处在的对象就是它的this。换句话说，箭头函数的this看外层的是否有函数，如果有，外层函数的this就是内部箭头函数的this，如果没有，则this是window。
   
   > 个人理解，这就是把上面第2种场景下的语言本身的bug修复了一下。



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




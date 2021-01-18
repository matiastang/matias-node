<!--
 * @Author: tangdaoyong
 * @Date: 2021-01-18 11:15:34
 * @LastEditors: tangdaoyong
 * @LastEditTime: 2021-01-18 12:08:38
 * @Description: require 和 import 
-->
# require 和 import 

[require 和 import 详解](https://www.cnblogs.com/datiangou/p/10158960.html)
[彻底搞清楚javascript中的require、import和export](https://www.cnblogs.com/libin-1/p/7127481.html)

## require

特点：
1. 运行时加载
2. 拷贝到本页面
3. 全部引入

### CommonJS

Node.js就是用CommonJS思想。
在CommonJS中，有一个全局性方法require()，用于加载模块。

#### 用法

```js
var math = require('math');
math.add(2, 3);

var math = require('math');
const Math = new math(2, 3)
Math.add();
```

#### 模块写法

模块写法分`exports`和`module.exports`。

```js
exports.add = (x,y) => x+y;

module.exports = class math {
  constructor(x,y) {
    this.x = x;
    this.y = y;
  }

  add() {
    return  x+y;
  }
};
```

#### CommonJS中module.exports 与 exports的区别
Module.exports

The module.exports object is created by the Module system. Sometimes this is not acceptable; many want their module to be an instance of some class. To do this, assign the desired export object to module.exports. Note that assigning the desired object to exports will simply rebind the local exports variable, which is probably not what you want to do.

译文：module.exports对象是由模块系统创建的。 有时这是难以接受的；许多人希望他们的模块成为某个类的实例。 为了实现这个，需要将期望导出的对象赋值给module.exports。 注意，将期望的对象赋值给exports会简单地重新绑定到本地exports变量上，这可能不是你想要的。

Module.exports

The exports variable is available within a module’s file-level scope, and is assigned the value of module.exports before the module is evaluated. It allows a shortcut, so that module.exports.f = … can be written more succinctly as exports.f = …. However, be aware that like any variable, if a new value is assigned to exports, it is no longer bound to module.exports:

译文：exports变量是在模块的文件级别作用域内有效的，它在模块被执行前被赋于 module.exports 的值。它有一个快捷方式，以便 module.exports.f = … 可以被更简洁地写成exports.f = …。 注意，就像任何变量，如果一个新的值被赋值给exports，它就不再绑定到module.exports(其实是exports.属性会自动挂载到没有命名冲突的module.exports.属性)

从[Api文档](http://nodejs.cn/api/modules.html#modules_module_exports)上面的可以看出，从require导入方式去理解，关键有两个变量(全局变量module.exports，局部变量exports)、一个返回值(module.exports)

```js
function require(...) {
var module = { exports: {} };
((module, exports) => {
// 你的被引入代码 Start
// var exports = module.exports = {}; (默认都有的)
function some_func() {};
exports = some_func;
// 此时，exports不再挂载到module.exports，
// export将导出{}默认对象
module.exports = some_func;
// 此时，这个模块将导出some_func对象，覆盖exports上的some_func
// 你的被引入代码 End
})(module, module.exports);
// 不管是exports还是module.exports，最后返回的还是module.exports
return module.exports;
}
```
```js
console.log(exports); // {}
console.log(module.exports); // {}
console.log(exports === module.exports); // true
console.log(exports == module.exports); // true
console.log(module);
/**
Module {
id: '.',
exports: {},
parent: null,
filename: '/Users/larben/Desktop/demo.js',
loaded: false,
children: [],
paths:
[ '/Users/larben/Desktop/node_modules',
'/Users/larben/node_modules',
'/Users/node_modules',
'/node_modules' ] }
*/
```

**注意**

每个js文件一创建，都有一个var exports = module.exports = {},使exports和module.exports都指向一个空对象。
module.exports和exports所指向的内存地址相同
### AMD

require.js和cujo.js就是用AMD思想。

AMD是”Asynchronous Module Definition”的缩写，意思就是”异步模块定义”。它采用异步方式加载模块，模块的加载不影响它后面语句的运行。所有依赖这个模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行。

`CommonJS`是同步的所以，第二行`math.add(2, 3)`，在第一行`require('math')`之后运行，因此必须等`math.js`加载完成。也就是说，如果加载时间很长，整个应用就会停在那里等。
这对服务器端不是一个问题，因为所有的模块都存放在本地硬盘，可以同步加载完成，等待时间就是硬盘的读取时间。但是，对于浏览器，这却是一个大问题，因为模块都放在服务器端，等待时间取决于网速的快慢，可能要等很长时间，浏览器处于”假死”状态。
因此，浏览器端的模块，不能采用”同步加载”（synchronous），只能采用”异步加载”（asynchronous）。这就是AMD规范诞生的背景。

#### 用法

```js
//require([module], callback);
require(['math'], function (math) {
　math.add(2, 3);
});
```
### 模块写法

> define(id?, dependencies?, factory)

* id:字符串，模块名称(可选)
* dependencies: 是我们要载入的依赖模块(可选)，使用相对路径。,注意是数组格式
* factory: 工厂方法，返回一个模块函数

1. 一个模块不依赖其他模块写法
```js
// math.js
　　define(function (){
　　　　var add = function (x,y){
　　　　　　return x+y;
　　　　};
　　　　return {
　　　　　　add: add
　　　　};
　　});
```
2. 模块还依赖其他模块

```js
define(['a','b'], function(a,b){
　　　　function foo(){
　　　　　　a.doSomething();// 依赖前置，提前执行
　　　　　　b.doSomething();
　　　　}
　　　　return {
　　　　　　foo : foo
　　　　};
　　});
```
> 当require()函数加载上面这个模块的时候，就会先加载Lib.js文件。

### CMD

sea.js就是用CMD思想。
CMD是"Common Module Definition"的缩写。类似于requirejs，但是seajs是依赖就近，延迟执行，requirejs是依赖前置，提前执行。

#### 用法
```js
seajs.config({
  alias: {
    'jquery': 'http://modules.seajs.org/jquery/1.7.2/jquery.js'
  }
});
seajs.use(['./hello', 'jquery'], function(hello, $) {
  $('#beautiful-sea').click(hello.sayHello);
});
```

#### 模块写法

```js
define(function(require, exports, module) {
  var $ = require('jquery');

  exports.sayHello = function() {
    $('#hello').toggle('slow');
  };
   var b = require("b");
   b.doSomething();    // 依赖就近，延迟执行
});
```

### CMD与AMD区别
AMD和CMD最大的区别是对依赖模块的执行时机处理不同，而不是加载的时机或者方式不同，二者皆为异步加载模块。

AMD依赖前置，js可以方便知道依赖模块是谁，立即加载；

而CMD就近依赖，需要使用把模块变为字符串解析一遍才知道依赖了那些模块，这也是很多人诟病CMD的一点，牺牲性能来带来开发的便利性，实际上解析模块用的时间短到可以忽略。

## import

特点：
1. 编译时加载
2. 只引用定义
3. 按需加载

> 推荐用import取代require

#### 用法

import有两种模块导入方式：命名式导入（名称导入）和默认导入（定义式导入），以及 import()。

```js
import defaultMember from "module-name";
import * as name from "module-name";
import { member } from "module-name";
import { member as alias } from "module-name";
import { member1 , member2 } from "module-name";
import { member1 , member2 as alias2 , [...] } from "module-name";
import defaultMember, { member [ , [...] ] } from "module-name";
import defaultMember, * as name from "module-name";
import "module-name";
```

name－从将要导入模块中收到的导出值的名称
member, memberN－从导出模块，导入指定名称的多个成员
defaultMember－从导出模块，导入默认导出成员
alias, aliasN－别名，对指定导入成员进行的重命名
module-name－要导入的模块。是一个文件名
as－重命名导入成员名称（“标识符”）
from－从已经存在的模块、脚本文件等导入

* import()

`import()`返回一个`Promise`对象。

```js
// 报错
if (x === 2) {
  import MyModual from './myModual';
}
```
引擎处理import语句是在编译时，这时不会去分析或执行if语句，所以import语句放在if代码块之中毫无意义，因此会报句法错误，而不是执行时错误。没办法像require样根据条件动态加载。
于是[提案](https://github.com/tc39/proposal-dynamic-import)引入`import()`函数，编译时分析if语句,完成动态加载。

```js
if(x === 2){
  import('myModual').then((MyModual)=>{
    new MyModual();
  })
}
```
#### 模块写法

export有两种模块导出方式：命名式导出（名称导出）和默认导出（定义式导出），命名式导出每个模块可以多个，而默认导出每个模块仅一个。

```js
export { name1, name2, …, nameN };
export { variable1 as name1, variable2 as name2, …, nameN };
export let name1, name2, …, nameN; // also var
export let name1 = …, name2 = …, …, nameN; // also var, const
 
export default expression;
export default function (…) { … } // also class, function*
export default function name1(…) { … } // also class, function*
export { name1 as default, … };
 
export * from …;
export { name1, name2, …, nameN } from …;
export { import1 as name1, import2 as name2, …, nameN } from …;
```
name1… nameN－导出的“标识符”。导出后，可以通过这个“标识符”在另一个模块中使用* import引用
default－设置模块的默认导出。设置后import不通过“标识符”而直接引用默认导入
－继承模块并导出继承模块所有的方法和属性
as－重命名导出“标识符”
from－从已经存在的模块、脚本文件…导出
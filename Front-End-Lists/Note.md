<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [JavaScript](#javascript)
  - [7种语言类型](#7种语言类型)
  - [装箱操作](#装箱操作)
  - [拆箱操作](#拆箱操作)
  - [面向对象](#面向对象)
    - [JavaScript的对象特征](#javascript的对象特征)
    - [JavaScript 对象的两类属性](#javascript-对象的两类属性)
  - [什么是原型？](#什么是原型)
    - [JavaScript的原型](#javascript的原型)
    - [ES6 中的类](#es6-中的类)
  - [JavaScript中的对象的分类](#javascript中的对象的分类)
    - [宿主对象](#宿主对象)
    - [内置对象·固有对象](#内置对象固有对象)
      - [内置对象·原生对象](#内置对象原生对象)
  - [JavaScript的执行](#javascript的执行)
    - [宏观和微观任务](#宏观和微观任务)
    - [Promise](#promise)
    - [如何分析异步执行的顺序](#如何分析异步执行的顺序)
    - [新特性：async/await](#新特性asyncawait)
    - [闭包](#闭包)
    - [函数](#函数)
    - [this 关键字的行为](#this-关键字的行为)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

<!--
 * @Author: zhy
 * @Date: 2020-11-10 11:04:16
 * @LastEditTime: 2020-11-20 21:13:44
-->
# JavaScript
## 7种语言类型
1. Undefined
   > 为什么有的编程规范要求用 void 0 代替 undefined？
   - Undefined 类型表示未定义，它的类型只有一个值，就是 undefined。任何变量在赋值前是 Undefined 类型、值为 undefined，一般我们可以用全局变量 undefined（就是名为 undefined 的这个变量）来表达这个值，或者 void 运算来把任意一个表达式变成 undefined 值。
   - 但是呢，因为 JavaScript 的代码 undefined 是一个变量，而并非是一个关键字，这是 JavaScript 语言公认的设计失误之一，所以，我们为了避免无意中被篡改，所以建议使用 void 0 来获取 undefined 值。

2. Null
   > - Undefined 跟 Null 有一定的表意差别，Null 表示的是：“定义了但是为空”。所以，在实际编程时，我们一般不会把变量赋值为 undefined，这样可以保证所有值为 undefined 的变量，都是从未赋值的自然状态。
   > - Null 类型也只有一个值，就是 null，它的语义表示空值，与 undefined 不同，null 是 JavaScript 关键字，所以在任何代码中，你都可以放心用 null 关键字来获取 null 值。
3. Boolean
4. String
   > 字符串是否有最大长度 ?
   - String 用于表示文本数据。String 有最大长度是 2^53 - 1，这在一般开发中都是够用的，但是有趣的是，这个所谓最大长度，并不完全是你理解中的字符数。
   - 因为 String 的意义并非“字符串”，而是字符串的 UTF16 编码，我们字符串的操作 charAt、charCodeAt、length 等方法针对的都是 UTF16 编码。所以，字符串的最大长度，实际上是受字符串的编码长度影响的。
5. Number
   > - Number 类型表示我们通常意义上的“数字”。这个数字大致对应数学中的有理数，当然，在计算机中，我们有一定的精度限制。
   > - JavaScript 中的 Number 类型有 18437736874454810627(即 2^64-2^53+3) 个值。
   > - JavaScript 中的 Number 类型基本符合 IEEE 754-2008 规定的双精度浮点数规则，但是 JavaScript 为了表达几个额外的语言场景（比如不让除以 0 出错，而引入了无穷大的概念），规定了几个例外情况：NaN，占用了 9007199254740990，这原本是符合 IEEE 规则的数字；
   > - Infinity，无穷大；
   > - -Infinity，负无穷大。
   > - 另外，值得注意的是，JavaScript 中有 +0 和 -0，在加法类运算中它们没有区别，但是除法的场合则需要特别留意区分，“忘记检测除以 -0，而得到负无穷大”的情况经常会导致错误，而区分 +0 和 -0 的方式，正是检测 1/x 是 Infinity 还是 -Infinity。
   > - 同样根据浮点数的定义，非整数的 Number 类型无法用 ==（=== 也不行） 来比较，一段著名的代码，这也正是我们第三题的问题，为什么在 JavaScript 中，0.1+0.2 不能 =0.3。小数在转化为二进制时，通过小数部分不停乘2取整，直到小数部分为0. 这种方式导致一些小数乘2永远做不到小数部分为0，只能取一定精度的值表示，进而导致精度误差的出现。
```
   console.log( 0.1 + 0.2 == 0.3);
```
这里输出的结果是 false，说明两边不相等的，这是浮点运算的特点，也是很多同学疑惑的来源，浮点数运算的精度问题导致等式左右的结果并不是严格相等，而是相差了个微小的值。所以实际上，这里错误的不是结论，而是比较的方法，正确的比较方法是使用 JavaScript 提供的最小精度值：
```
   console.log( Math.abs(0.1 + 0.2 - 0.3) <= Number.EPSILON);
```
检查等式左右两边差的绝对值是否小于最小精度，才是正确的比较浮点数的方法。这段代码结果就是 true 了。
6. Symbol
   > Symbol 是 ES6 中引入的新类型，它是一切非字符串的对象 key 的集合，在 ES6 规范中，整个对象系统被用 Symbol 重塑。
7. Object
   > 在 JavaScript 中，对象的定义是“属性的集合”。属性分为数据属性和访问器属性，二者都是 key-value 结构，key 可以是字符串或者 Symbol 类型。

## 装箱操作
> 每一种基本类型 Number、String、Boolean、Symbol 在对象中都有对应的类，所谓装箱转换，正是把基本类型转换为对应的对象，它是类型转换中一种相当重要的种类。

## 拆箱操作
> 在 JavaScript 标准中，规定了 ToPrimitive 函数，它是对象类型到基本类型的转换（即，拆箱转换）。
> 对象到 String 和 Number 的转换都遵循“先拆箱再转换”的规则。通过拆箱转换，把对象变成基本类型，再从基本类型转换为对应的 String 或者 Number。

## 面向对象
### JavaScript的对象特征
- 对象具有唯一标识性：一般而言，各种语言的对象唯一标识性都是用内存地址来体现的， 对象具有唯一标识的内存地址，所以具有唯一的标识。所以，JavaScript 程序员都知道，任何不同的 JavaScript 对象其实是互不相等的。
- 关于对象的第二个和第三个特征“状态和行为”，不同语言会使用不同的术语来抽象描述它们，比如 C++ 中称它们为“成员变量”和“成员函数”，Java 中则称它们为“属性”和“方法”。在 JavaScript 中，将状态和行为统一抽象为“属性”。
- **对象具有高度的动态性，这是因为 JavaScript 赋予了使用者在运行时为对象添改状态和行为的能力。**

### JavaScript 对象的两类属性
- 对 JavaScript 来说，属性并非只是简单的名称和值，JavaScript 用一组特征（attribute）来描述属性（property）。
- 先来说第一类属性，数据属性。它比较接近于其它语言的属性概念。
- 数据属性具有四个特征。value：就是属性的值。writable：决定属性能否被赋值。enumerable：决定 for in 能否枚举该属性。configurable：决定该属性能否被删除或者改变特征值。在大多数情况下，我们只关心数据属性的值即可。
- 第二类属性是访问器（getter/setter）属性，它也有四个特征。getter：函数或 undefined，在取属性值时被调用。setter：函数或 undefined，在设置属性值时被调用。enumerable：决定 for in 能否枚举该属性。configurable：决定该属性能否被删除或者改变特征值。访问器属性使得属性在读和写时执行代码，它允许使用者在写和读属性时，得到完全不同的值，它可以视为一种函数的语法糖。
- 可以使用内置函数 getOwnPropertyDescripter 来查看
- 可以使用 Object.defineProperty，改变属性的特征，或者定义访问器属性。

## 什么是原型？
- 原型是顺应人类自然思维的产物。中文中有个成语叫做“照猫画虎”，这里的猫看起来就是虎的原型，所以，由此我们可以看出，用原型来描述对象的方法可以说是古已有之。
- “基于类”的编程提倡使用一个关注分类和类之间关系开发模型。在这类语言中，总是先有类，再从类去实例化一个对象。类与类之间又可能会形成继承、组合等关系。类又往往与语言的类型系统整合，形成一定编译时的能力。
- 与此相对，“基于原型”的编程看起来更为提倡程序员去关注一系列对象实例的行为，而后才去关心如何将这些对象，划分到最近的使用方式相似的原型对象，而不是将它们分成类。

### JavaScript的原型
- 如果所有对象都有私有字段[[prototype]]，就是对象的原型；
- 读一个属性，如果对象本身没有，则会继续访问对象的原型，直到原型为空或者找到为止。
  
### ES6 中的类
- 类提供了继承能力
```
class Animal { 
  constructor(name) {
    this.name = name;
  }
  
  speak() {
    console.log(this.name + ' makes a noise.');
  }
}

class Dog extends Animal {
  constructor(name) {
    super(name); // call the super class constructor and pass in the name parameter
  }

  speak() {
    console.log(this.name + ' barks.');
  }
}

let d = new Dog('Mitzie');
d.speak(); // Mitzie barks.
```

## JavaScript中的对象的分类
> 我们可以把对象分成几类。
> - 宿主对象（host Objects）：由 JavaScript 宿主环境提供的对象，它们的行为完全由宿主环境决定。
> - 内置对象（Built-in Objects）：由 JavaScript 语言提供的对象。
>  - 固有对象（Intrinsic Objects ）：由标准规定，随着 JavaScript 运行时创建而自动创建的对象实例。
>  - 原生对象（Native Objects）：可以由用户通过 Array、RegExp 等内置构造器或者特殊语法创建的对象。
>  - 普通对象（Ordinary Objects）：由{}语法、Object 构造器或者 class 关键字定义类创建的对象，它能够被原型继承。

### 宿主对象
> JavaScript 宿主对象千奇百怪，但是前端最熟悉的无疑是浏览器环境中的宿主了。在浏览器环境中，我们都知道全局对象是 window，window 上又有很多属性，如 document。实际上，这个全局对象 window 上的属性，一部分来自 JavaScript 语言，一部分来自浏览器环境。JavaScript 标准中规定了全局对象属性，W3C 的各种标准中规定了 Window 对象的其它属性。宿主对象也分为固有的和用户可创建的两种，比如 document.createElement 就可以创建一些 DOM 对象。宿主也会提供一些构造器，比如我们可以使用 new Image 来创建 img 元素

### 内置对象·固有对象
> 固有对象是由标准规定，随着 JavaScript 运行时创建而自动创建的对象实例.
> 它们通常扮演者类似基础库的角色。我们前面提到的“类”其实就是固有对象的一种.

#### 内置对象·原生对象
> 把 JavaScript 中，能够通过语言本身的构造器创建的对象称作原生对象
   
## JavaScript的执行
> 当拿到一段 JavaScript 代码时，浏览器或者 Node 环境首先要做的就是；传递给 JavaScript 引擎，并且要求它去执行。
> 然而，执行 JavaScript 并非一锤子买卖，宿主环境当遇到一些事件时，会继续把一段代码传递给 JavaScript 引擎去执行，此外，我们可能还会提供 API 给 JavaScript 引擎，比如 setTimeout 这样的 API，它会允许 JavaScript 在特定的时机执行。
> 所以，我们首先应该形成一个感性的认知：一个 JavaScript 引擎会常驻于内存中，它等待着我们（宿主）把 JavaScript 代码或者函数传递给它执行。
> **在 ES5 之后，JavaScript 引入了 Promise，这样，不需要浏览器的安排，JavaScript 引擎本身也可以发起任务了。**

### 宏观和微观任务
> 在宏观任务中，JavaScript 的 Promise 还会产生异步代码，JavaScript 必须保证这些异步代码在一个宏观任务中完成，因此，每个宏观任务中又包含了一个微观任务队列
> 有了宏观任务和微观任务机制，我们就可以实现 JavaScript 引擎级和宿主级的任务了，例如：Promise 永远在队列尾部添加微观任务。setTimeout 等宿主 API，则会添加宏观任务。

### Promise
> Promise 是 JavaScript 语言提供的一种标准化的异步管理方式，它的总体思想是，需要进行 io、等待或者其它异步操作的函数，不返回真实结果，而返回一个“承诺”，函数的调用方可以在合适的时机，选择等待这个承诺兑现（通过 Promise 的 then 方法的回调）。

**Promise 的 then 回调是一个异步的执行过程，下面我们就来研究一下 Promise 函数中的执行顺序，我们来看一段代码示例：**
```
    var r = new Promise(function(resolve, reject){
        console.log("a");
        resolve()
    });
    r.then(() => console.log("c"));
    console.log("b")
```
> 我们执行这段代码后，注意输出的顺序是 a b c。在进入 console.log(“b”) 之前，毫无疑问 r 已经得到了 resolve，但是 Promise 的 resolve 始终是异步操作，所以 c 无法出现在 b 之前。

**接下来我们试试跟 setTimeout 混用的 Promise。在这段代码中，我设置了两段互不相干的异步操作：通过 setTimeout 执行 console.log(“d”)，通过 Promise 执行 console.log(“c”)。**

```
    var r = new Promise(function(resolve, reject){
        console.log("a");
        resolve()
    });
    setTimeout(()=>console.log("d"), 0)
    r.then(() => console.log("c"));
    console.log("b")
```
> 我们发现，不论代码顺序如何，d 必定发生在 c 之后，**因为 Promise 产生的是 JavaScript 引擎内部的微任务，而 setTimeout 是浏览器 API，它产生宏任务。**

### 如何分析异步执行的顺序
- 首先我们分析有多少个宏任务；
- 在每个宏任务中，分析有多少个微任务；
- 根据调用次序，确定宏任务中的微任务执行次序；
- 根据宏任务的触发规则和调用次序，确定宏任务的执行次序；
- 确定整个顺序

**稍微复杂的例子，这是一段非常常用的封装方法，利用 Promise 把 setTimeout 封装成可以用于异步的函数。**
```
    function sleep(duration) {
        return new Promise(function(resolve, reject) {
            console.log("b");
            setTimeout(resolve,duration);
        })
    }
    console.log("a");
    sleep(5000).then(()=>console.log("c"));
```
> 我们首先来看，setTimeout 把整个代码分割成了 2 个宏观任务，这里不论是 5 秒还是 0 秒，都是一样的。
> 第一个宏观任务中，包含了先后同步执行的 console.log(“a”); 和 console.log(“b”);。
> setTimeout 后，第二个宏观任务执行调用了 resolve，然后 then 中的代码异步得到执行，所以调用了 console.log(“c”)，最终输出的顺序才是： a b c。
> Promise 是 JavaScript 中的一个定义，但是实际编写代码时，我们可以发现，它似乎并不比回调的方式书写更简单，但是从 ES6 开始，我们有了 async/await，这个语法改进跟 Promise 配合，能够有效地改善代码结构。

### 新特性：async/await
> async 函数必定返回 Promise，我们把所有返回 Promise 的函数都可以认为是异步函数

### 闭包
> 闭包翻译自英文单词 closure，这是个不太好翻译的词，在计算机领域，它就有三个完全不相同的意义：编译原理中，它是处理语法产生式的一个步骤；计算几何中，它表示包裹平面点集的凸多边形（翻译作凸包）；而在编程语言领域，它表示一种函数。
> 可以简单理解一下，闭包其实只是一个绑定了执行环境的函数，这个函数并不是印在书本里的一条简单的表达式，闭包与普通函数的区别是，它携带了执行的环境，就像人在外星中需要自带吸氧的装备一样，这个函数也带有在程序中生存的环境。

### 函数
- 普通函数：**用 function 关键字定义的函数**
- 箭头函数：**用 => 运算符定义的函数**
- 方法：**在 class 中定义的函数**
- 生成器函数：**用 function * 定义的函数** ``` function* foo(){ // code} ```
- 类：**用 class 定义的类，实际上也是函数。**
- 异步函数：**普通函数、箭头函数和生成器函数加上 async 关键字。**

### this 关键字的行为

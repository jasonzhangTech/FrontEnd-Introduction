<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [JavaScript](#javascript)
  - [7种语言类型](#7种语言类型)
  - [装箱操作](#装箱操作)
  - [拆箱操作](#拆箱操作)
  - [面向对象](#面向对象)
    - [JavaScript的对象特征](#javascript的对象特征)
    - [JavaScript 对象的两类属性](#javascript-对象的两类属性)
  - [](#)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

<!--
 * @Author: zhy
 * @Date: 2020-11-10 11:04:16
 * @LastEditTime: 2020-11-10 16:23:20
-->
# JavaScript
## 7种语言类型
1. Undefined
   > 为什么有的编程规范要求用 void 0 代替 undefined？
   - Undefined 类型表示未定义，它的类型只有一个值，就是 undefined。任何变量在赋值前是 Undefined 类型、值为 undefined，一般我们可以用全局变量 undefined（就是名为 undefined 的这个变量）来表达这个值，或者 void 运算来把任意一个表达式变成 undefined 值。
   - 但是呢，因为 JavaScript 的代码 undefined 是一个变量，而并非是一个关键字，这是 JavaScript 语言公认的设计失误之一，所以，我们为了避免无意中被篡改，所以建议使用 void 0 来获取 undefined 值。

2. Null
   > - Undefined 跟 Null 有一定的表意差别，Null 表示的是：“定义了但是为空”。所以，在实际编程时，我们一般不会把变量赋值为 undefined，这样可以保证所有值为 undefined 的变量，都是从未赋值的自然状态。
   - Null 类型也只有一个值，就是 null，它的语义表示空值，与 undefined 不同，null 是 JavaScript 关键字，所以在任何代码中，你都可以放心用 null 关键字来获取 null 值。
3. Boolean
4. String
   > 字符串是否有最大长度 ?
   - String 用于表示文本数据。String 有最大长度是 2^53 - 1，这在一般开发中都是够用的，但是有趣的是，这个所谓最大长度，并不完全是你理解中的字符数。
   - 因为 String 的意义并非“字符串”，而是字符串的 UTF16 编码，我们字符串的操作 charAt、charCodeAt、length 等方法针对的都是 UTF16 编码。所以，字符串的最大长度，实际上是受字符串的编码长度影响的。
5. Number
   > - Number 类型表示我们通常意义上的“数字”。这个数字大致对应数学中的有理数，当然，在计算机中，我们有一定的精度限制。
   - JavaScript 中的 Number 类型有 18437736874454810627(即 2^64-2^53+3) 个值。
   - JavaScript 中的 Number 类型基本符合 IEEE 754-2008 规定的双精度浮点数规则，但是 JavaScript 为了表达几个额外的语言场景（比如不让除以 0 出错，而引入了无穷大的概念），规定了几个例外情况：NaN，占用了 9007199254740990，这原本是符合 IEEE 规则的数字；
   - Infinity，无穷大；
   - -Infinity，负无穷大。
   - 另外，值得注意的是，JavaScript 中有 +0 和 -0，在加法类运算中它们没有区别，但是除法的场合则需要特别留意区分，“忘记检测除以 -0，而得到负无穷大”的情况经常会导致错误，而区分 +0 和 -0 的方式，正是检测 1/x 是 Infinity 还是 -Infinity。
   - 同样根据浮点数的定义，非整数的 Number 类型无法用 ==（=== 也不行） 来比较，一段著名的代码，这也正是我们第三题的问题，为什么在 JavaScript 中，0.1+0.2 不能 =0.3。小数在转化为二进制时，通过小数部分不停乘2取整，直到小数部分为0. 这种方式导致一些小数乘2永远做不到小数部分为0，只能取一定精度的值表示，进而导致精度误差的出现。
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

## 
   
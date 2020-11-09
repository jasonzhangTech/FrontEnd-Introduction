<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [函数式编程](#%E5%87%BD%E6%95%B0%E5%BC%8F%E7%BC%96%E7%A8%8B)
  - [几个核心概念](#%E5%87%A0%E4%B8%AA%E6%A0%B8%E5%BF%83%E6%A6%82%E5%BF%B5)
  - [CAUTION](#caution)
  - [纯函数(Pure Functions)](#%E7%BA%AF%E5%87%BD%E6%95%B0pure-functions)
  - [柯里化(Currying) & 函数组合(Compose)](#%E6%9F%AF%E9%87%8C%E5%8C%96currying--%E5%87%BD%E6%95%B0%E7%BB%84%E5%90%88compose)
    - [柯里化(Currying)](#%E6%9F%AF%E9%87%8C%E5%8C%96currying)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

<!--
 * @Author: zhy
 * @Date: 2020-11-09 10:24:47
 * @LastEditTime: 2020-11-09 11:48:59
-->
# 函数式编程
> **函数式编程**强调的是如何通过函数的组合变换去解决问题，而不是我通过写什么样的语句去解决问题，当你的代码越来越多的时候，这种函数的拆分和组合就会产生出强大的力量

## 几个核心概念
1. 数据不可变： 它要求你所有的数据都是不可变的，这意味着如果你想修改一个对象，那你应该创建一个新的对象用来修改，而不是修改已有的对象。
2. 无状态： 主要是强调对于一个函数，不管你何时运行，它都应该像第一次运行一样，给定相同的输入，给出相同的输出，完全不依赖外部状态的变化。

## CAUTION
在 JS 中，我们经常可以看到下面这种对 map 的 “错误” 用法，把 map 当作一个循环语句，然后去直接修改数组中的值
``` const list = [...];
// 修改 list 中的 type 和 age
list.map(item => {
  item.type = 1;
  item.age++;
}) 
```


这样函数最主要的输出功能没有了，变成了直接修改了外部变量，这就是它的副作用。而没有副作用的写法应该是：
``` const list = [...];
// 修改 list 中的 type 和 age
const newList = list.map(item => ({...item, type: 1, age:item.age + 1})); 
```
**保证函数没有副作用，一来能保证数据的不可变性，二来能避免很多因为共享状态带来的问题。当你一个人维护代码时候可能还不明显，但随着项目的迭代，项目参与人数增加，大家对同一变量的依赖和引用越来越多，这种问题会越来越严重。**

## 纯函数(Pure Functions)
> 1. 不依赖外部状态（无状态）： 函数的的运行结果不依赖全局变量，this 指针，IO 操作等。

> 2. 没有副作用（数据不变）： 不修改全局变量，不修改入参。

> 所以纯函数才是真正意义上的 “函数”， 它意味着**相同的输入，永远会得到相同的输出**。

## 柯里化(Currying) & 函数组合(Compose)
> 如果说函数式编程中有两种操作是必不可少的那无疑就是柯里化（Currying）和函数组合（Compose），柯里化其实就是流水线上的加工站，函数组合就是我们的流水线，它由多个加工站组成。

### 柯里化(Currying)
> 柯里化的意思是将一个多元函数，转换成一个依次调用的单元函数.
### 函数组合(Compose)
> 函数组合的目的是将多个函数组合成一个函数。
下面来看一个简化版的实现：
```
const compose = (f, g) => x => f(g(x))

const f = x => x + 1;
const g = x => x * 2;
const fg = compose(f, g);
fg(1) //3
```

## 实战
假设我现在有一套数据：
```
const data = [
  {
    name: 'Peter',
    sex: 'M',
    age: 18,
    grade: 99
  },
  ……
]
```

实现以下几个常用功能：

1. 获取所有年龄小于 18 岁的对象，并返回他们的名称和年龄。
2. 查找所有男性用户。
3. 更新一个指定名称用户的成绩（不影响原数组）。
4. 取出成绩最高的 10 名，并返回他们的名称和分数。

这边提供一下 Ramda 库中的参考函数：
```
// 对象操作（最后一个参数是对象），均会返回新的对象拷贝
R.prop('name')    // 获取对象 name 字段的值
R.propEq('name', '123')   // 判断对象 name 字段是否等于‘123’
R.assoc('name', '123')   // 更新对象的'name'的值为'123'
R.pick(['a', 'd']); //=> {a: 1, d: 4}  // 获取对象某些属性，如果对应属性不存在则不返回
R.pickAll(['a', 'd']); //=> {a: 1, d: 4}  // 获取对象某些属性，如果对应属性不存在则返回`key : undefined`

// 数组操作
R.map(func)  // 传统的 map 操作
R.filter(func)  // 传统的 filter 操作
R.reject(func)  // filter 的补集
R.take(n)    // 取出数组前 n 个元素

// 比较操作
R.equals(a, b)  // 判断 b 是否等于 a 
R.gt(2, 1) => true  // 判断第一个参数是否大于第二个参数
R.lt(2, 1) => false // 判断第一个参数是否小于第二个参数

// 排序操作
R.sort(func)    // 根据某个排序函数排序
R.ascend(func)    // 根据 func 转换后的值，生成一个升序比较函数
R.descend(func)    // 根据 func 转换后的值，生成一个降序比较函数
// 例子：
R.sort(R.ascend(R.prop('age')))  // 根据 age 进行升序排序 

// 必备函数
R.pipe()   //compose 的反向，从前往后组合
R.compose()  // 从后到前组合
R.curry()  // 柯里化

```

## 参考
```
const expectData = {
  ageLt18: [
    { name: "Uktg", age: 17 },
    { name: "3U5", age: 17 },
    { name: "Hu8M", age: 17 },
    { name: "Z2d", age: 17 },
    { name: "n0ap", age: 17 },
    { name: "ptTc", age: 17 },
    { name: "Hqn", age: 17 },
    { name: "QC3", age: 17 }
  ],
  males: [
    { sex: "M", name: "vyo", age: 18, grade: 72 },
    { sex: "M", name: "Uktg", age: 17, grade: 86 },
    { sex: "M", name: "3U5", age: 17, grade: 82 },
    { sex: "M", name: "OVfZ", age: 19, grade: 54 },
    { sex: "M", name: "5Rx", age: 18, grade: 92 },
    { sex: "M", name: "8GPC", age: 18, grade: 79 },
    { sex: "M", name: "Z2d", age: 17, grade: 82 },
    { sex: "M", name: "QyKY", age: 19, grade: 59 },
    { sex: "M", name: "VT1", age: 19, grade: 89 },
    { sex: "M", name: "ptTc", age: 17, grade: 65 },
    { sex: "M", name: "Xvhf", age: 18, grade: 65 },
    { sex: "M", name: "wOR", age: 19, grade: 78 },
    { sex: "M", name: "Hqn", age: 17, grade: 74 }
  ],
  top10: [
    { name: "5Rx", grade: 92 },
    { name: "VT1", grade: 89 },
    { name: "gZW", grade: 87 },
    { name: "Uktg", grade: 86 },
    { name: "3U5", grade: 82 },
    { name: "Z2d", grade: 82 },
    { name: "NqXw", grade: 79 },
    { name: "8GPC", grade: 79 },
    { name: "wOR", grade: 78 },
    { name: "jXgh", grade: 77 }
  ]
};

const data = [
  {
    sex: "M",
    name: "vyo",
    age: 18,
    grade: 72
  },
  {
    sex: "M",
    name: "Uktg",
    age: 17,
    grade: 86
  },
  {
    sex: "F",
    name: "gZW",
    age: 18,
    grade: 87
  },
  {
    sex: "F",
    name: "NqXw",
    age: 19,
    grade: 79
  },
  {
    sex: "M",
    name: "3U5",
    age: 17,
    grade: 82
  },
  {
    sex: "M",
    name: "OVfZ",
    age: 19,
    grade: 54
  },
  {
    sex: "F",
    name: "Hu8M",
    age: 17,
    grade: 62
  },
  {
    sex: "M",
    name: "5Rx",
    age: 18,
    grade: 92
  },
  {
    sex: "M",
    name: "8GPC",
    age: 18,
    grade: 79
  },
  {
    sex: "M",
    name: "Z2d",
    age: 17,
    grade: 82
  },
  {
    sex: "M",
    name: "QyKY",
    age: 19,
    grade: 59
  },
  {
    sex: "M",
    name: "VT1",
    age: 19,
    grade: 89
  },
  {
    sex: "F",
    name: "n0ap",
    age: 17,
    grade: 58
  },
  {
    sex: "M",
    name: "ptTc",
    age: 17,
    grade: 65
  },
  {
    sex: "M",
    name: "Xvhf",
    age: 18,
    grade: 65
  },
  {
    sex: "F",
    name: "uMe1",
    age: 18,
    grade: 56
  },
  {
    sex: "F",
    name: "jXgh",
    age: 18,
    grade: 77
  },
  {
    sex: "M",
    name: "wOR",
    age: 19,
    grade: 78
  },
  {
    sex: "M",
    name: "Hqn",
    age: 17,
    grade: 74
  },
  {
    sex: "F",
    name: "QC3",
    age: 17,
    grade: 59
  }
];
const { curry, pipe, filter, prop } = R;
// code here
/*
 *  ex1 
 */
// :: String -> Number -> Object -> Boolean
const propLt = curry((p, c) => R.pipe(R.prop(p), R.lt(R.__, c)));

// :: Object ->  Boolean
const ageUnder18 = propLt("age", 18);

// :: [a] -> b 
const getAgeUnder18 = R.pipe(
  R.filter(ageUnder18),
  R.map(R.pickAll(["name", "age"]))
);

/*
 *  ex2
 */

// :: String -> [a] -> [a]
const getSex = sex => R.filter((R.propEq("sex", sex)));
// :: [a] -> [a]
const getMales = getSex("M");
const getFemales = getSex("F");

/*
 *  ex3 
 */

// :: String -> (String -> String -> a -> Object -> Object)
const updatePropBy = curry((byProp, updateProp, match, newValue, obj) => 
                           R.when(R.propEq(byProp, match), R.assoc(updateProp, newValue))(obj));

// 如果上面的理解有困难，可以类比到下面这个函数：
const updatePropBy_2 = curry((byProp, updateProp, match, newValue, obj) => {
  if (obj[byProp] === match) {
    let newObj = {...obj };
    newObj[updateProp] = newValue;
    return newObj;
  }
  return ({...obj})
});

                           
// :: String -> (String -> a -> Object -> Object)
const updatePropByName = updatePropBy('name');

// :: String -> (a -> Object -> Object)
const updateGradeByName = updatePropByName('grade');
const updateAgeByName = updatePropByName('age');

// :: String -> Number -> [Object] -> [Object]
const updateUsersGradeByName = curry((name, value, arr) => R.map(updateGradeByName(name, value), arr));
// :: String -> Number -> [Object] -> [Object]
const updateUsersAgeByName = curry((name, age, arr) => R.map(updateAgeByName(name, age), arr));


/*
 *  ex4
 */

// :: String -> [a] -> [a]
const desc = x => R.sort(R.descend(R.prop(x)));

// :: [a] -> [b]
const getGradeTop10 = R.pipe(
  desc("grade"),
  R.take(10),
  R.map(R.pickAll(["name", "grade"]))
);



mocha.setup("bdd");
var assert = chai.assert;

describe("Test", function() {
  it("1. 获取所有年龄小于18岁的对象，并返回他们的名称和年龄", function() {
    const result = getAgeUnder18(data);
    assert.deepEqual(result, expectData.ageLt18);
  });

  it("2. 查找所有男性用户", function() {
    const result = getMales(data);
    assert.deepEqual(result, expectData.males);
  });
  
  
  it("3. 更新一个指定名称用户的成绩", function() {
    const result = updateUsersGradeByName('QC3', 100, data);
    const getGrade = R.pipe(R.find(R.propEq('name', 'QC3')), R.prop('grade'));
    assert.deepEqual(getGrade(result), 100);
  });
  

  it("4. 取出成绩最高的10名，并返回他们的名称和分数", function() {
    const result = getGradeTop10(data);
    assert.deepEqual(result, expectData.top10);
  });

});

mocha.run();

```
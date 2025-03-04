[ECMAScript 2016 新特性](https://github.com/tc39/proposals/blob/master/finished-proposals.md)

### Array.prototype.includes

用来检查数组中是否包含指定成员，在这之前想要检查某个数组当中是否存在某个指定的成员，需要使用`indexOf()`方法来实现。

```js
const arr = ["tom", "jack", NaN, false];
console.log(arr.indexOf("jack")); // 1
console.log(arr.indexOf("foo")); // -1
```

从打印的结果可以看到，`indexOf()`方法返回成员在数组中的索引值，当没有找到指定成员时，返回`-1`。

但是这种情况存在一个问题，就是不能查找数组当中为`NaN`的成员。

```js
const arr = ["tom", "jack", NaN, false];
console.log(arr.indexOf(NaN)); // -1
```

ECMAScript 2016 为数组新增的 `includes()` 方法就可以直接检查数组当中是否存在某个成员，这个方法返回一个 Boolean 值来表示存在还是不存在， `同时也支持NaN的查找`。

```js
const arr = ["tom", "jack", NaN, false];
console.log(arr.includes(NaN)); // true
console.log(arr.includes("foo")); // false
```

### Exponentiation operator

ECMAScript 2016 除了新增 includes()外，还新增了指数运算符。在这之前想要进行指数计算的话，需要借助于`Math`对象的`pow()`方法来实现。

```js
console.log(Math.pow(2, 3)); // 8
```

新增的指数运算符就像加减乘除运算符一样，直接在语言层面直接进行指数计算。

```js
console.log(2 ** 3); // 8
```

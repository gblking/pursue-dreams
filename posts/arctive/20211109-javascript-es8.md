[ECMAScript 2017 新特性](https://github.com/tc39/proposals/blob/master/finished-proposals.md)

### Object.values / Object.entries

```js
const obj = { x: 1, y: 2 };
console.log(Object.keys(obj)); // ['x', 'y']
```

```js
const obj = { x: 1, y: 2 };
console.log(Object.values(obj)); // [1, 2]
console.log(Object.entries(obj)); // [['x', 1], ['y', 2]]
```

### String padding

```js
const str = "abc";
// padStart
console.log(str.padStart(5, "d")); // ddabc
console.log(str.padStart(5, "defg")); // deabc
console.log(str.padStart(2, "defg")); // abc

// padEnd
console.log(str.padEnd(5, "d")); // abcdd
console.log(str.padEnd(5, "defg")); // abcde
console.log(str.padEnd(2, "defg")); // abc
```

### Object.getOwnPropertyDescriptors

这个方法返回一个对象的所有属性，包括 `getter` 里的 `get` 和 `setter` 里的 `set` 。之所以添加这个属性是为了让对象可以允许复制和克隆一个对象到另一个对象，包括 `getter` 和 `setter` 函数。和 `Object.assign` 相对。

`Object.assign` 是浅拷贝所有属性和值，不包括 `getter` 和 `setter` 函数。

### Trailing commas in function parameter lists and calls

允许在参数尾部添加逗号

```js
function clownPuppiesEverywhere(
  param1,
  param2 // 这里可以用逗号结尾
) {
  /* ... */
}

clownPuppiesEverywhere(
  "foo",
  "bar" // 这里可以用逗号结尾
);
```

### Async functions

```js
async function f1() {
  const v1 = await f2();
  return v1;
}

async function f2() {
  return 1;
}

f1().then((v) => console.log(v));
```

1. async function 的返回值是一个 promise，async function 的返回值就是 promise resolved 的值，async function 如果抛异常，则异常值就是 promise rejected 的值。
2. await 接受一个 promise 作为参数，如果 await 后面的值不是 promise，会转换一个 resolved 的 promise。
3. 箭头函数 也支持 async，称为 async arrow function，例如:

```js
const f = async () => { ... };
```

### Shared memory and atomics

```js
// index.js

const worker = new Worker("./worker.js");

const buffer = new SharedArrayBuffer(Int32Array.BYTES_PER_ELEMENT * 10);
var ia = new Int32Array(buffer);

worker.postMessage(ia);

setTimeout(() => {
  Atomics.store(ia, 0, 100);
  Atomics.wake(ia, 0, 1);
}, 1 * 1000);
```

```js
// worker.js

let ia;
onmessage = ({ data }) => {
  ia = data;
  console.log(data); // Int32Array(10) [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]

  Atomics.wait(ia, 0, 0);

  // 1s 后
  console.log(ia); // Int32Array(10) [100, 0, 0, 0, 0, 0, 0, 0, 0, 0]
};
```

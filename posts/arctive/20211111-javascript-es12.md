### String.prototype.replaceAll

字符串替换

```js
const str = "Stay Hungry. Stay Foolish.";
const newStr = str.replace(/Stay/g, "Always");
const newStr = str.replaceAll("Stay ", "Always");

console.log(newStr); // Always Hungry. Always Foolish.
```

### Promise.any

- `Promise.all` (ES2015) 只有当传入的每个 Promise 实例(p1,p2,p3)的状态都变成 fulfilled 时，p 才 fulfilled，只要(p1,p2,p3)有一个被 rejected，p 的状态就变成 rejected。
- `Promise.race` (ES2015) 当传入的 Promise 实例(p1,p2,p3)中有一个率先改变状态，那么 p 的状态就跟着改变，也就是说返回最先改变的 Promise 实例的返回值。
- `Promise.allSettled` (ES2020) 只有等到所有传入的 Promise 实例(p1,p2,p3)都返回结果，不管是 fulfilled 还是 rejected，包装实例才会结束。
- `Promise.any` (ES2021) 当其中任何一个 Promise 完成(fulfilled)时，就返回那个已经有完成值的 Promise。如果所有的 Promise 都拒绝 (rejected), 那么返回一个拒绝的 Promise。  
  注意：前者不会因为某个 Promise 变成 rejected 状态而结束。

### WeakRefs

WeakRef 实例不会阻止 GC 回收，但是 GC 会在两次 EventLoop 之间回收 WeakRef 实例。GC 回收后的 WeakRef 实例的 deref() 方法将会返回 undefined。

```js
let ref = new WeakRef(obj);
let isLive = ref.deref(); // 如果 obj 被垃圾回收了，那么 isLive 就是 undefined
```

### Logical Assignment Operators

逻辑赋值操作符

```js
a ||= b; // 等同于 a || (a = b);

a &&= b; // 等同于 a && (a = b);

a ??= b; // 等同于 a ?? (a = b);
```

### Numeric separators

数字分隔符

```js
const x = 10000000;
const y = 1_000_000;
console.log(x === y); // true
```

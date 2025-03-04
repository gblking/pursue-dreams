### Promise.prototype.finally

Promise 新增的一个实例方法。意图是允许在 resolve/reject 之后执行回调。finally 没有返回值，始终会被执行。

```js
promise
.then(result => {···})
.catch(error => {···})
.finally(() => {···});
```

### Asynchronous Iteration

异步迭代， 它允许我们轻松的创建异步代码循环。  
此特性添加了一个新的“for-await-of”循环，允许我们在循环中调用返回 promises（或带有一堆 promise 的 Arrays ）的异步函数。 循环会等待每个 Promise 在进行下一个循环之前 resolve 。

```js
const promises = [
  new Promise((resolve) => resolve(1)),
  new Promise((resolve) => resolve(1)),
  new Promise((resolve) => resolve(1)),
];
async function test1() {
  for (const obj of promises) {
    console.log(obj);
  }
}
async function test2() {
  for await (const obj of promises) {
    console.log(obj);
  }
}
test1(); //promise,promise,promise
test2(); //1,2,3
```

### RegExp Unicode Property Escapes

正则去匹配 Unicode 字符是很不容易的。像 \w , \W , \d 这种只能匹配英文字符和数字。但是其他语言的字符怎么办呢，比如印度语，希腊语？

有了 Unicode Property Escapes 之后，它为每一个字符添加一个 metadata 属性，用它去分组或者表达各式各样的符号。  
例如 Unicode 数据库组里把所有的印度语字符，标识为 Script = Devanagari。还有一个属性 Script_Extensions， 值也为 Devanagari。  
所以我们可以通过搜索 Script=Devanagari，得到所有的印度语。

```js
/^\p{Script=Devanagari}+$/u.test("हिन्दी"); //true
/\p{Emoji}/u.test("❤️"); //true
/\p{Emoji}\p{Emoji_Modifier}/u.test("✌️"); //false
```

### RegExp Lookbehind Assertions

对正则表达式的增强，确保在一些字符串存在之前，另一些字符串存在。  
(?<=…) 去正向断定，也可以用 (?<!…) 去取反。  
正向断定： 我们想确保 # 在 winning 之前。（就是#winning），想正则匹配返回 winning。下面是写法：

```js
/(?<=#).*/.test("winning"); //false
/(?<=#).*/.test("#winning"); //true
"#winning".match(/(?<=#).*/)[0];
```

反向断定：匹配一个数字，有 € 字符而没有 $ 字符在前面的数字。写法：

```js
"A gallon of milk is $3.00".match(/(?<!\$)\d+\.?\d+/); // null
"A gallon of milk is €2.43".match(/(?<!\$)\d+\.?\d+/)[0]; // 2.43
```

### Rest/Spread Properties

对象的结构与组合

1. rest 操作符用于对象结构赋值

   ```js
   // 用于数组
   const [...iterableObj] = [1, 3, 5, 7, 9];
   [...iterableObj, 0, 2, 4, 6, 8];
   // output
   // [1, 3, 5, 7, 9, 0, 2, 4, 6, 8]
   // 用于对象
   const obj = { foo: 1, bar: 2, baz: 3 };
   const { foo, ...rest } = obj;
   // output foo rest
   // {foo:1}
   // {bar: 2, baz: 3}
   ```

2. rest 操作符收集其他参数

   ```js
   function func({ param1, param2, ...rest }) {
     // rest operator
     console.log("All parameters: ", { param1, param2, ...rest }); // spread operator
     return param1 + param2;
   }
   ```

   语法限制：结构时在对象的顶层只能使用一个...rest 操作符并且要放在最后

   ```js
   const {...rest, foo} = obj; // SyntaxError
   const {foo, ...rest1, ...rest2} = obj; // SyntaxError
   ```

3. spread 操作符能够将所有的可枚举属性插入到新的对象中

   ```js
   const obj = {foo: 1, bar: 2, baz: 3};
   {...obj, qux: 4}
   { foo: 1, bar: 2, baz: 3, qux: 4 }
   ```

4. ...rest 和...spread 常用在克隆对象，合并操作等场景。如填充一些默认属性

   ```js
   const DEFAULTS = { foo: "a", bar: "b" };
   const userData = { foo: 1 };
   const data = { ...DEFAULTS, ...userData };
   // {foo: 1, bar: 'b'}
   ```

### RegExp named capture groups

这个方法允许开发者在正则表达式中(?…) 给不同的组起名称。使得我们可以轻易的通过名称获取到某个组

```js
//before
let re = /(\d{4})-(\d{2})-(\d{2})/;
let result = re.exec("2021-06-15");
console.log(result);
//["2021-06-15", "2021", "06", "15", index: 0, input: "2021-06-15", groups: undefined]

//after
let re = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/;
let result = re.exec("2021-06-15");
console.log(result);
// ["2021-06-15", "2021", "06", "15", index: 0, input: "2021-06-15",
// groups: {
// year:'2021',
// month:'06',
// day:'15'
// }]
```

### s (dotAll) flag for regular expressions

正则表达式中的 `.`

```js
/first.second/.test("first\nsecond"); //false
/first.second/s.test("first\nsecond"); //true
```

### Lifting template literal restriction

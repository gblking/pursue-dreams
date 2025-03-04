## 概述

使用 typescript 进行 vue 开发，普通的 vue 开发模式无法享受到 typescript 的特性，如：类型检测、类型推断。 通过使用 class 语法有助于我们得到 ts 的类型检测。

```js
<template>
  <div>
    <button v-on:click="decrement">-</button>
    {{ count }}
    <button v-on:click="increment">+</button>
  </div>
</template>

<script>
import Vue from 'vue'
import Component from 'vue-class-component'

// Define the component in class-style
@Component
export default class Counter extends Vue {
  // Class properties will be component data
  count = 0

  // Methods will be component methods
  increment() {
    this.count++
  }

  decrement() {
    this.count--
  }
}
</script>
```

## 文档

[TypeScript](https://www.tslang.cn/docs/home.html)  
[Vue CLI](https://cli.vuejs.org/zh/guide/)  
[vue_class_component](https://class-component.vuejs.org/)  
[vue_property_decorator](https://github.com/kaorun343/vue-property-decorator)  
[vuex-class](https://github.com/ktsn/vuex-class/)

## 安装

创建项目
![0]({{VUE_APP_PLULIC_PATH}}posts/images/20220112/0.png)

选择 "Manually select features"  
![1]({{VUE_APP_PLULIC_PATH}}posts/images/20220112/1.png)

选中 TypeScript 功能以使用 Vue 类组件。如果需要，还可以添加其他功能：
![2]({{VUE_APP_PLULIC_PATH}}posts/images/20220112/2.png)

使用 class-style 语法
![3]({{VUE_APP_PLULIC_PATH}}posts/images/20220112/3.png)

## vue-class-component

### General Guide

#### @Component

`@Component` 装饰器创建 Class Vue 组件：

```js
import Vue from "vue";
import Component from "vue-class-component";

// HelloWorld class will be a Vue component
@Component
export default class HelloWorld extends Vue {}
```

#### Data

初始`data`可以声明为类属性：

```js
<template>
  <div>{{ message }}</div>
</template>

<script>
import Vue from 'vue'
import Component from 'vue-class-component'

@Component
export default class HelloWorld extends Vue {
  // Declared as component data
  message = 'Hello World!'
}
</script>
```

`注意：`当初始化`data`数据为`undefined`,使用上面这种方式的，当对 data 数据进行改变时无法被监听到。可使用`null`代替或者使用`data` hook 钩子：

```js
import Vue from "vue";
import Component from "vue-class-component";

@Component
export default class HelloWorld extends Vue {
  // `message` will be reactive with `null` value
  message = null;

  // See Hooks section for details about `data` hook inside class.
  data() {
    return {
      // `hello` will be reactive as it is declared via `data` hook.
      hello: undefined,
    };
  }
}
```

#### Methods

`methods`可以直接声明为 class 原型方法：

```js
<template>
  <button v-on:click="hello">Click</button>
</template>

<script>
import Vue from 'vue'
import Component from 'vue-class-component'

@Component
export default class HelloWorld extends Vue {
  // Declared as component method
  hello() {
    console.log('Hello World!')
  }
}
</script>
```

#### Computed

`computed`可以声明为 class 的`getter/setter`属性：

```js
<template>
  <input v-model="name">
</template>

<script>
import Vue from 'vue'
import Component from 'vue-class-component'

@Component
export default class HelloWorld extends Vue {
  firstName = 'John'
  lastName = 'Doe'

  // Declared as computed property getter
  get name() {
    return this.firstName + ' ' + this.lastName
  }

  // Declared as computed property setter
  set name(value) {
    const splitted = value.split(' ')
    this.firstName = splitted[0]
    this.lastName = splitted[1] || ''
  }
}
</script>
```

#### Hooks

`data`, `render` 和其他 Vue 生命周期钩子函数都可以在 class 中直接声明

```js
import Vue from "vue";
import Component from "vue-class-component";

@Component
export default class HelloWorld extends Vue {
  // Declare mounted lifecycle hook
  mounted() {
    console.log("mounted");
  }

  // Declare render function
  render() {
    return <div>Hello World!</div>;
  }
}
```

#### Components

引入 components:

```js
<template>
 <OtherComponent />
</template>

<script>
import Vue from 'vue'
import Component from 'vue-class-component'
import OtherComponent from './OtherComponent.vue'

@Component({
 components: {
   OtherComponent
 }
})
export default class HelloWorld extends Vue {}
</script>
```

#### Additional Hooks

当使用一些 Vue 插件，如：`Vue Router`，希望 class 类组件解析它们提供的 hooks 钩子，可通过`Component.registerHooks`对此类钩子进行注册:

```js
// class-component-hooks.js
import Component from "vue-class-component";

// Register the router hooks with their names
Component.registerHooks([
  "beforeRouteEnter",
  "beforeRouteLeave",
  "beforeRouteUpdate",
]);
```

在`main.js`中顶部位置通过`import`引入注册文件，`注:`注册文件需在其他组件前引入，即放在顶部位置引入，确保执行顺序

```js
// main.js

// Make sure to register before importing any components
import "./class-component-hooks";

import Vue from "vue";
import App from "./App";

new Vue({
  el: "#app",
  render: (h) => h(App),
});
```

class 类组件将其实现为类原型方法：

```js
import Vue from "vue";
import Component from "vue-class-component";

@Component
export default class HelloWorld extends Vue {
  // The class component now treats beforeRouteEnter,
  // beforeRouteUpdate and beforeRouteLeave as Vue Router hooks
  beforeRouteEnter(to, from, next) {
    console.log("beforeRouteEnter");
    next();
  }

  beforeRouteUpdate(to, from, next) {
    console.log("beforeRouteUpdate");
    next();
  }

  beforeRouteLeave(to, from, next) {
    console.log("beforeRouteLeave");
    next();
  }
}
```

#### 自定义 Decorators

自定义 decorators 装饰器来扩展功能。提供`createDecorator`创建自定义装饰器，需要一个回调函数作为第一个参数，回调函数接收以下参数：

- `options`：Vue 组件 options 对象，修改此对象会影响提供该对象的组件
- `key`：应用该装饰器的属性或方法键
- `parameterIndex`：装饰参数的索引如果自定义装饰器作为参数使用

```js
// decorators.js
import { createDecorator } from "vue-class-component";

// Declare Log decorator.
export const Log = createDecorator((options, key) => {
  // Keep the original method for later.
  const originalMethod = options.methods[key];

  // Wrap the method with the logging logic.
  options.methods[key] = function wrapperMethod(...args) {
    // Print a log.
    console.log(`Invoked: ${key}(`, ...args, ")");

    // Invoke the original method.
    originalMethod.apply(this, args);
  };
});
```

```js
import Vue from "vue";
import Component from "vue-class-component";
import { Log } from "./decorators";

@Component
class MyComp extends Vue {
  // It prints a log when `hello` method is invoked.
  @Log
  hello(value) {
    // ...
  }
}
```

```js
// 调用
hello(42);

// 输出
Invoked: hello(42);
```

#### Extend and Mixins

**Extend**  
使用 Extend 进行类继承

```js
// super.js
import Vue from "vue";
import Component from "vue-class-component";

// Define a super class component
@Component
export default class Super extends Vue {
  superValue = "Hello";
}
```

**Mixins**

```js
// mixins.js
import Vue from "vue";
import Component from "vue-class-component";

// You can declare mixins as the same style as components.
@Component
export class Hello extends Vue {
  hello = "Hello";
}

@Component
export class World extends Vue {
  world = "World";
}
```

```js
import Component, { mixins } from "vue-class-component";
import { Hello, World } from "./mixins";

// Use `mixins` helper function instead of `Vue`.
// `mixins` can receive any number of arguments.
@Component
export class HelloWorld extends mixins(Hello, World) {
  created() {
    console.log(this.hello + " " + this.world + "!"); // -> Hello World!
  }
}
```

#### 注意事项

**this**  
箭头函数中使用 this 获取初始属性值，无法获取。这是因为在初始化类属性时，这只是 Vue 的代理对象：

```js
import Vue from "vue";
import Component from "vue-class-component";

@Component
export default class MyComp extends Vue {
  foo = 123;

  // DO NOT do this
  bar = () => {
    // Does not update the expected property.
    // `this` value is not a Vue instance in fact.
    this.foo = 456;
  };
}
```

使用普通方法：

```js
import Vue from "vue";
import Component from "vue-class-component";

@Component
export default class MyComp extends Vue {
  foo = 123;

  // DO this
  bar() {
    // Correctly update the expected property.
    this.foo = 456;
  }
}
```

**constructor**

```js
import Vue from "vue";
import Component from "vue-class-component";

@Component
export default class Posts extends Vue {
  posts = [];

  // DO NOT do this
  constructor() {
    fetch("/posts.json")
      .then((res) => res.json())
      .then((posts) => {
        this.posts = posts;
      });
  }
}
```

由于 Vue class component 工作方式，上面的代码将会调用两次。  
可将上面的代码 constructor 用 create 替换。

### TypeScript Guide

#### Props 定义

vue-class-component 没有提供专门的 API 用于定义 Props,可以使用 Vue.extend API：

```js
<template>
  <div>{{ message }}</div>
</template>

<script lang="ts">
import Vue from 'vue'
import Component from 'vue-class-component'

// Define the props by using Vue's canonical way.
const GreetingProps = Vue.extend({
  props: {
    name: String
  }
})

// Use defined props by extending GreetingProps.
@Component
export default class Greeting extends GreetingProps {
  get message(): string {
    // this.name will be typed
    return 'Hello, ' + this.name
  }
}
</script>
```

`或`

```js
<template>
  <div>{{ message }}</div>
</template>

<script lang="ts">
import Vue from 'vue'
import Component, { mixins } from 'vue-class-component'
import Super from './super'

// Define the props by using Vue's canonical way.
const GreetingProps = Vue.extend({
  props: {
    name: String
  }
})

// Use `mixins` helper to combine defined props and a mixin.
@Component
export default class Greeting extends mixins(GreetingProps, Super) {
  get message(): string {
    // this.name will be typed
    return 'Hello, ' + this.name
  }
}
</script>
```

#### 属性类型声明

有时您必须在 vue-class-component 之外定义组件属性和方法，如：Vuex 提供的 MapGetter 和 mapActions，需要在组件 options 对象中使用。  
可通过将组件 options 传递给`@component`作为 decorator 的参数，但是 properties 和 methods 在运行时不会自动声明，需要在 class 类组件中进行声明：

```js
import Vue from 'vue'
import Component from 'vue-class-component'
import { mapGetters, mapActions } from 'vuex'

// Interface of post
import { Post } from './post'

@Component({
  computed: mapGetters([
    'posts'
  ]),

  methods: mapActions([
    'fetchPosts'
  ])
})
export default class Posts extends Vue {
  // Declare mapped getters and actions on type level.
  // You may need to add `!` after the property name
  // to avoid compilation error (definite assignment assertion).

  // Type the mapped posts getter.
  posts!: Post[]

  // Type the mapped fetchPosts action.
  fetchPosts!: () => Promise<void>

  mounted() {
    // Use the mapped getter and action.
    this.fetchPosts().then(() => {
      console.log(this.posts)
    })
  }
}
```

#### $refs 类型扩展

```js
<template>
  <input ref="input">
</template>

<script lang="ts">
import Vue from 'vue'
import Component from 'vue-class-component'

@Component
export default class InputFocus extends Vue {
  // annotate refs type.
  // The symbol `!` (definite assignment assertion)
  // is needed to get rid of compilation error.
  $refs!: {
    input: HTMLInputElement
  }

  mounted() {
    // Use `input` ref without type cast.
    this.$refs.input.focus()
  }
}
</script>
```

#### Hooks Auto-complete

Vue Class Component 提供了内置的 hook 类型, 可以在 class component 声明中为 TypeScript 自动完成`data`、`render`和其他生命周期钩子(hooks)。通过引入`vue-class-component/hooks`启用。

```js
// main.ts
import "vue-class-component/hooks"; // import hooks type to enable auto-complete
import Vue from "vue";
import App from "./App.vue";

new Vue({
  render: (h) => h(App),
}).$mount("#app");
```

#### 在 Decorator 中注释 Component 类型

有时候您需要在`@component` decorator 参数中的函数上使用组件类型，如下：

```js
@Component({
  watch: {
    postId(id: string) {
      // To fetch post data when the id is changed.
      this.fetchPost(id); // -> Property 'fetchPost' does not exist on type 'Vue'.
    },
  },
})
class Post extends Vue {
  postId: string;

  fetchPost(postId: string): Promise<void> {
    // ...
  }
}
```

上面的代码生成了一个错误，该错误指示 watch 处理程序中，不存在 fetchPost。之所以发生这种情况，是因为`@Component` decorator 参数是`Vue`类型。要使用自己的组件类型(即上述示例的 Post)，可以通过其类型参数对装饰器进行注释。

```js
// Annotate the decorator with the component type 'Post' so that `this` type in
// the decorator argument becomes 'Post'.
@Component<Post>({
  watch: {
    postId(id: string) {
      this.fetchPost(id) // -> No errors
    }
  }
})
class Post extends Vue {
  postId: string

  fetchPost(postId: string): Promise<void> {
    // ...
  }
}
```

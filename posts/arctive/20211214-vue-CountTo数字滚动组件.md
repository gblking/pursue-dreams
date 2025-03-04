## 效果图

![告诫]({{VUE_APP_PLULIC_PATH}}posts/images/20211214/countTo.gif)

## 代码

### template

```vue
<template>
  <span class="count-to">
    {{ displayValue }}
  </span>
</template>
```

### script

```vue
<script>
import { onUnmounted, ref, computed, onMounted, getCurrentInstance } from "vue";
import {
  requestAnimationFrame,
  cancelAnimationFrame,
} from "./requestAnimationFrame.js";
export default {
  name: "CountTo",
  props: {
    // 开始值
    from: {
      type: Number,
      required: false,
      default: 0,
    },
    // 结束值
    to: {
      type: Number,
      required: false,
      default: 100,
    },
    // 持续时间，毫秒为单位
    duration: {
      type: Number,
      required: false,
      default: 3000,
    },
    // 自动播放
    autoplay: {
      type: Boolean,
      required: false,
      default: true,
    },
    // 显示小数位数
    decimals: {
      type: Number,
      required: false,
      default: 0,
      validator(value) {
        return value >= 0;
      },
    },
    // 十进制分割
    decimal: {
      type: String,
      required: false,
      default: ".",
    },
    // 分隔符
    separator: {
      type: String,
      required: false,
      default: ",",
    },
    // 前缀
    prefix: {
      type: String,
      required: false,
      default: "",
    },
    // 后缀
    suffix: {
      type: String,
      required: false,
      default: "",
    },
  },

  setup() {
    // 是否为倒数，即数字从 大 -> 小
    const countDown = computed(() => ctx.from > ctx.to);

    /**
     * 判断是否为数字
     */
    const isNumber = (val) => {
      return !isNaN(parseFloat(val));
    };

    /**
     *  格式化数据
     */
    const formatNumber = (num) => {
      num = num.toFixed(ctx.decimals);
      num += "";
      const x = num.split(".");
      let x1 = x[0];
      const x2 = x.length > 1 ? ctx.decimal + x[1] : "";
      const rgx = /(\d+)(\d{3})/;
      if (ctx.separator && !isNumber(ctx.separator)) {
        while (rgx.test(x1)) {
          x1 = x1.replace(rgx, "$1" + ctx.separator + "$2");
        }
      }
      return ctx.prefix + x1 + x2 + ctx.suffix;
    };

    /**
     * 开始滚动动画
     */
    const start = () => {
      rAF.value = requestAnimationFrame(count);
    };

    /**
     * time: 下次动画时间(毫秒)
     */
    const count = (time) => {
      if (!startTime.value) startTime.value = time;
      const progress = time - startTime.value; // 进度
      let printVal = 0;
      if (countDown.value) {
        printVal = ctx.from - (ctx.from - ctx.to) * (progress / ctx.duration);
      } else {
        printVal = ctx.from + (ctx.to - ctx.from) * (progress / ctx.duration);
      }

      // 比较当前值与结束值
      if (countDown.value) {
        printVal = printVal < ctx.to ? ctx.to : printVal;
      } else {
        printVal = printVal > ctx.to ? ctx.to : printVal;
      }

      displayValue.value = formatNumber(printVal);
      if (progress < ctx.duration) {
        rAF.value = requestAnimationFrame(count);
      }
    };

    const { ctx } = getCurrentInstance();
    let startTime = ref(null);
    let rAF = ref(null); // requestAnimationFrame setTimeOut
    let displayValue = ref(formatNumber(ctx.from)); // 页面显示内容

    onMounted(() => {
      if (ctx.autoplay) {
        start();
      }
    });
    onUnmounted(() => {
      cancelAnimationFrame(rAF.value);
    });

    return {
      displayValue,
      start,
    };
  },
};
</script>
```

### css

```css
<style lang="scss" scoped>
.count-to {
  font-weight: 600;
  font-size: 24px;
  font-family: "PingFang SC Bold";
}
</style>
```

### requestAnimationFrame.js

```javascript
let lastTime = 0;
const prefixes = "webkit moz ms o".split(" "); // 各浏览器前缀

let requestAnimationFrame;
let cancelAnimationFrame;

const isServer = typeof window === "undefined";
if (isServer) {
  requestAnimationFrame = function () {
    return;
  };
  cancelAnimationFrame = function () {
    return;
  };
} else {
  requestAnimationFrame = window.requestAnimationFrame;
  cancelAnimationFrame = window.cancelAnimationFrame;
  let prefix;
  // 通过遍历各浏览器前缀，来得到requestAnimationFrame和cancelAnimationFrame在当前浏览器的实现形式
  for (let i = 0; i < prefixes.length; i++) {
    if (requestAnimationFrame && cancelAnimationFrame) {
      break;
    }
    prefix = prefixes[i];
    requestAnimationFrame =
      requestAnimationFrame || window[prefix + "RequestAnimationFrame"];
    cancelAnimationFrame =
      cancelAnimationFrame ||
      window[prefix + "CancelAnimationFrame"] ||
      window[prefix + "CancelRequestAnimationFrame"];
  }

  // 如果当前浏览器不支持requestAnimationFrame和cancelAnimationFrame，则会退到setTimeout
  if (!requestAnimationFrame || !cancelAnimationFrame) {
    requestAnimationFrame = function (callback) {
      const currTime = new Date().getTime();
      // 为了使setTimteout的尽可能的接近每秒60帧的效果
      const timeToCall = Math.max(0, 16 - (currTime - lastTime));
      const id = window.setTimeout(() => {
        callback(currTime + timeToCall);
      }, timeToCall);
      lastTime = currTime + timeToCall;
      return id;
    };

    cancelAnimationFrame = function (id) {
      window.clearTimeout(id);
    };
  }
}

export { requestAnimationFrame, cancelAnimationFrame };
```

## API

| 属性      | 说明                     | 类型    | 默认值 |
| --------- | ------------------------ | ------- | ------ |
| from      | 起始值                   | Number  | 0      |
| to        | 结束值                   | Number  | 100    |
| duration  | 持续时间，单位毫秒       | Number  | 3000   |
| decimals  | 显示小数位数             | Number  | 0      |
| decimal   | 十进制分割               | String  | .      |
| autoplay  | 自动播放                 | Boolean | true   |
| separator | 分隔符(4 位数才能有效果) | String  | ,      |
| prefix    | 前缀                     | String  |        |
| suffix    | 后缀                     | String  |        |
| styles    | 自定义样式               | Object  |        |

## Methods

| 方法名 | 说明         | 参数 |
| ------ | ------------ | ---- |
| start  | 开始执行动画 | -    |

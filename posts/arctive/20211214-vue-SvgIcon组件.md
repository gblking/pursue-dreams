## 示例

![svgicon]({{VUE_APP_PLULIC_PATH}}posts/images/20211214/svgicon.png)

```vue
<SvgIcon icon-class="config"></SvgIcon>
```

## SvgIcon.vue

```vue
<template>
  <svg class="svg-icon" aria-hidden="true">
    <use :xlink:href="iconName" />
  </svg>
</template>

<script>
import { computed, getCurrentInstance } from "vue";
export default {
  name: "SvgIcon",
  props: {
    iconClass: {
      type: String,
      required: true,
    },
  },
  setup() {
    const { ctx } = getCurrentInstance();
    const iconName = computed(() => {
      return `#icon-${ctx.iconClass}`;
    });
    return {
      iconName,
    };
  },
};
</script>

<style scoped>
.svg-icon {
  width: 1em;
  height: 1em;
  vertical-align: -0.15em;
  fill: currentColor;
  overflow: hidden;
}
</style>
```

## 配置

1. 在 vue.config.js 中添加配置

   ```js
   const path = require("path");
   const resolve = (dir) => path.join(__dirname, dir);

   module.exports = {
     chainWebpack: (config) => {
       config.resolve.alias.set("@", resolve("src"));
       config.module.rules.delete("svg"); //删除默认配置svg
       config.module
         .rule("svg-smart")
         .test(/\.svg$/)
         .include.add(
           resolve("src/icons/svg"),
           resolve("node_modules/mavon-editor/dist/font")
         ) // svg图路径
         .end()
         .use("svg-sprite-loader")
         .loader("svg-sprite-loader")
         .options({
           symbolId: "icon-[name]",
         })
         .end();
     },
   };
   ```

2. src 目录下新建 icons 文件夹
3. icons 文件夹下新建 index.js

   ```js
   const requireAll = (requireContext) =>
     requireContext.keys().map(requireContext);
   const req = require.context("./svg", false, /\.svg$/);
   requireAll(req);
   ```

4. src/icons 目录下新建 icons 文件夹，用于放置.svg 文件

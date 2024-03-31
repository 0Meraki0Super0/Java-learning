
在Vue.js项目中，每个页面通常由三个主要部分组成。这三个主要部分是：

1. **Template（模板）：** 定义页面的结构和布局，包含HTML和Vue模板语法，用于呈现用户界面的内容。
    
2. **Script（脚本）：** 包含页面的行为，包括数据、方法、生命周期钩子等。使用JavaScript 和Vue的组件选项语法编写。
    
3. **Style（样式）：** 定义页面的样式，包括CSS和预处理器（如Sass或Less）。这部分负责页面的外观和样式。

 例如：
 <template>
  <div>
    <!-- 页面的结构和布局 -->
    <h1>{{ pageTitle }}</h1>
    <p>{{ pageContent }}</p>
  </div>
</template>
``` html
<template>
  <div>
    <!-- 页面的结构和布局 -->
    <h1>{{ pageTitle }}</h1>
    <p>{{ pageContent }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 页面的数据
      pageTitle: 'My Page',
      pageContent: 'Welcome to my page!',
    };
  },
  methods: {
    // 页面的方法
  },
  // 其他组件选项，生命周期钩子等
};
</script>


<style>
/* 页面的样式 */
h1 {
  color: blue;
}
</style>

```
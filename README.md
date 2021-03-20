# vue-plugin-side-anchor

<div align='center'>

![version](https://img.shields.io/badge/version-1.0.0-blue)
![license](https://img.shields.io/badge/license-MIT-brightgreen)

</div>

> 添加右侧边锚点导航栏到你的vuepress站点中，类似于 [firebase document](https://firebase.google.cn/docs/web/setup)

## 特性
+ 自动收集标题以及层级关系，无需配置
+ 滑动时，侧边锚点自动高亮

## 使用
```bash
yarn add vuepress-plugin-side-anchor -D
# 或者
npm i vuepress-plugin-side-anchor -D
```

# 使用
在 .vuepress/config.js 添加如下配置。
```js
module.exports = {
  // ...
  plugins: [
    // ...
    ['vuepress-plugin-side-anchor']
  ]
}
```
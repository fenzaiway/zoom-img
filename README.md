# zoom-img
基于iview + lodash + jquery实现的图片放大功能

#### 使用方式

```
<img :src="o" v-for="(o, index) in imgs" class="zoom-img" :key="index" alt="">
<u-zoom-img></u-zoom-img>
```
给图片增加  `class="zoom-img"` 类属性
在页面中引入组件即可

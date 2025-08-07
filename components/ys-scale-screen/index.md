# YsScaleScreen 组件文档

## 简介
`YsScaleScreen` 是一个屏幕自适应组件，用于实现页面内容的自适应缩放，支持全屏铺满、自定义宽高、延迟处理等功能。

## 参数

| 参数名         | 类型                | 默认值  | 说明                                              |
| :------------- | :------------------ | :------ | :------------------------------------------------ |
| `isEdit`       | `Boolean`           | `false` | 是否处于编辑状态，用于控制某些行为（如缩放等）    |
| `width`        | `String \| Number`  | `1920`  | 设计稿的宽度（单位：px）                          |
| `height`       | `String \| Number`  | `1080`  | 设计稿的高度（单位：px）                          |
| `fullScreen`   | `Boolean`           | `false` | 是否全屏铺满，若为 `true`，则按照宽高比例分别缩放 |
| `autoScale`    | `Object \| Boolean` | `true`  | 是否自动缩放，支持传入对象配置（暂未详细实现）    |
| `delay`        | `Number`            | `500`   | 缩放操作的防抖延迟（单位：ms）                    |
| `boxStyle`     | `Object`            | `{}`    | 自定义外层容器的样式                              |
| `wrapperStyle` | `Object`            | `{}`    | 自定义内容包裹层的样式                            |

## 暴露方法

| 方法名     | 返回值类型 | 说明                     |
| :--------- | :--------- | :----------------------- |
| `getEdit`  | `Boolean`  | 获取当前是否处于编辑状态 |
| `getScale` | `Number`   | 获取当前的缩放比例       |

## 使用示例

```vue
<template>
  <YsScaleScreen
    :width="1920"
    :height="1080"
    :fullScreen="true"
    :delay="300"
    :boxStyle="{ background: 'black' }"
  >
    <div class="content">
      <!-- 页面内容 -->
    </div>
  </YsScaleScreen>
</template>

<script>
import YsScaleScreen from './YsScaleScreen.vue';

export default {
  components: {
    YsScaleScreen
  }
};
</script>
```

## 方法调用

```javascript
this.$refs.screen.getEdit(); // 获取是否处于编辑状态
this.$refs.screen.getScale(); // 获取当前缩放比例
```
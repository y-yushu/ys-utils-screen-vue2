# YsContainer 组件文档

## 简介
`YsContainer` 是一个可拖拽、可调整大小的容器组件，支持编辑模式下的拖拽和尺寸调整功能。它适用于需要动态调整布局的场景，例如可视化编辑器。

## 参数

| 参数名   | 类型               | 默认值  | 说明                                   |
| :------- | :----------------- | :------ | :------------------------------------- |
| `id`     | `String`           | `''`    | 容器的唯一标识符                       |
| `cuEdit` | `Boolean`          | `false` | 是否启用编辑模式（独立于全局编辑状态） |
| `width`  | `String \| Number` | `''`    | 容器的初始宽度（单位：px）             |
| `height` | `String \| Number` | `''`    | 容器的初始高度（单位：px）             |
| `left`   | `String \| Number` | `''`    | 容器的初始左偏移（单位：px）           |
| `top`    | `String \| Number` | `''`    | 容器的初始上偏移（单位：px）           |

## 方法

| 方法名      | 返回值类型 | 说明                                                 |
| :---------- | :--------- | :--------------------------------------------------- |
| `getConfig` | `Object`   | 获取当前容器的配置（包括宽度、高度、左偏移和上偏移） |

## 事件

| 事件名   | 返回值类型                                                     | 说明                                                         |
| :------- | :------------------------------------------------------------- | :----------------------------------------------------------- |
| `move`   | `{ left: Number, top: Number }`                                | 容器位置发生变化时触发，返回新的左偏移和上偏移值             |
| `resize` | `{ width: Number, height: Number, left: Number, top: Number }` | 容器尺寸发生变化时触发，返回新的宽度、高度、左偏移和上偏移值 |

## 使用示例

### 基础用法
```vue
<template>
  <YsContainer
    :width="300"
    :height="200"
    :left="50"
    :top="50"
    @move="onMove"
    @resize="onResize"
  >
    <div class="content">
      <!-- 自定义内容 -->
    </div>
  </YsContainer>
</template>

<script>
import YsContainer from './YsContainer.vue';

export default {
  components: {
    YsContainer
  },
  methods: {
    onMove({ left, top }) {
      console.log(`容器移动到：left=${left}, top=${top}`);
    },
    onResize({ width, height, left, top }) {
      console.log(`容器调整为：width=${width}, height=${height}, left=${left}, top=${top}`);
    }
  }
};
</script>
```

### 编辑模式
```vue
<template>
  <YsContainer
    :cuEdit="true"
    :width="300"
    :height="200"
    :left="50"
    :top="50"
    @move="onMove"
    @resize="onResize"
  >
    <div class="content">
      <!-- 自定义内容 -->
    </div>
  </YsContainer>
</template>

<script>
import YsContainer from './YsContainer.vue';

export default {
  components: {
    YsContainer
  },
  methods: {
    onMove({ left, top }) {
      console.log(`容器移动到：left=${left}, top=${top}`);
    },
    onResize({ width, height, left, top }) {
      console.log(`容器调整为：width=${width}, height=${height}, left=${left}, top=${top}`);
    }
  }
};
</script>
```

## 样式说明
- 容器的样式由 `custyle` 计算属性动态生成，支持宽度、高度、左偏移和上偏移的设置。
- 编辑模式下，容器会显示可拖拽的边框和角点，用于调整尺寸。
- 边框和角点的样式可以通过自定义样式覆盖默认样式。

## 注意事项
- 组件依赖全局注入的 `getEdit` 和 `getScale` 方法，分别用于获取全局编辑状态和缩放比例。
- 在非编辑模式下，容器的边框和角点不会显示，且无法进行拖拽或调整尺寸操作。
- 组件会自动处理缩放比例，确保在不同缩放级别下拖拽和调整尺寸的准确性。
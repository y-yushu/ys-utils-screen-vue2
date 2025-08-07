<template>
  <div class="ys-container" :style="custyle">
    <slot></slot>
    <div v-if="meEdit" class="ys-container-edit" @mousedown="onContainerMouseDown($event)">
      <!-- 四条边 -->
      <div class="border-top" @mousedown.stop="onMouseDown('top', $event)"></div>
      <div class="border-left" @mousedown.stop="onMouseDown('left', $event)"></div>
      <div class="border-right" @mousedown.stop="onMouseDown('right', $event)"></div>
      <div class="border-bottom" @mousedown.stop="onMouseDown('bottom', $event)"></div>
      <!-- 四个点 -->
      <div class="border-top-left" @mousedown.stop="onMouseDown('top-left', $event)"></div>
      <div class="border-top-right" @mousedown.stop="onMouseDown('top-right', $event)"></div>
      <div class="border-bottom-left" @mousedown.stop="onMouseDown('bottom-left', $event)"></div>
      <div class="border-bottom-right" @mousedown.stop="onMouseDown('bottom-right', $event)"></div>
    </div>
  </div>
</template>

<script>
// 处理数字类型的样式值
const getValue = val => {
  if (typeof val === 'number') return val + 'px'
  return val
}

export default {
  name: 'YsContainer',
  // 注入全局的编辑状态和缩放比例
  inject: {
    getEdit: {
      default: () => false
    },
    getScale: {
      default: () => 1
    }
  },
  props: {
    id: {
      type: String,
      default: ''
    },
    // 编辑模式
    cuEdit: {
      type: Boolean,
      default: false
    },
    width: {
      type: [String, Number],
      default: ''
    },
    height: {
      type: [String, Number],
      default: ''
    },
    left: {
      type: [String, Number],
      default: ''
    },
    top: {
      type: [String, Number],
      default: ''
    }
  },
  data() {
    return {
      // 组件的属性
      meWidth: 0,
      meHeight: 0,
      meLeft: 0,
      meTop: 0,

      // 拖拽状态
      dragging: false,
      dragDirection: '',
      startX: 0,
      startY: 0,
      startWidth: 0,
      startHeight: 0,
      startLeft: 0,
      startTop: 0,

      // 容器拖动状态
      containerDragging: false
    }
  },
  computed: {
    // 当前组件是否可编辑
    meEdit() {
      const ftype = this.getEdit()
      return ftype || this.cuEdit
    },
    custyle() {
      return {
        width: getValue(this.meWidth),
        height: getValue(this.meHeight),
        left: getValue(this.meLeft),
        top: getValue(this.meTop)
      }
    }
  },
  created() {
    this.meWidth = this.width
    this.meHeight = this.height
    this.meLeft = this.left
    this.meTop = this.top
  },
  methods: {
    // 当鼠标按下容器本身时
    onContainerMouseDown(event) {
      // 如果点击的是边界元素，不处理容器拖动
      if (event.target !== event.currentTarget) return

      if (!this.meEdit) return

      event.preventDefault()
      this.containerDragging = true

      // 记录起始位置
      this.startX = event.clientX
      this.startY = event.clientY
      this.startLeft = parseFloat(this.meLeft) || 0
      this.startTop = parseFloat(this.meTop) || 0

      // 添加鼠标移动和松开事件
      document.addEventListener('mousemove', this.onContainerMouseMove)
      document.addEventListener('mouseup', this.onContainerMouseUp)
    },

    // 拖动容器时的鼠标移动处理
    onContainerMouseMove(event) {
      if (!this.containerDragging) return

      // 计算鼠标移动距离，考虑缩放因素
      const scale = this.getScale()
      const deltaX = (event.clientX - this.startX) / scale
      const deltaY = (event.clientY - this.startY) / scale

      // 更新位置
      this.meLeft = this.startLeft + deltaX
      this.meTop = this.startTop + deltaY

      // 通知父组件位置发生变化
      this.$emit('move', {
        left: this.meLeft,
        top: this.meTop
      })
    },

    // 容器拖动结束
    onContainerMouseUp() {
      this.containerDragging = false

      // 移除事件监听
      document.removeEventListener('mousemove', this.onContainerMouseMove)
      document.removeEventListener('mouseup', this.onContainerMouseUp)
    },

    // 当鼠标按下边界或角点时，创建鼠标移动事件
    onMouseDown(direction, event) {
      if (!this.meEdit) return

      event.preventDefault()
      this.dragging = true
      this.dragDirection = direction

      // 创建鼠标移动事件时，记录位置
      this.startX = event.clientX
      this.startY = event.clientY
      this.startWidth = parseFloat(this.meWidth) || 0
      this.startHeight = parseFloat(this.meHeight) || 0
      this.startLeft = parseFloat(this.meLeft) || 0
      this.startTop = parseFloat(this.meTop) || 0

      // 添加鼠标移动和松开事件
      document.addEventListener('mousemove', this.onMouseMove)
      document.addEventListener('mouseup', this.onMouseUp)
    },

    // 鼠标移动事件触发时，更新位置和尺寸
    onMouseMove(event) {
      if (!this.dragging) return

      // 计算鼠标移动距离，考虑缩放因素
      const scale = this.getScale()
      const deltaX = (event.clientX - this.startX) / scale
      const deltaY = (event.clientY - this.startY) / scale

      // 根据拖拽方向更新尺寸和位置
      if (this.dragDirection === 'bottom') {
        // 底部边框 - 改变高度
        this.meHeight = Math.max(10, this.startHeight + deltaY)
      } else if (this.dragDirection === 'right') {
        // 右侧边框 - 改变宽度
        this.meWidth = Math.max(10, this.startWidth + deltaX)
      } else if (this.dragDirection === 'top') {
        // 顶部边框 - 改变高度和top位置
        const newHeight = Math.max(10, this.startHeight - deltaY)
        this.meHeight = newHeight
        this.meTop = this.startTop + (this.startHeight - newHeight)
      } else if (this.dragDirection === 'left') {
        // 左侧边框 - 改变宽度和left位置
        const newWidth = Math.max(10, this.startWidth - deltaX)
        this.meWidth = newWidth
        this.meLeft = this.startLeft + (this.startWidth - newWidth)
      } else if (this.dragDirection === 'top-left') {
        // 左上角 - 同时改变宽度、高度、left和top位置
        const newWidthTL = Math.max(10, this.startWidth - deltaX)
        const newHeightTL = Math.max(10, this.startHeight - deltaY)
        this.meWidth = newWidthTL
        this.meHeight = newHeightTL
        this.meLeft = this.startLeft + (this.startWidth - newWidthTL)
        this.meTop = this.startTop + (this.startHeight - newHeightTL)
      } else if (this.dragDirection === 'top-right') {
        // 右上角 - 改变宽度、高度和top位置
        this.meWidth = Math.max(10, this.startWidth + deltaX)
        const newHeightTR = Math.max(10, this.startHeight - deltaY)
        this.meHeight = newHeightTR
        this.meTop = this.startTop + (this.startHeight - newHeightTR)
      } else if (this.dragDirection === 'bottom-left') {
        // 左下角 - 改变宽度、高度和left位置
        const newWidthBL = Math.max(10, this.startWidth - deltaX)
        this.meWidth = newWidthBL
        this.meHeight = Math.max(10, this.startHeight + deltaY)
        this.meLeft = this.startLeft + (this.startWidth - newWidthBL)
      } else if (this.dragDirection === 'bottom-right') {
        // 右下角 - 同时改变宽度和高度
        this.meWidth = Math.max(10, this.startWidth + deltaX)
        this.meHeight = Math.max(10, this.startHeight + deltaY)
      }

      // 通知父组件尺寸发生变化
      this.$emit('resize', {
        width: this.meWidth,
        height: this.meHeight,
        left: this.meLeft,
        top: this.meTop
      })
    },

    // 当鼠标松开时，注销鼠标移动事件
    onMouseUp() {
      this.dragging = false

      // 移除事件监听
      document.removeEventListener('mousemove', this.onMouseMove)
      document.removeEventListener('mouseup', this.onMouseUp)
    },

    // 获取配置
    getConfig() {
      return {
        width: this.meWidth,
        height: this.meHeight,
        left: this.meLeft,
        top: this.meTop
      }
    }
  }
}
</script>

<style scoped>
.ys-container {
  position: absolute;
  z-index: 100;
}

.ys-container-edit {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background: rgba(255, 255, 255, 0.8);
  cursor: move; /* 添加移动光标 */
}

.border-top {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 5px;
  background: repeating-linear-gradient(90deg, #800080, #800080 10px, transparent 10px, transparent 15px);
  z-index: 100;
  cursor: n-resize;
}

.border-bottom {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 5px;
  background: repeating-linear-gradient(90deg, #800080, #800080 10px, transparent 10px, transparent 15px);
  z-index: 100;
  cursor: s-resize;
}

.border-left {
  position: absolute;
  left: 0;
  top: 0;
  bottom: 0;
  width: 5px;
  background: repeating-linear-gradient(#800080, #800080 10px, transparent 10px, transparent 15px);
  z-index: 100;
  cursor: w-resize;
}

.border-right {
  position: absolute;
  right: 0;
  top: 0;
  bottom: 0;
  width: 5px;
  background: repeating-linear-gradient(#800080, #800080 10px, transparent 10px, transparent 15px);
  z-index: 100;
  cursor: e-resize;
}

.border-top-left {
  position: absolute;
  top: -15px;
  left: -15px;
  width: 40px;
  height: 40px;
  background: #db00db;
  z-index: 200;
  cursor: nw-resize;
}
.border-top-right {
  position: absolute;
  top: -15px;
  right: -16px;
  width: 40px;
  height: 40px;
  background: #db00db;
  z-index: 200;
  cursor: ne-resize;
}
.border-bottom-left {
  position: absolute;
  bottom: -15px;
  left: -15px;
  width: 40px;
  height: 40px;
  background: #db00db;
  z-index: 200;
  cursor: sw-resize; /* 修正了鼠标样式 */
}
.border-bottom-right {
  position: absolute;
  bottom: -15px;
  right: -15px;
  width: 40px;
  height: 40px;
  background: #db00db;
  z-index: 200;
  cursor: se-resize; /* 修正了鼠标样式 */
}
</style>

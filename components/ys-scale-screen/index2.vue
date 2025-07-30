<template>
  <div ref="v-container" class="" :style="{ ...style.box, ...boxStyle }">
    <div ref="v-box" class="screen-wrapper" :style="{ ...style.wrapper, ...wrapperStyle }">
      <slot></slot>
    </div>
  </div>
</template>

<script>
// /**
//  * 防抖函数
//  * @param {Function} fn
//  * @param {number} delay
//  * @returns {() => void}
//  */
// function debounce(fn, delay) {
//   let timer
//   return function (...args) {
//     if (timer) clearTimeout(timer)
//     timer = setTimeout(
//       () => {
//         if (typeof fn === 'function') fn.apply(null, args)
//         clearTimeout(timer)
//       },
//       delay > 0 ? delay : 100
//     )
//   }
// }

export default {
  name: 'YsScaleScreen',
  props: {
    width: {
      type: [String, Number],
      default: 1920
    },
    height: {
      type: [String, Number],
      default: 1080
    },
    fullScreen: {
      type: Boolean,
      default: false
    },
    autoScale: {
      type: [Object, Boolean],
      default: true
    },
    delay: {
      type: Number,
      default: 500
    },
    boxStyle: {
      type: Object,
      default: () => ({})
    },
    wrapperStyle: {
      type: Object,
      default: () => ({})
    }
  },
  data() {
    return {
      style: {
        box: {
          overflow: 'hidden',
          backgroundSize: '100% 100%',
          background: '#000',
          width: '100vw',
          height: '100vh'
        },
        wrapper: {
          transitionProperty: 'all',
          transitionTimingFunction: 'cubic-bezier(0.4, 0, 0.2, 1)',
          transitionDuration: '500ms',
          position: 'relative',
          overflow: 'hidden',
          zIndex: 100,
          transformOrigin: 'left top'
        }
      },
      state: {
        width: 0,
        height: 0,
        originalWidth: 0,
        originalHeight: 0,
        observer: null
      }
    }
  },
  mounted() {
    this.initSize().then(() => {
      this.updateSize()
      this.updateScale()
    })
  },
  beforeDestroy() {},
  methods: {
    initSize() {
      return new Promise(resolve => {
        this.$nextTick(() => {
          // region 获取大屏真实尺寸
          this.state.width = this.width
          this.state.height = this.height

          if (!this.state.originalHeight || !this.state.width) {
            const { width, height } = this.$refs['v-container'].getBoundingClientRect()
            this.state.originalWidth = width
            this.state.originalHeight = height
          }

          resolve()
        })
      })
    },

    updateSize() {
      if (this.state.width && this.state.height) {
        const box = this.$refs['v-box']
        box.style.width = this.state.width + 'px'
        box.style.height = this.state.height + 'px'
      }
    },

    updateScale() {
      const box = this.$refs['v-box']
      // 获取真实视口尺寸
      const currentWidth = this.state.originalWidth
      const currentHeight = this.state.originalHeight

      // 获取大屏最终的宽高
      const realWidth = this.state.width || this.state.originalWidth
      const realHeight = this.state.height || this.state.originalHeight

      // 计算缩放比例
      const widthScale = currentWidth / +realWidth
      const heightScale = currentHeight / +realHeight

      // 若要铺满全屏，则按照各自比例缩放
      if (this.fullScreen) {
        box.style.transform = `scale(${widthScale},${heightScale})`
        return false
      }

      // 按照宽高最小比例进行缩放
      const scale = Math.min(widthScale, heightScale)
      this.setScale(scale)
    },

    setScale(scale) {
      console.log('🚀 ~ autoScale ~ scale:', scale)
      // const box = this.$refs['v-box']
      // const domWidth = el.value.clientWidth
      // const domHeight = el.value.clientHeight
    }
  }
}
</script>

<style scope></style>

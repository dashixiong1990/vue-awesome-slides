<template lang="pug">
  .slide-item(:style="styleObj" :class='{"on-stage": isOnStage}')
    slot(name='slide' :slide='slide' :index='index')
</template>

<script>
export default {
  name: 'Slide',
  props: ['index', 'slide'],
  data () {
    return {
      opacity: '',
      translate: '',
      isOnStage: this.index === 0,
      transformWithAnimate: false
    }
  },
  computed: {
    styleObj () {
      const mode = this.$parent.config.mode
      const modeMap = {
        'horizontal': 'X',
        'vertical': 'Y',
        'fade': 'X'
      }
      let styleObj
      if (mode === 'fade') {
        styleObj = {
          opacity: this.opacity,
          transition: `opacity ${this.$parent.config.speed}ms`
        }
      } else {
        const transform = `translate${modeMap[mode]}(${this.translate})`
        const transition = `transform ${this.$parent.config.speed}ms`
        if (this.transformWithAnimate) {
          styleObj = {
            transform,
            transition
          }
        } else {
          styleObj = {
            transform
          }
        }
      }

      return styleObj
    }
  },
  methods: {
    paintFrame (frame) {
      return new Promise((resolve) => {
        window.requestAnimationFrame(() => {
          frame()
          window.requestAnimationFrame(() => {
            resolve()
          })
        })
      })
    },
    reachBehindScene () {
      const { mode } = this.$parent.config
      if (mode === 'fade') {
        return
      }
      const multiplier = this.$parent.slideDirection === 'clockwise' ? 1 : -1
      this.transformWithAnimate = false
      return this.paintFrame(()=>{this.transform(multiplier)}) 
    },
    debut () {
      const mode = this.$parent.config.mode
      this.transformWithAnimate = true
      this.isOnStage = true

      if (mode === 'fade') {
        this.opacity = 1
      } else {
        this.transform(0)
      }
    },
    exit () {
      const mode = this.$parent.config.mode
      this.transformWithAnimate = true
      this.isOnStage = false

      if (mode === 'fade') {
        this.opacity = 0
      } else {
        const multiplier = this.$parent.slideDirection === 'clockwise' ? -1 : 1
        this.transform(multiplier)
      }
    },
    transform (multiplier) {
      const mode = this.$parent.config.mode
      const modeMap = {
        'horizontal': 'width',
        'vertical': 'height'
      }
      this.translate = `${this.$parent.config[modeMap[mode]] * multiplier}px`
    }
  }
}
</script>

<style>
  .slide-item{
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
  }
</style>
